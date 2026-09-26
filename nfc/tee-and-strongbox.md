# TEE and StrongBox

[Secure elements](secure-element.md) protect keys extremely well but are small, slow, expensive, and usually closed to third-party code. That leaves a gap: plenty of work needs isolation from a compromised OS but also needs **speed, megabytes of RAM, and access to peripherals** - fingerprint matching, video decryption, biometric pipelines. A separate certified chip cannot do those things.

A **Trusted Execution Environment (TEE)** fills that gap by partitioning the *main* CPU into two isolated worlds. **StrongBox** is Android's answer to the resulting weakness: for the keys that really matter, route them back to a genuine secure element.

Understanding both together is the point of this file, because the interesting question is never "is this secure" but **"which of these three tiers is this key in, and what does that tier actually resist?"**

## TEE: the mental model

The idea is to take one physical CPU and make it behave like two computers that do not trust each other equally:

- a **normal world** running Android or Linux, with all its apps and all its bugs, and
- a **secure world** running a small dedicated OS and a handful of *trusted applications*, invisible and inaccessible to the normal world.

The normal world cannot read secure-world memory, cannot inspect its registers, and cannot attach a debugger to it. It can only make a formal request across a controlled doorway and receive a result. That asymmetry is enforced by hardware, not by the kernel, so **rooting Android does not get you into the secure world**.

