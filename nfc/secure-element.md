# Secure elements

A **secure element (SE)** is a tamper-resistant chip with its own CPU, memory, and operating system, whose job is to hold secrets and perform cryptography **in a way that survives an attacker who physically owns the device**.

That last clause is the whole point. Normal software security assumes the attacker is remote and the hardware is yours. A secure element assumes the opposite: the attacker is holding the chip, has a lab, and is willing to grind the packaging off. Every design decision follows from that.

The smart cards in [ISO 7816](iso-7816.md) and [ISO 14443](iso-14443.md) were the original secure elements. A modern phone contains the same idea reduced to a few square millimeters and soldered to a board.

## The problem it solves

Consider the concrete task: **store a payment key on a phone.**

The phone runs a general-purpose OS with millions of lines of code, a browser, arbitrary apps, root exploits, and an owner who may want to cheat. Any key in normal storage is recoverable - by malware, by a rooted shell, by pulling the flash chip and reading it. Encrypting it does not help, because the key that decrypts it has to live somewhere too. This regresses forever until something breaks the chain.

A secure element breaks it with one architectural move:

```
   WITHOUT an SE - the key must exist in the CPU to be used
   ┌──────────────────────────────────────────────┐
   │  Application OS                              │
   │    key ──> crypto library ──> result         │
   │     ^                                        │
   │     └── in RAM, in a debugger, in a core     │
   │         dump, in swap. Recoverable.          │
   └──────────────────────────────────────────────┘

   WITH an SE - the key never crosses the boundary
   ┌──────────────────────────┐   ┌───────────────────────────┐
   │  Application OS          │   │  Secure element           │
   │                          │   │                           │
   │  "sign this challenge"   │──>│  key ──> crypto engine    │
   │                          │   │            │              │
   │  <── signature ──────────│<──│────────────┘              │
   │                          │   │                           │
   │  never sees the key      │   │  key is generated here,   │
   │                          │   │  lives here, dies here    │
   └──────────────────────────┘   └───────────────────────────┘
```

This is the **oracle model**, and it is the single most important concept. You do not ask the secure element for the key. You send it data and ask it to *perform an operation*, and it returns the result. The key is created inside the chip, is never exported, and physically cannot be read out through any interface the chip exposes.

Three properties fall out of this:

