# Common NFC protocols and card products

[ISO 14443](iso-14443.md) gives you a radio link and [ISO 7816](iso-7816.md) gives you a command grammar. Neither tells you what a *real* card does. That is decided by a product family or an industry profile layered on top, and this is where almost all practical confusion lives - "we use NFC" or "we use MIFARE" conveys almost nothing on its own.

This file maps the landscape and then goes deep on **MIFARE DESFire**, which is the default choice for new access-control and closed-loop payment systems.

## The landscape

```
  ┌───────────────────────────────────────────────────────────────┐
  │  EMV     ePassport   NDEF      DESFire    MIFARE    FeliCa    │
  │  payment ICAO 9303   messages  app        Classic   services  │
  ├──────────────────────┬─────────┴──────────┼──────────┬────────┤
  │   ISO 7816-4 APDUs   │  native cmds       │ Crypto1  │ FeliCa │
  │                      │  (APDU-wrapped)    │          │ cmds   │
  ├──────────────────────┴────────────────────┤          │        │
  │        ISO 14443-4 transport              │          │        │
  ├───────────────────────────────────────────┴──────────┤        │
  │        ISO 14443-3 anticollision (Type A / Type B)   │ JIS X  │
  ├──────────────────────────────────────────────────────┤ 6319-4 │
  │        ISO 14443-2  13.56 MHz                        │        │
  └──────────────────────────────────────────────────────┴────────┘
```