Crucially the relationship is one-way: the secure world can read normal-world memory freely, which is how it receives buffers of work. The normal world cannot look back. It is the same structural idea as the [oracle model](secure-element.md#the-problem-it-solves), except the boundary is a permission bit on a bus rather than a separate package.

## How ARM TrustZone actually works

TrustZone is the mechanism on essentially every phone. It is not a coprocessor; it is a **security state** threaded through the entire SoC.

```
        NORMAL WORLD                    │        SECURE WORLD
  ──────────────────────────────────────┼──────────────────────────────
   EL0  Android apps                    │  S-EL0  Trusted Applications
                                        │         (keymaster, DRM,
   EL1  Linux kernel                    │          biometrics)
                                        │
   EL2  hypervisor                      │  S-EL1  TEE OS kernel
                                        │         (Trusty, QTEE,
                                        │          Kinibi, OP-TEE)
  ──────────────────────────────────────┴──────────────────────────────
   EL3   Secure Monitor  <- the only doorway. Entered by the SMC
                            instruction. Saves one world's state,
                            restores the other's.
  ─────────────────────────────────────────────────────────────────────
   Hardware enforcement:
     NS bit      every bus transaction is tagged secure / non-secure
     TZASC       partitions DRAM into secure and non-secure regions
     TZPC        assigns peripherals to one world (e.g. the
                 fingerprint sensor belongs to the secure world)
     cache tags  cache lines carry the NS bit, so no flush is needed
                 when switching worlds
     GIC         interrupts can be routed to the secure world only
```

The key primitive is the **NS bit**. Every read and write travelling across the SoC interconnect carries a bit saying which world issued it, and memory controllers and peripheral gates check it. A normal-world access to a secure DRAM region is not a permissions error handled by software - it simply fails in hardware.

That is also why TrustZone can hand an entire peripheral to the secure world. If the fingerprint sensor's bus is marked secure-only, the Android kernel *cannot* read the sensor at all, no matter how compromised it is. The raw biometric data goes straight to a trusted application, and the normal world only ever learns "match" or "no match". The same trick gives you *secure display* and *secure touch* for PIN entry that malware cannot screenshot or synthesize.

Switching worlds costs a `SMC` instruction and a context save/restore in EL3 - microseconds, not milliseconds. Cheap enough to call per video frame, which is what makes DRM viable here and not on a smart card.

### What actually runs in there

- **Keymaster / KeyMint** - the Android Keystore backend, discussed below.
- **Biometric matching** - template storage and comparison, with the sensor bound to the secure world.
- **DRM** - Widevine L1 requires the decrypt and decode path to never expose plaintext video to the normal world.
- **Gatekeeper / Weaver** - lock-screen credential verification with hardware-backed rate limiting.
- **Verified boot** - checking signatures on the next stage as the device boots.
- **Payment and attestation** - device integrity signals, secure PIN entry.

The standard interfaces come from GlobalPlatform: the **TEE Client API** for normal-world callers and the **TEE Internal Core API** for writing trusted applications, each identified by a UUID. In practice the TEE OS is vendor-specific (Qualcomm QTEE, Trustonic Kinibi, Samsung TEEgris, Google Trusty, or the open-source OP-TEE), and **loading your own trusted application requires the OEM's signing key** - the same gatekeeping problem as [installing an applet on an eSE](secure-element.md#the-software-stack-java-card-and-globalplatform).

### The chain of trust

A TEE's guarantees rest entirely on the secure world being the code the vendor intended. That is established at boot:

```
   Boot ROM            immutable silicon, holds the root public key hash
        │  verifies signature
        v
   Bootloader stages   each verifies the next before handing off
        │
        ├──> TEE OS image (verified, launched into the secure world)
        │
        v
   Android kernel      launched into the normal world, its verity
                       state recorded in the TEE
```

Two consequences worth holding onto. First, **the root of trust is a hash burned into silicon**, so a compromised bootloader signing key is catastrophic and unpatchable in the field. Second, the TEE records what it measured about the normal world, which is what lets [attestation](#attestation-and-what-it-really-proves) later report "verified boot was green" as a fact rather than a claim from a kernel that may be lying.

### Where a TEE stores things

A TEE has **no non-volatile storage of its own** - it shares the device's flash with Android. So it solves persistence cryptographically:

- A **Hardware Unique Key (HUK)** is fused into the SoC at manufacture, readable only by the secure world. Storage keys derive from it, so secure-world data is encrypted into ordinary files that the normal world can see but not decrypt.
- Encryption alone does not stop **rollback**: an attacker who copies the encrypted PIN-retry counter, lets you use three attempts, then restores the old file has reset the counter. The fix is **RPMB** (Replay Protected Memory Block), a small authenticated, counter-protected region of the eMMC/UFS chip keyed with a secret programmed once at manufacture. Writes are authenticated and a monotonic counter defeats replay.

This matters because it is a structural difference from a secure element, which simply has its own EEPROM inside the tamper boundary. A TEE's persistence is only as good as the HUK, the RPMB key provisioning, and the storage chip's firmware.

## Why a TEE is not a secure element

The [secure element file](secure-element.md#what-tamper-resistant-actually-means) states this bluntly; here is the substance.

**Same silicon means same physical fate.** Every attack in the secure element's threat catalogue - decapsulation, microprobing, laser fault injection, power analysis - works identically against the secure world, because it is the same die, package, power rail, and clock as the main CPU. A TEE has no active shield, no environmental sensors, and no dual-rail logic. It was never built to survive an attacker with a lab.

**Shared hardware creates channels that logical isolation cannot close:**

- **CLKSCREW** (USENIX Security 2017) is the canonical result. The normal world controls dynamic voltage and frequency scaling for power management - including for the cores running the secure world. By pushing frequency outside safe limits at a precise moment, researchers induced faults inside the secure world, extracted an AES key from Qualcomm's TEE, and loaded a self-signed trusted application. No memory boundary was ever violated. A shared *physical resource* was enough.
- **Cache side channels** (ARMageddon, TruSpy) recover secrets across the world boundary by timing shared caches, because cache tagging prevents *access*, not *contention*.
- **Confused deputy bugs** (Boomerang) exploit trusted applications that dereference a normal-world pointer without validating it, tricking the privileged secure world into reading or writing secure memory on the attacker's behalf.

**The trusted computing base is large.** A TEE OS plus its trusted applications is tens or hundreds of thousands of lines of C, with a DRM stack, a biometric stack, and vendor code of varying quality. All of it runs more privileged than the Android kernel. Memory-corruption bugs in trusted applications have repeatedly yielded full secure-world compromise and Widevine key extraction. Compare a secure element's certified, minimal, formally scrutinized OS evaluated at Common Criteria EAL5+ and up.

So the accurate summary: **a TEE is strong against software attacks from the normal world and weak against physical attacks and against bugs in its own large codebase.** That is a real and useful guarantee. It is just a different one from a secure element's.

## StrongBox

Android 9 introduced **StrongBox**: a Keystore implementation that must run on a genuine, physically separate secure element rather than in TrustZone. It is Android saying "for these keys, the TEE is not enough."

The Compatibility Definition Document requires a StrongBox implementation to have:

- its **own discrete CPU**, separate from the application processor,
- its **own RAM**, not shared with the main SoC,
- its **own secure storage with rollback protection**,
- a **true hardware random number generator**,
- **tamper-resistant packaging**, and
- **side-channel resistance**.

In other words, the CDD reimposes exactly the secure element properties a TEE lacks. On Pixel this is the **Titan M / Titan M2** chip (Titan M2 is RISC-V based and certified to Common Criteria with AVA_VAN.5, the highest vulnerability-assessment level); other OEMs ship an embedded secure element in the same role.

StrongBox also brings **Insider Attack Resistance**: firmware updates to the chip require the user's device passcode. So even a leaked OEM firmware signing key cannot silently push malicious firmware to a locked device. That is a deliberate defense against a coerced or compromised manufacturer, and a genuinely unusual property.

The cost is capability. StrongBox supports a deliberately narrow algorithm set - broadly RSA-2048, ECDSA on P-256, AES-128/256, and HMAC-SHA-256 - and it is **slow** with a **limited key count**. Anything outside that set, or any key needing throughput, belongs in the TEE.

## The developer-visible surface: Android Keystore

Keystore is where these tiers stop being architecture and become an API decision. The layering:

```
   ┌──────────────────────────────────────────────────────────┐
   │  Your app: KeyGenParameterSpec, Cipher, Signature        │
   │  (holds a key *alias*, never key material)               │
   ├──────────────────────────────────────────────────────────┤
   │  Android Keystore system service                         │
   ├──────────────────────────────────────────────────────────┤
   │  KeyMint HAL  (Keymaster before Android 12)              │
   ├────────────────────────────────┬─────────────────────────┤
   │  TEE implementation            │  StrongBox impl         │
   │  TRUSTED_ENVIRONMENT           │  STRONGBOX              │
   │  TrustZone secure world        │  Titan M2 / eSE         │
   └────────────────────────────────┴─────────────────────────┘
```

Three security levels exist, and the difference is *where the key material can ever exist in plaintext*:

```
   SOFTWARE             key is in the Android process / kernel memory.
                        Root or a memory dump recovers it.

   TRUSTED_ENVIRONMENT  key exists in plaintext only inside the secure
                        world. Stored on disk as a blob encrypted under
                        a HUK-derived key. Root does NOT recover it.
                        A physical or CLKSCREW-class attack might.

   STRONGBOX            key exists in plaintext only inside a separate
                        tamper-resistant chip with its own RAM and
                        storage. Root does not recover it; neither does
                        a lab attack on the main SoC.
```

Requesting StrongBox is one call, and the failure mode is explicit rather than silent:

```kotlin
val spec = KeyGenParameterSpec.Builder("my-key",
        KeyProperties.PURPOSE_SIGN)
    .setDigests(KeyProperties.DIGEST_SHA256)
    .setAlgorithmParameterSpec(ECGenParameterSpec("secp256r1"))
    .setIsStrongBoxBacked(true)              // demand a real SE
    .setUserAuthenticationRequired(true)     // enforced by hardware
    .setUserAuthenticationParameters(0,
        KeyProperties.AUTH_BIOMETRIC_STRONG)
    .setInvalidatedByBiometricEnrollment(true)
    .setAttestationChallenge(serverNonce)    // ask for attestation
    .build()

val kpg = KeyPairGenerator.getInstance("EC", "AndroidKeyStore")
try {
    kpg.initialize(spec)
} catch (e: StrongBoxUnavailableException) {
    // No StrongBox on this device. Decide deliberately: fall back to
    // TEE, or refuse the feature. Do not fall back silently.
}
val key = kpg.generateKeyPair()
```

Then verify what you actually got, rather than assuming:

```kotlin
val info = KeyFactory.getInstance(key.private.algorithm, "AndroidKeyStore")
    .getKeySpec(key.private, KeyInfo::class.java)
// SECURITY_LEVEL_STRONGBOX / _TRUSTED_ENVIRONMENT / _SOFTWARE
val level = info.securityLevel
```

The properties worth knowing, because they are enforced by hardware and therefore actually mean something:

- **`setUserAuthenticationRequired`** - the key is unusable until the user authenticates. The *TEE or StrongBox* checks this, so a rooted OS cannot bypass it. Pair with `setUserAuthenticationParameters` to choose biometric versus device credential and a validity window (a timeout of 0 means one authentication per use).
- **`setInvalidatedByBiometricEnrollment`** - if a new fingerprint is enrolled, destroy the key. Without this, an attacker who learns the PIN can enrol their own finger and inherit the key.
- **`setUnlockedDeviceRequired`** - no use while the screen is locked.
- **Rollback resistance** - guarantees a deleted key is really gone and cannot be restored from a backup of the key blob. Available on StrongBox, rarely on TEE, and worth requesting explicitly if your threat model includes it.

## Attestation and what it really proves

Ask for an attestation challenge at key generation and Keystore returns an **X.509 certificate chain** rooted in Google's hardware attestation root. A custom extension (OID `1.3.6.1.4.1.11129.2.1.17`) carries the claims:

```
   Google attestation root (public, pinned by your server)
        │
   Intermediate (OEM / batch)
        │
   Leaf certificate for your key
        │
        └─ attestation extension:
             attestationChallenge      your nonce, proves freshness
             attestationSecurityLevel  SOFTWARE / TEE / STRONGBOX
             verifiedBootState         GREEN / YELLOW / ORANGE / RED
             osVersion, patchLevel     what is actually running
             key authorizations        purposes, algorithms, whether
                                       user auth is required
```

Verify the chain to the pinned root **on your server**, check the challenge matches, and check the security level and boot state are what you require. Also check the chain against **Google's published attestation key revocation list**, because keys do get revoked when compromise is discovered.

What attestation proves and does not:

- It **does** prove a key with stated properties lives in hardware of a stated tier on a device whose boot state is as reported.
- It does **not** prove your app is unmodified, that the device is free of malware, or that the user is who they claim. A rooted device with an unlocked bootloader reports `ORANGE` and you can reject it, but a device with intact verified boot and a compromised app still attests happily.
- Its trust is only as good as **OEM key hygiene**, which has failed in practice. Attestation keys and platform signing keys have leaked from vendors, which is precisely why the revocation list exists and why pinning the root and checking revocation are not optional.

## Apple's equivalent

Apple does not expose a TEE to developers; it exposes the **Secure Enclave**, which sits architecturally where StrongBox does - a separate coprocessor with its own boot ROM, its own OS (an L4-derived microkernel), a true RNG, an AES engine inline with the flash controller, and a memory protection engine that encrypts, authenticates, and anti-replays its slice of DRAM.

Practical differences from Android:

- **P-256 elliptic curve only.** No RSA, no AES keys. You get ECDSA signing and ECIES key agreement via `kSecAttrTokenIDSecureEnclave` or CryptoKit's `SecureEnclave.P256`. If you need symmetric crypto, wrap a data key with a Secure Enclave key.
- **No tier choice.** There is no TEE-versus-StrongBox decision, so the fallback complexity disappears, along with the flexibility.
- **Access control via `SecAccessControl`** with `.biometryCurrentSet` (invalidated when biometric enrolment changes), `.userPresence`, or `.devicePasscode` - the direct analogues of the Keystore auth flags.
- **Passcode anti-hammering lives in the Enclave**, with escalating delays and optional erase after ten failures, which is the whole reason a 6-digit passcode is viable.
- Biometric templates never leave the Enclave, and the sensor is cryptographically paired to it, so a swapped sensor cannot inject a match.

## Choosing a tier

| | Software | TEE | StrongBox / SE |
|---|---|---|---|
| Key visible to rooted OS | **yes** | no | no |
| Survives physical/lab attack | no | **no** | yes |
| Resists CLKSCREW-class attacks | no | **no** | yes |
| Own RAM and storage | no | no (shares flash) | yes |
| Speed | fastest | fast | **slow** |
| Algorithm coverage | everything | wide | **narrow** |
| Suitable for per-frame work | yes | yes | no |
| Certified | no | rarely | CC EAL5+ typical |
| Third-party code allowed | yes | OEM key needed | effectively no |

Guidance:

- **Default to TEE** (`TRUSTED_ENVIRONMENT`) for hardware-backed keys. It is universally available, fast, and defeats the realistic attacker: malware and root, not a laboratory.
- **Use StrongBox** for long-lived, high-value, non-revocable secrets where a physical attack is plausible and the performance cost is irrelevant: device identity, passkeys, key-wrapping keys, payment key material.
- **Never fall back silently** from StrongBox to TEE. Catch `StrongBoxUnavailableException`, record which tier you got, and let the server decide whether that tier is acceptable for the operation.
- **Put bulk work in the TEE, roots of trust in StrongBox.** The idiomatic pattern: a StrongBox key wraps a TEE key that does the volume work.

## Practical notes

- **A TEE is not storage.** Key blobs live in the normal filesystem, encrypted. Wiping app data or a factory reset destroys them, so treat hardware keys as **device-bound and unbackupable** by design. Plan a re-enrolment path rather than trying to migrate them.
- **Auth-bound keys break on enrolment changes.** With `setInvalidatedByBiometricEnrollment`, adding a fingerprint permanently invalidates the key. This is correct behaviour and a support-ticket generator, so handle `KeyPermanentlyInvalidatedException` and re-enrol gracefully.
- **Hardware attestation is not app integrity.** They answer different questions. If you need app integrity, that is Play Integrity or App Attest, and it rests on different assumptions.
- **StrongBox availability is genuinely patchy.** Pixel 3 and later, recent Samsung flagships, and not much else reliably. Probe it, do not assume it.
- **Do not ship your own trusted application unless you are the OEM.** You will not get it signed. Design for Keystore and StrongBox as the interfaces you are actually given.
- **The tier is a claim until you verify it.** `KeyInfo.getSecurityLevel()` tells your app, and attestation tells your server. Only the second is trustworthy, because the first comes from a process an attacker may control.

## Further reading

- [Secure elements](secure-element.md) - the hardware tier StrongBox routes to, and why certification matters
- [ISO 7816](iso-7816.md) - the APDU grammar an eSE-backed StrongBox speaks internally
- ARM Security Technology / TrustZone documentation - the authoritative account of the NS bit and EL3
- GlobalPlatform TEE Internal Core API - the trusted application programming model
- Android CDD section 9.11 and the Android Keystore documentation - the normative StrongBox requirements and the attestation schema
- *CLKSCREW: Exposing the Perils of Security-Oblivious Energy Management* (USENIX Security 2017) - the clearest demonstration of why shared silicon limits what a TEE can promise