- **Compromising the host OS does not yield the key.** Malware with full root can ask the SE to sign things while the phone is in its hands, which is bad, but it cannot extract the key and use it later, elsewhere, at scale. The blast radius is one device for as long as the attacker holds it.
- **The SE can enforce policy the host cannot be trusted with.** "This key requires the user's fingerprint", "this counter only increments", "after 10 wrong PINs, erase". Because enforcement lives behind the boundary, a hostile host can only ask and be refused. This is the same argument as [smart card access conditions](iso-7816.md#part-4-level-3-security), for the same reason.
- **You get a hardware-anchored identity.** Since a key provably lives in a specific chip, a remote party can distinguish a real device from software pretending to be one. That is *attestation*, covered below.

## What "tamper resistant" actually means

This phrase gets used loosely. In a certified secure element it means a specific, expensive list of countermeasures against a specific catalogue of physical attacks.

The attacks:

- **Invasive** - decapsulate the chip with acid, then microprobe the internal buses directly, or edit the circuit with a focused ion beam to disable a check.
- **Semi-invasive** - expose the die and inject faults with a laser to skip an instruction, hoping a signature verification branches the wrong way.
- **Non-invasive side channels** - measure power consumption or electromagnetic emission during a crypto operation and statistically recover the key. **Differential power analysis** can extract an AES key from an unprotected implementation in minutes.
- **Fault injection** - glitch the supply voltage or clock at a precise moment to corrupt a computation, then derive the key from the faulty output. RSA-CRT signatures are famously vulnerable to this with a single fault.

The countermeasures, which is where the cost goes:

- An **active shield**: a serpentine metal mesh over the whole die, continuously monitored. Cut or probe through it and the chip detects the break and halts.
- **Environmental sensors** for voltage, clock frequency, temperature, and light, each with an operating window. Outside the window, the chip stops rather than compute unreliably.
- **Encrypted and scrambled memory**, with the bus between CPU and NVM encrypted under a per-chip key, so probing the bus yields noise and two identical chips have different bit patterns for the same data.
- **Constant-time, randomized crypto implementations**, with dummy operations, random execution order, and masking so power traces do not correlate with key bits.
- **Redundant computation**: do it twice or verify the result, so a single injected fault is detected rather than emitted.
- **No debug interface.** JTAG is physically blown at manufacture. The chip layout uses "glue logic", deliberately irregular, so an attacker cannot find the crypto block by looking at it.

Whether a given chip really does all this is what **certification** answers. **Common Criteria** at EAL5+ or EAL6+ against a hardware protection profile means an accredited lab spent months attacking it with exactly these techniques. **FIPS 140-2/140-3** level 3 and 4 are the US equivalent, and EMVCo runs its own scheme for payment. These certifications are the only practical way to tell a real secure element from a chip that merely claims to be one, and they are why secure elements are not simply a cheap feature to add.

Note what tamper resistance does *not* mean: **it is not tamper proof.** The goal is to make extraction cost more time, money, and expertise than the key is worth, and to make the attack non-scalable - a lab attack on one chip does not give you the keys in the other million.

## The landscape: which "secure" thing is which

This taxonomy is where most confusion lives, because marketing uses all these terms interchangeably and they have genuinely different threat models.

```
   ┌─────────────────────────────────────────────────────────────┐
   │  MAIN SoC                                                    │
   │  ┌──────────────────────┬──────────────────────────────┐    │
   │  │  Normal world        │  Secure world (TEE)          │    │
   │  │  Android / iOS       │  TrustZone: same CPU cores,  │    │
   │  │  apps, browser       │  isolated by the MMU and a   │    │
   │  │                      │  bit in hardware             │    │
   │  └──────────────────────┴──────────────────────────────┘    │
   │  ┌───────────────────────────┐                              │
   │  │ iSE - integrated SE       │  separate logic, same die    │
   │  └───────────────────────────┘                              │
   └──────────────┬──────────────────────────────────────────────┘
                  │  SPI / I2C
      ┌───────────┴─────────────┬─────────────────────┐
   ┌──┴──────────┐   ┌──────────┴──────┐   ┌──────────┴────────┐
   │  eSE        │   │  UICC / eSIM    │   │  NFC controller   │
   │  soldered,  │   │  removable or   │   │  (CLF) - the      │
   │  separate   │   │  soldered SE,   │   │  radio itself     │
   │  die,       │   │  carrier-owned  │   │                   │
   │  certified  │   └─────────────────┘   └───────────────────┘
   └─────────────┘
```

| Thing | Isolation | Resists physical attack | Who controls it |
|---|---|---|---|
| **Smart card** | separate chip, own package | yes, certified | the card issuer |
| **eSE** (embedded SE) | separate die, soldered | yes, certified | OEM |
| **UICC / SIM / eSIM** | separate die, socketed or soldered | yes, certified | mobile carrier |
| **iSE** (integrated SE) | isolated block on the main die | mostly, often certified | OEM |
| **TEE** (TrustZone) | same CPU, MMU-enforced | **no** | OEM |
| **Secure Enclave / Titan M** | separate core or die, own crypto | yes | OEM |
| **TPM** | separate chip (or firmware TPM) | yes, if discrete | platform owner |
| **HSM** | rack appliance, active zeroization | yes, FIPS L3/L4 | the server operator |

The distinction that matters most in practice:

**A TEE is not a secure element** ([covered in depth here](tee-and-strongbox.md)). TrustZone splits one CPU into a normal world and a secure world, isolated by hardware permission bits and the MMU. That is genuinely useful and defeats software attacks from the normal world, and it is *fast* with access to plenty of RAM - so it is the right place for fingerprint matching, DRM, and biometric pipelines. But it runs on the same silicon, with the same power supply and the same package, so **every physical attack listed earlier works exactly as well against a TEE as against the main CPU**. TEE operating systems have also had a long run of serious software vulnerabilities, because a secure world with a large attack surface is still a large attack surface. Use a TEE for isolating processing; use a secure element for holding keys.

Two clarifications about Apple, since the names collide constantly: the **Secure Enclave** is a separate coprocessor handling device keys, biometrics, and passcode anti-hammering. It is *not* the chip used for Apple Pay contactless - that is a distinct **embedded secure element** speaking [ISO 14443](iso-14443.md) to the outside world. A phone typically contains several of these things at once.

## Inside a secure element

A secure element is a whole computer, just a deliberately small and paranoid one:

```
   ┌────────────────────────────────────────────────────────┐
   │  Applets:   payment  │  transit  │  ID  │  FIDO key    │
   ├────────────────────────────────────────────────────────┤
   │  Java Card VM + GlobalPlatform card manager            │
   ├────────────────────────────────────────────────────────┤
   │  Card OS (e.g. NXP JCOP, Infineon, Thales)             │
   ├────────────────────────────────────────────────────────┤
   │  Secure CPU   │ Crypto coprocessor │ RNG (true, not    │
   │  16/32-bit    │ AES, 3DES, RSA, EC │ pseudo)           │
   ├───────────────┴────────────────────┴───────────────────┤
   │  ROM  │  RAM (a few KB)  │  NVM: EEPROM or flash       │
   │       │                  │  (tens to hundreds of KB)   │
   ├────────────────────────────────────────────────────────┤
   │  Active shield · sensors · encrypted memory bus ·       │
   │  no JTAG · glue logic                                  │
   └────────────────────────────────────────────────────────┘
```

Resources are startling by modern standards: **kilobytes of RAM**, low hundreds of kilobytes of non-volatile storage, and a clock in the tens of megahertz. A dedicated crypto coprocessor does the heavy lifting because the CPU alone would be far too slow for RSA. A true hardware random number generator is a required component, since almost every protocol depends on unpredictable nonces and a software PRNG on a deterministic chip has no entropy to draw on.

**Storage is non-volatile and object-oriented.** In Java Card, an object you allocate lives in EEPROM and persists across power loss by default; you have to explicitly ask for a transient array if you want it in RAM. This inverts normal programming intuition, and it is a direct consequence of the [contactless power model](iso-14443.md#the-mental-model-a-wireless-power-socket-that-also-carries-data) - a card can lose power mid-operation at any instant, so the platform provides a transaction API (`beginTransaction` / `commitTransaction`) and expects you to wrap any multi-step state change in it.

### The software stack: Java Card and GlobalPlatform

Two standards do the work here, and they divide cleanly:

- **Java Card** defines *what an applet is*: a heavily reduced Java - no threads, no garbage collection in older versions, no floating point, tiny heap - whose entry point is a `process(APDU)` method. The applet is fundamentally a [7816 APDU](iso-7816.md#part-4-level-1-the-apdu) handler.
- **GlobalPlatform** defines *how applets get on and off the card, and who is allowed to put them there*.

GlobalPlatform's model is a hierarchy of **security domains**, each a keyed authority over a region of the card:

```
   ┌───────────────────────────────────────────────────────┐
   │  ISD - Issuer Security Domain                          │
   │  the card owner's root authority, holds the card keys  │
   │      │                                                 │
   │      ├── SSD "Bank A"  ── applet: Bank A payment       │
   │      │      own keys, can load and delete only its own │
   │      │                                                 │
   │      ├── SSD "Transit" ── applet: transit purse        │
   │      │                                                 │
   │      └── applet: FIDO authenticator                    │
   └───────────────────────────────────────────────────────┘
```

Loading an applet requires authenticating to a security domain over a **Secure Channel Protocol** - SCP02 (3DES), SCP03 (AES), or SCP11 (elliptic curve) - and then issuing `INSTALL [for load]`, `LOAD` (the compiled applet, chunked across many APDUs), and `INSTALL [for install]`. Without the domain's keys you cannot put code on the card, which is what stops an attacker from simply installing their own applet to read someone else's.

Cards and applets both have **irreversible lifecycle states**. A card moves `OP_READY → INITIALIZED → SECURED` and can be pushed to `TERMINATED`, which is permanent and bricks it deliberately. An applet goes `INSTALLED → SELECTABLE → PERSONALIZED → LOCKED`. The one-way nature is the point: personalization can close doors that nothing can reopen, so a card in the field cannot be rolled back to a state where its factory keys still work.

**The practical consequence of all this: you usually cannot put an applet on your phone's eSE.** The ISD keys belong to the OEM, not you. Deploying a real applet means a commercial relationship with the OEM or a **Trusted Service Manager**, which is precisely the friction that pushed the industry toward host card emulation.

## Secure elements and NFC in a phone

This is where the SE meets the rest of this directory. When a phone taps a reader, something has to answer the reader's `SELECT` command, and there are three possible somethings.

```
                         ┌────────────────────────┐
                         │   Reader / terminal    │
                         └───────────┬────────────┘
                            ISO 14443 │ 13.56 MHz
                         ┌───────────┴────────────┐
                         │   NFC controller (CLF) │
                         │   ┌──────────────────┐ │
                         │   │ AID routing table│ │  <- decides who
                         │   └──────────────────┘ │     answers
                         └──┬──────────┬───────┬──┘
                    SPI     │    SWP   │       │  NCI
              ┌─────────────┘    ┌─────┘       └──────────┐
        ┌─────┴─────┐      ┌─────┴──────┐        ┌────────┴────────┐
        │   eSE     │      │  UICC/SIM  │        │  Host CPU: app  │
        │ applet    │      │  applet    │        │  OS + HCE app   │
        └───────────┘      └────────────┘        └─────────────────┘
          works with          carrier-           needs the OS awake
          screen off          controlled         and the app running
```

The **NFC controller** (also called the contactless frontend, CLF) owns the antenna and holds a **routing table**. When a reader selects an AID, the controller looks it up and forwards the APDU to whichever destination claims it. Routing can also be by protocol or by RF technology.

The three destinations, and their real tradeoffs:

- **eSE** - an applet on the embedded secure element. The controller and eSE can operate on **harvested field power with the phone switched off or battery dead**, because the path never involves the main CPU. This is why Apple Pay Express Transit works on a dead phone. It is also the fastest path, which matters for transit gates with hard latency budgets.
- **UICC** - an applet on the SIM, reached over the **Single Wire Protocol**. Technically equivalent to the eSE but **controlled by the mobile carrier**, which made it commercially contentious and largely killed it outside a few operator-led deployments.
- **The host CPU** - **host card emulation (HCE)**, introduced in Android 4.4. The APDU is delivered to a normal app over the NFC controller interface, and ordinary application code answers it. No secure element involved at all.

### HCE versus SE

HCE removed the OEM and carrier from the deployment path: any developer can register an AID and emulate a card. The obvious problem is that the keys now live in normal app storage on a device that may be rooted.

The industry answer is **tokenization**, and it is a genuinely good piece of design rather than a workaround:

- The app never holds the real card number (the **FPAN**) or a long-lived key. It holds a **device-specific token** (a **DPAN**) plus a **limited-use key** that is valid for a small number of transactions or a short window, refreshed from the cloud.
- Compromise therefore yields a key that is nearly worthless: bounded in count, bounded in time, and traceable to one device, which the issuer can deactivate without touching the underlying card.

This reframes the whole question. A secure element tries to make the key **unextractable**. Tokenization accepts that the key is extractable and makes it **not worth extracting**. Both are valid; they trade hardware cost against cloud infrastructure and online connectivity.

Which is why the two ecosystems diverged: Apple built eSE-based Apple Pay with full control of the hardware, while Google shipped HCE plus tokenization to work across an OEM landscape it did not control. Modern Android hedges, using StrongBox or Titan M for key storage underneath an HCE app - a hybrid where the *transport* is HCE but the key material sits behind a hardware boundary.

## Attestation: proving a key really is in hardware

An SE is only useful to a remote party if that party can tell the difference between a real secure element and software claiming to be one. **Attestation** is the mechanism.

At manufacture, the chip is injected with a private key and a certificate chaining to the vendor's root. When the SE generates a new key later, it can produce a signed statement: *"key X was generated inside me, is non-exportable, and requires user biometric authentication to use"*, signed by that factory key. A server verifies the chain and now knows the key's properties as a hardware fact rather than a client's claim.

This is what backs Android Key Attestation, Apple's App Attest, TPM endorsement keys, and FIDO2 authenticator attestation. It is also what makes SEs load-bearing for things far beyond payment: passkeys, device identity in zero-trust networks, and hardware-bound session tokens all depend on it.

The privacy tension is real and worth knowing: a per-chip attestation key is a perfect unique identifier. Schemes therefore use batch keys shared across many devices, or anonymous credential systems like Direct Anonymous Attestation, deliberately trading some precision for unlinkability.

## Provisioning: the genuinely hard part

Cryptography is the easy half. The hard half is **getting the first secret into the chip without anyone in the supply chain learning it.**

```
   1. Wafer fab      chip gets a unique ID and a manufacturer key,
                     injected inside the fab under HSM control

   2. Card OS load   OS and keys installed in a secured facility,
                     transport keys used so nothing useful travels
                     in the clear

   3. Personalize    issuer authenticates with GP keys, loads its
                     applet and per-device diversified keys

   4. Field          user provisions a credential; keys derived
                     server-side in an HSM, delivered over a
                     secure channel, never existing in plaintext
                     outside either endpoint

   5. End of life    TERMINATE the card, or delete the applet and
                     its keys
```

Every step needs an HSM on the other end - which is why HSMs and secure elements are two halves of one system, the server-side and device-side ends of the same trust chain. Key ceremonies, split-knowledge custody, dual control, and audited logs exist because this pipeline, not the chip, is where real systems get compromised. The [DESFire deployment advice](common-protocols.md#deploying-desfire-without-undermining-it) about per-card diversified keys is exactly this problem at a smaller scale.

## Practical notes

- **An SE protects keys, not logic.** Malware on a rooted phone can ask the SE to sign whatever it likes while it has access. The SE bounds *persistence and scale*, not live misuse. Bind operations to user presence (biometric, button press) if that matters, and let the SE enforce that binding.
- **Budget for latency and size.** Kilobytes of RAM and a slow CPU are real constraints. RSA-2048 keygen on-card takes seconds. Transit gates that must complete in ~300 ms are the reason the eSE path bypasses the host CPU entirely.
- **The oracle boundary is where your design lives.** Decide deliberately what crosses it. Sending a full document to an SE to sign is usually wrong; hash on the host and send the digest. But then you must ask what the SE is really attesting to, since it cannot see what it signed.
- **"Secure" in a datasheet means nothing without a certification and a target.** Ask which Common Criteria level, against which protection profile, and by which lab. A chip marketed as a "secure MCU" with no certification is not in the same category as a CC EAL6+ secure element.
- **You probably cannot deploy to the eSE.** Plan for HCE plus hardware-backed keystore, or for a secure element you control - a card or a USB token - rather than assuming access to the phone's.
- **Anti-hammering must be in the SE.** A 6-digit PIN has a million possibilities, so its entire security rests on a retry counter that survives power loss and cannot be reset by the host. Putting that counter anywhere else makes the PIN decorative.

## Where you meet secure elements

Payment cards and phone wallets; SIM and eSIM (an eUICC is a secure element that holds downloadable carrier profiles); passports and national ID cards; FIDO2 and U2F security keys; TPMs doing measured boot and disk-encryption key release; hardware cryptocurrency wallets; DRM key storage in set-top boxes; and the [DESFire](common-protocols.md#mifare-desfire) and Java Card products that are secure elements in a card-shaped package.

## Further reading

- [ISO 7816](iso-7816.md) - the APDU grammar every SE applet speaks
- [ISO 14443](iso-14443.md) - how a contactless SE reaches the outside world
- [Common protocols and card products](common-protocols.md) - concrete SE-based products
- [TEE and StrongBox](tee-and-strongbox.md) - the cheaper isolation tier, why it is weaker, and how Android exposes both
- GlobalPlatform Card Specification - the definitive account of security domains and applet lifecycle
- Java Card Platform specifications - the applet programming model and its transaction API
- GSMA SGP.22 - eSIM remote provisioning, a well-documented real example of the provisioning problem solved end to end