Note where MIFARE Classic sits: it uses standard 14443-3 anticollision and then leaves the standard entirely. That is the `SAK` bit 6 fork described in [ISO 14443](iso-14443.md#the-one-bit-that-matters-most).

| Family | Stack | Crypto | Verdict |
|---|---|---|---|
| MIFARE Classic | 14443-3 + proprietary | Crypto1 | **Broken. Do not use.** |
| MIFARE Ultralight / NTAG21x | 14443-3, NFC Type 2 | none or password | fine for public data only |
| MIFARE Plus | 14443-3 or -4 | AES | Classic migration path |
| **MIFARE DESFire EV2/EV3** | **14443-4** | **AES-128/192** | **the sensible default** |
| FeliCa | JIS X 6319-4 | DES / AES | Japan and Hong Kong transit |
| NTAG 424 DNA | 14443-4, NFC Type 4 | AES + SUN | tags needing authenticity |
| EMV contactless | 14443-4 + 7816-4 | RSA/ECC + 3DES/AES | payment, not yours to build |
| ePassport | 14443-4 + 7816-4 | PACE, EAC | government issued |
| 125 kHz prox | not NFC at all | **none** | **clonable in seconds** |

## MIFARE DESFire

NXP's **DESFire** is a proper ISO 14443-4 card: it runs a real file system, supports AES, and enforces per-file access control in hardware. The name comes from **DES** plus **fire**wall, the firewall being the isolation between applications on one card.

The key idea, and the reason it is worth understanding in detail: **DESFire moves the security decisions into the card's own file metadata.** You do not write application code that decides who may read what; you configure each file with which key is required for which operation, and the card enforces it against any terminal, hostile or not.

### Generations

This matters because the differences are security-critical, not just feature lists.

| Generation | Year | Notes |
|---|---|---|
| **D40** (original) | 2002 | 3DES only. **Broken** by a side-channel attack in 2011 that recovered the key in hours. Discontinued. Do not deploy. |
| **EV1** | 2009 | AES-128 added, random UID, 2/4/8 KB. Still cryptographically sound, widely deployed. |
| **EV2** | 2016 | **Proximity Check** (relay resistance), Transaction MAC files, unlimited applications, delegated application management, LRP side-channel-resistant mode. |
| **EV3** | 2020 | Secure Dynamic Messaging, transaction timer, random UID by default, higher certification (CC EAL5+). |
| **DESFire Light** | 2018 | Cheaper single-application variant, AES only, EV2-era security. |

If you are specifying hardware today, specify **EV2 or EV3**. The jump from EV1 is worth it mainly for **Proximity Check**, which is the only real defense against the relay attack that [ISO 14443 structurally permits](iso-14443.md#security-what-the-standard-does-and-does-not-give-you) - it times the card's responses at the RF level to bound its physical distance.

### The file system

DESFire is not a 7816 file tree. It has its own two-level structure:

```
   PICC level  ──  card master key, card-wide settings,
        │          random UID config, free-memory info
        │
        ├── Application  AID 3 bytes, e.g. 0x001122
        │        │        up to 14 keys of its own (key 0..13)
        │        │
        │        ├── File 0   Standard Data,  access: keys + comm mode
        │        ├── File 1   Value,          access: keys + comm mode
        │        └── File 2   Cyclic Record,  access: keys + comm mode
        │                     (up to 32 files, IDs 0x00-0x1F)
        │
        ├── Application  AID 0x334455   <- fully isolated from the above.
        │                                 Different keys, no cross access.
        └── Application  AID 0x667788
```

Two things to notice:

- **Applications are hard security boundaries.** Authenticating to application A gives you nothing in application B. This is what lets a single card carry a building badge, a canteen purse, and a library credential issued by three parties that do not trust each other. That is the "firewall".
- **Keys are per application**, numbered 0 to 13, with key 0 being the application master key. Key 0 at PICC level is the card master key.

### File types, and why the choice matters

This is the most useful part of DESFire to understand, because picking the right file type gets you correctness guarantees for free:

- **Standard Data** - a plain fixed-size byte array. Simple, non-transactional. A write is immediate and a power loss mid-write leaves you with torn data.
- **Backup Data** - same, but **transactional**. Writes go to a shadow copy and only become visible on `CommitTransaction`. A power loss rolls back cleanly.
- **Value** - a single signed integer with `Credit`, `Debit`, and `LimitedCredit` operations, plus configurable upper and lower limits. The card enforces the limits, so a hostile terminal cannot set a purse to an arbitrary balance - only shift it within the rules. Transactional.
- **Linear Record** - append-only fixed-size records, filling up and then refusing more writes. Transactional.
- **Cyclic Record** - same but wraps, overwriting the oldest. Transactional. Ideal for "the last 20 door events".
- **Transaction MAC** (EV2+) - not storage but an attestation: it accumulates a MAC over committed transactions, so a backend can later verify that an offline transaction genuinely happened on a real card.

**Use the transactional types.** Recall from [ISO 14443](iso-14443.md#common-gotchas) that the user's hand moves and the field dies instantly with no warning. A stored-value purse built on a Standard Data file *will* eventually corrupt; the same purse on a Value file cannot.

### Access rights: the core mechanism

Every file carries a 2-byte access rights field, read as four nibbles:

```
   ┌──────┬──────┬───────────┬────────────┐
   │ Read │Write │ Read&Write│ ChangeAccess│
   └──────┴──────┴───────────┴────────────┘
     each nibble holds:
       0x0 - 0xD   the key number required
       0xE         free access, no authentication needed
       0xF         never, denied to everyone including key 0

   Example: 0x1E2F
     Read            with key 1
     Write           free, no auth        <- e.g. a terminal can log
     Read&Write      with key 2
     ChangeAccess    never                <- rights are frozen forever
```

The `0xF` ("never") value is the one worth internalizing: **it lets you make a decision irreversible**. Setting `ChangeAccessRights` to `0xF` at personalization means no key, not even the master key, can ever loosen that file's protection again. That converts a policy into a hardware property.

### Communication modes

Orthogonally to *who* may access a file, each file declares *how* the data crosses the air:

- **Plain** - readable by anyone eavesdropping.
- **MACed** - cleartext, but integrity-protected against modification.
- **Fully Enciphered** - encrypted and integrity-protected.

This is per file, which is exactly right: the card's public identifier can be Plain while the purse balance is Fully Enciphered, paying the crypto cost only where it is needed. **A very common deployment mistake is leaving files in Plain mode**, which gives you DESFire's access control but none of its confidentiality, and readers happily work either way so nothing looks wrong.

### Authentication

A three-pass mutual challenge-response that also derives session keys:

```
   Terminal                                      Card
      │  Authenticate(key number)  ───────────>   │
      │                                            │ picks random RndB
      │  <─────────────  E(key, RndB)             │
      │                                            │
      │ decrypts RndB, picks RndA                  │
      │  E(key, RndA || RndB<<1)   ───────────>   │ checks RndB came back
      │                                            │ -> terminal knows key
      │  <─────────────  E(key, RndA<<1)          │
      │ checks RndA came back                      │
      │ -> card knows the key                      │
      │                                            │
      └── both sides now derive a session key from RndA and RndB ──┘
```

Both directions are proved, so the terminal also detects a cloned or emulated card. The session key means every subsequent MACed or Enciphered exchange is bound to this session and cannot be replayed into another one.

EV2 adds `AuthenticateEV2First` / `AuthenticateEV2NonFirst`, which maintain a command counter across the session so that individual commands cannot be reordered or dropped, not just replayed.

### Talking to a DESFire card

DESFire has a **native** command set that predates its ISO compliance, and most stacks send it wrapped inside an APDU:

```
   Native command, APDU-wrapped:
     90 <cmd> 00 00 <Lc> <data...> 00
     │  │              │
     │  │              └─ payload length
     │  └─ DESFire command code, e.g. 0x5A = SelectApplication
     └─ CLA = 0x90, "proprietary"   <- the tell-tale sign

   Response:
     <data...> 91 <status>
                  │
                  └─ 0x00 = success
                     0xAF = "additional frame", call again with 0xAF
                            to get the next chunk
```

The `91 AF` chunking is worth knowing: DESFire replies larger than one frame require you to loop, re-issuing command `0xAF` until you get `91 00`. Forgetting this is a classic first-integration bug.

Commands you will actually use:

| Code | Command | Purpose |
|---|---|---|
| `0x60` | `GetVersion` | hardware/software generation and UID. Returns 3 frames. |
| `0x6A` | `GetApplicationIDs` | list AIDs on the card |
| `0x5A` | `SelectApplication` | enter an application |
| `0xCA` | `CreateApplication` | personalization |
| `0xCD` | `CreateStdDataFile` | and siblings for the other file types |
| `0xAA` | `AuthenticateAES` | the handshake above |
| `0xBD` | `ReadData` | read a data file |
| `0x3D` | `WriteData` | write a data file |
| `0x6C` | `GetValue` | read a Value file |
| `0x0C` / `0xDC` | `Credit` / `Debit` | change a Value file |
| `0xC7` | `CommitTransaction` | make staged writes real |
| `0xA7` | `AbortTransaction` | discard them |
| `0xC4` | `ChangeKey` | key rotation |
| `0x51` | `GetCardUID` | the real UID when random UID is on |

Status bytes you will hit: `0x9D` permission denied, `0xAE` authentication error, `0xA0` application not found, `0xF0` file not found, `0x1E` integrity error, `0xBE` boundary error (read past end of file).

### Deploying DESFire without undermining it

- **Change the default keys.** A factory card has an all-zeros master key. A surprising number of real deployments never change it, which reduces the whole card to a memory chip.
- **Never use a single global key.** Derive a **per-card key** from the UID and a backend master secret (diversification). Otherwise one extracted key compromises every card you have ever issued - which is exactly how the legacy HID iCLASS ecosystem was broken.
- **Set communication mode to Fully Enciphered** for anything sensitive. Access control alone does not stop eavesdropping.
- **Use `0xF` to freeze** access rights and key-change rights after personalization wherever policy allows.
- **Turn on random UID** so the card cannot be tracked by passive readers, and fetch the real one with `GetCardUID` after authenticating when you need it.
- **Enable Proximity Check** (EV2+) if relay attacks are in your threat model, which for door access they are.
- **Keep the crypto on the card, not just on the reader.** A system that reads a plaintext ID off a DESFire file and trusts it has the security of MIFARE Classic while paying DESFire prices. This is depressingly common.

## The rest of the MIFARE family

**MIFARE Classic** (1K/4K) is 14443-3 plus NXP's proprietary Crypto1, with memory split into sectors of four blocks, each sector protected by key A and key B. Crypto1 has been comprehensively broken since 2008 - keys are recoverable in seconds with commodity hardware, and cards are cloneable including the "read-only" UID on magic cards. **Treat a MIFARE Classic deployment as having no security at all.** It survives only because of enormous installed-base inertia.

**MIFARE Ultralight and NTAG21x** are NFC Forum Type 2 tags: a small EEPROM, no real crypto, sometimes a 32-bit password that is sent in the clear and offers only token protection. Correct uses are public data - a URL on a poster, a product identifier. Incorrect use is anything the holder must not forge. Ultralight C adds 3DES authentication and is a genuine improvement.

**MIFARE Plus** exists to migrate Classic installations: it is pin-compatible with the Classic command set and sector layout but replaces Crypto1 with AES, and runs at "security levels" SL0 through SL3 so a site can move readers over gradually. SL3 is the only one worth reaching. If you are not migrating, skip it and use DESFire.

**NTAG 424 DNA** is a Type 4 tag with AES and **SUN** (Secure Unique NFC): each tap produces a URL containing a fresh counter and a CMAC, so a plain phone with no app can read a tag and a backend can verify the tap was genuine and not a copied URL. This is the right tool for anti-counterfeiting and authenticated tap-to-web.

## FeliCa

Sony's **FeliCa** (JIS X 6319-4, called **NFC-F**) is a wholly separate standard from ISO 14443, not a variant of it. It runs at 212/424 kbit/s, structures storage as *Services* within *Areas* addressed by service and area codes, and uses DES or AES mutual authentication. Two variants matter: FeliCa Standard (full crypto, used for Suica, PASMO, Edy, Octopus) and FeliCa Lite-S (cheap, limited authentication, and the basis of NFC Forum Type 3 tags).

Its practical significance is regional: FeliCa dominates Japanese and Hong Kong transit, so any reader intended for those markets must implement NFC-F as well as NFC-A and NFC-B.

## NFC Forum tag types and NDEF

The NFC Forum defines five **tag types**, which are interoperability profiles rather than new radio standards:

| Type | Built on | Typical product | Capacity |
|---|---|---|---|
| 1 | 14443A | Topaz | 96 - 454 B |
| 2 | 14443A | NTAG21x, Ultralight | 48 B - 888 B |
| 3 | NFC-F | FeliCa Lite-S | up to 1 MB |
| **4** | **14443A/B + 7816-4** | **DESFire, NTAG 424, Java Card** | **up to 32 KB+** |
| 5 | ISO 15693 | ICODE | up to 8 KB |

The common data format across all five is **NDEF** (NFC Data Exchange Format): a sequence of records, each carrying a type-name-format field, a type, and a payload. Standard record types cover URIs (with common prefixes compressed to one byte), text with a language code, MIME media, Smart Posters, and vendor-defined external types. This is what makes "tap a tag, open a URL" work identically across five unrelated silicon families.

Note that NDEF is a *container format with no security whatsoever*. An NDEF URL on a Type 2 tag can be rewritten by anyone with a phone unless the tag was locked, and even then the tag can simply be replaced with a different one. NTAG 424's SUN exists precisely to close that gap.

## EMV contactless

Payment cards are ISO 14443-4 plus ISO 7816-4 APDUs, but the transaction logic comes from the **EMV** specifications: Book D for the RF layer, and one of several *kernels* for the application flow, because Visa, Mastercard, Amex, and JCB each standardized their own.

The broad shape is: select the `PPSE` directory to discover which payment applications the card holds, select one by AID, `GET PROCESSING OPTIONS`, read records to obtain the card's certificates and data, then ask the card to generate a cryptogram (`ARQC`) over the transaction details, which the issuer verifies online. Offline data authentication (SDA/DDA/CDA) lets a terminal check the card's signature without connectivity.

Practically: **you do not implement this.** It is certification-gated, and the interesting parts are in the kernel specs, not the ISO documents. Its relevance here is that it is the highest-volume real user of the 14443 + 7816 stack, and its relay-resistance protocol is the industry's response to a weakness in the base standard.

## ePassports (ICAO Doc 9303)

An e-passport is an ISO 14443 chip holding a 7816-4 file tree: `EF.DG1` (the machine-readable zone data), `EF.DG2` (the facial image), further data groups for fingerprints and iris, and `EF.SOD`, a signed hash list over all of them.

The layered protections are a good case study in how much has to be bolted on top of ISO 14443:

- **PACE** (replacing the weaker **BAC**) establishes a secure channel from a password the holder physically possesses - the MRZ line or the printed card access number. This is what stops the passport being read while closed in a pocket.
- **Passive Authentication** verifies the country's signature over `EF.SOD`, proving the data is authentic.
- **Active** or **Chip Authentication** makes the chip prove it holds a private key, which detects a cloned chip that copied the data.
- **Terminal Authentication** (part of EAC) forces the *reader* to present a certificate before fingerprints are released.

## Legacy access control, for context

Most buildings do not run any of the above. They run **125 kHz proximity** cards - HID Prox, EM4100, Indala - which are not NFC at all, have no cryptography whatsoever, and broadcast a fixed number that a 30 dollar handheld device clones in seconds. **HID iCLASS legacy** (13.56 MHz) was the intended upgrade but used a global master key that leaked, so it is likewise cloneable.

This is worth knowing for two reasons. First, if you are auditing physical security, the badge is very often the weakest link in the building by an enormous margin. Second, when someone says "we already have contactless badges", that may mean a technology with strictly less security than a paper key.

## Picking one

- **Public, unauthenticated data** - a URL, a product code: NTAG21x (Type 2). Cheap, universally readable by phones.
- **Data that must be provably genuine but stays public** - anti-counterfeiting, authenticated tap-to-web: NTAG 424 DNA with SUN.
- **Access control, transit, closed-loop stored value** - DESFire EV2 or EV3, with per-card diversified keys, Fully Enciphered mode, transactional file types, and Proximity Check.
- **Anything replacing MIFARE Classic in place** - MIFARE Plus at SL3, or bite the bullet and move to DESFire.
- **Open-loop payment** - you do not build this; you accept EMV via a certified terminal.

The recurring lesson across all of these: the radio standard is never the security boundary. **Every one of these systems is only as strong as its key management**, and the most common failure is not broken cryptography but default keys, global keys, and plaintext identifiers trusted by the reader.

## Further reading

- [ISO 14443](iso-14443.md) - the radio and transport layers everything here sits on
- [ISO 7816](iso-7816.md) - the APDU and file-system grammar
- [Secure elements](secure-element.md) - the tamper-resistant hardware these credentials live in
- NXP AN12343 / AN10922 - DESFire EV3 features and AES key diversification, the two documents to read before a DESFire deployment
- NFC Forum Type 2 and Type 4 Tag Operation specifications - short and readable
