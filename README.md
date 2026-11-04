# DFP / Anti-Bot Red-Team Index

**One page for every vendor writeup in the device-intelligence / fingerprinting /
anti-bot red-teaming project.**

Each vendor section below links the repo for that vendor's full writeup,
scripts, and artifacts, and ends with the three things we're after: could we
**read the payload** in plaintext, could we **tamper with it** and have the
server accept the result, and could we **mint a token** client-side — plus how
hard each one was to crack.

> **Scope note.** All work was done on authorized own-stack / public-asset
> analysis; telemetry was observed or fulfilled locally (nothing forwarded to
> vendor servers beyond normal page loads); findings are stated honestly, never
> overstated. No customer/prod data was touched.

---

---

## At-a-glance (ranked by effort)

| # | Vendor | Crypto-RE | Resistance | **Effort** | evidence (scripts · phases · commits) |
|---:|---|:---:|---:|---:|---|
| 1 | [TikTok web](https://github.com/JWally/dfp-redteam-tiktok-web) | 9.5 | ~65 | **51.5** | 26 · 6 · 22 |
| 2 | [Stytch (Twilio)](https://github.com/JWally/dfp-redteam-stytch) | 9 | ~70 | **39.8** | 38 · 1 · 7 |
| 3 | "Pendulum" *(private)* | 7.5 | ~58 | **25.5** | 12 · 4 · 6 |
| 4 | [hCaptcha](https://github.com/JWally/dfp-redteam-hcaptcha) | n/a | ~85 | **25.2** | 20 · 2 · 5 |
| 5 | [Castle](https://github.com/JWally/dfp-redteam-castle) | 8 | ~63 | **24.5** | 18 · 2 · 10 |
| 6 | [Fingerprint Pro](https://github.com/JWally/dfp-redteam-fingerprint-pro) | n/a | ~80 | **22.8** | 13 · 3 · 7 |
| 7 | [HUMAN Security / PerimeterX](https://github.com/JWally/dfp-redteam-human-px) | 4 | ~65 | **19.5** | 14 · 2 · 6 |
| 8 | [Sift](https://github.com/JWally/dfp-redteam-sift) | n/a | ~55 | **17.2** | 8 · 3 · 5 |
| 9 | [Kasada (KPSDK: Twitch/Nike/FootLocker)](https://github.com/JWally/dfp-redteam-kasada) | 4 | ~50 | **14.0** | 9 · 2 · 4 |
| 10 | [reCAPTCHA v2](https://github.com/JWally/dfp-redteam-everydollar) | n/a | ~85 | **13.8** | 9 · 2 · 3 |
| 11 | [ClearSale](https://github.com/JWally/dfp-redteam-clearsale) | 1 | ~30 | **12.8** | 8 · 2 · 3 |
| 12 | [Kount (Equifax)](https://github.com/JWally/dfp-redteam-kount) | 0.5 | ~40 | **11.5** | 7 · 2 · 2 |
| 13 | [Forter](https://github.com/JWally/dfp-redteam-forter) | 7 | ~55 | **11.5** | 6 · 2 · 6 |
| 14 | [SEON (v6 JS SDK)](https://github.com/JWally/dfp-redteam-seon) | 7 | ~60 | **11.0** | 10 · 1 · 4 |
| 15 | ThreatMetrix (LexisNexis Risk Solutions) *(private)* | n/a | ~75 | **8.8** | 4 · 2 · 3 |
| 16 | [Kraken (Pro)](https://github.com/JWally/dfp-redteam-kraken) | 6 | ~70 | **8.5** | 3 · 2 · 6 |
| 17 | [NoFraud](https://github.com/JWally/dfp-redteam-nofraud) | 0.5 | ~35 | **8.5** | 4 · 2 · 2 |
| 18 | [Verisoul](https://github.com/JWally/dfp-redteam-verisoul) | 9.5 | ~72 | **8.2** | 7 · 1 · 5 |
| 19 | [DataDome](https://github.com/JWally/dfp-redteam-datadome) | 2 | ~50 | **5.0** | 4 · 1 · 4 |
| 20 | [Binance](https://github.com/JWally/dfp-redteam-binance) | 5 | ~62 | **4.0** | 3 · 1 · 4 |
| 21 | [Riskified](https://github.com/JWally/dfp-redteam-riskified) | 5 | ~55 | **3.8** | 3 · 1 · 3 |
| 22 | [Cloudflare Turnstile](https://github.com/JWally/dfp-redteam-turnstile) | n/a | ~70 | **3.8** | 3 · 1 · 3 |
| 23 | [Arkose Labs (FunCaptcha)](https://github.com/JWally/dfp-redteam-arkose) | 7 | ~70 | **3.8** | 3 · 1 · 3 |
| 24 | [Ravelin / Socure / Deduce](https://github.com/JWally/dfp-redteam-fraud-sensors) | n/a | ~45 | **0.2** | 0 · 0 · 1 |

_Effort = repo-observable time / RE-depth: scripts + 4×(work-phases−1) + commits÷4. No writeup-size term (verbosity ≠ difficulty). Crypto-RE grade ≠ effort: Verisoul keeps 9.5 grade but ~8 effort; Stytch (38 tools, one dense phase) is the hardest single-client break._

## The three design tiers that actually predict difficulty

1. **Server-issued proof / server-graph** (hCaptcha, reCAPTCHA v2, Fingerprint
   Pro, Kraken PoW, ThreatMetrix) — the client cannot mint the proof; it only
   exists after server-side acceptance. For TMX the moat is the server-side
   device graph + history (encrypted wire, server-decrypted, `device_id`
   assessed with reputation) — nothing client-side to break.
2. **Client-computed, server-verified** (TikTok, Verisoul, Castle, SEON,
   Forter, Binance, Kasada, Riskified) — everything is readable/tamperable at
   a client boundary; the obfuscation/VM/cipher sets the *price*, not the
   boundary.
3. **Client-asserted identity, silently trusted** (Sift) — the weakest class:
   the device identity is chosen by the client (a UUID in a plaintext image
   GET) and the collector accepts it with no signature or device check — the
   exact inverse of Fingerprint Pro's server-owned identity.

## Per-vendor writeups

**What each vendor section ends with — the three things we're after:**

> **Read payload** — could we intercept and read the raw telemetry/fingerprint
> the client ships to the server, *in plaintext*? (Or is it encrypted/opaque?)
>
> **Tamper with payload** — could we change those signals before they ship
> (e.g. fake a canvas hash, spoof the UA) and still have the **server accept
> the result** — meaning the server trusts our forged data?
>
> **Mint token** — could we produce a **fresh, valid proof/token entirely
> client-side** — impersonating a trusted client without the vendor's server
> ever issuing it? (Distinct from tamper: tampering edits an existing payload,
> minting builds one from scratch.)

Status markers: 🟢 = **achieved** · 🟡 = **partially / work in progress** · 🔴 = **not achieved** · ⚪ = **n/a / not applicable**

### [TikTok web](https://github.com/JWally/dfp-redteam-tiktok-web) — crypto-RE 9.5 / resistance 65–70
**Effort (time/RE-depth):** 26 scripts · 6 work-phases · 22 commits · effort 51.5

The most *engineered* client of the batch: a substitution-ciphered 1,913-entry string table, deobfuscated collector, 98,961-byte inflated **bytecode VM** with a self-hosting interpreter, RC4/XOR, per-request nonce, and engine-fingerprint checks. It breaks cheap mass-bypass tools — but with runtime control it's fully traceable, and we built a **faithful runner that mints X-Gnarly/X-Bogus locally** in-sandbox. Cost-raiser, not a boundary. Crypto-RE: **9.5**. Resistance: **65–70**.

**Status:**
- 🟢 Read payload: - signals + signatures captured plaintext.
- 🟢 Tamper with payload: - faithful re-execution with modified inputs.
- 🟢 Mint token: - X-Gnarly/X-Bogus minted in-sandbox via a faithful VM runner.



### [Stytch (Twilio)](https://github.com/JWally/dfp-redteam-stytch) — crypto-RE 9 / resistance 70
**Effort (time/RE-depth):** 38 scripts · 1 work-phases · 7 commits · effort 39.8

The AES-256-CBC key was recoverable from the WASM device-intel module (`C+96⊕table8304`), letting us **decrypt the telemetry in full** and run a tamper-and-reseal PoC. The WASM layer is compact/obfuscated/encrypted, which is real work — but the key ships in code and WASM memory is readable, so the client layer ultimately doesn't decide anything. Verdict is server-side. Crypto-RE: **9**. Resistance: **70**.

**Status:**
- 🟢 Read payload: - telemetry decrypted to full plaintext (AES-256-CBC key recovered from WASM).
- 🟢 Tamper with payload: - tamper-and-reseal PoC.
- 🟢 Mint token: - key ships in client code → client-mintable.


### "Pendulum" — crypto-RE 7.5 / resistance 58 *(private, codename)*
**Effort (time/RE-depth):** 12 scripts · 4 work-phases · 6 commits · effort 25.5

The ChaCha20 key state leaked from the device-intel SDK, giving us a **clean-vs-tampered oracle**: read the readable pre-encryption payload, mint/persist your own `deviceID`, and watch the server's verdict move. 48-module payload, readable end to end. Server-side verdict is the only real check; the client adds cost, not proof. Crypto-RE: **7.5**. Resistance: **58**.

**Status:**
- 🟢 Read payload: - pre-encryption payload read plaintext (ChaCha20 key recovered).
- 🟢 Tamper with payload: - clean-vs-tampered oracle.
- 🟢 Mint token: - `deviceID` mintable / persistable.

### [hCaptcha](https://github.com/JWally/dfp-redteam-hcaptcha) — resistance ~85
**Effort (time/RE-depth):** 20 scripts · 2 work-phases · 5 commits · effort 25.2

The strongest interactive-challenge design we mapped: the client never mints anything — the server issues **JWT tickets** (`hsw`/`hsj`) that pin **hash-verified** bundle URLs, the challenge request itself is encrypted (`enc_get_req`), and the context-gated **WASM secure module** (`hsw.js`) never decodes its strings or instantiates under automation. The gate fires server-side on first contact ("Please try again"), so there is no client payload to read or tamper. Crypto-RE: **n/a** (we couldn't even init the module). Resistance: **~85**.

**Status:**
- 🟡 Read payload: - captured the encrypted challenge envelope (`enc_get_req`), but it's opaque by design.
- 🔴 Tamper with payload: - no payload to edit; the client never mints anything.
- 🔴 Mint token: - token is server-issued after a human challenge solve.


### [Castle](https://github.com/JWally/dfp-redteam-castle) — crypto-RE 8 / resistance 63
**Effort (time/RE-depth):** 18 scripts · 2 work-phases · 10 commits · effort 24.5

The heaviest obfuscation of the SDK batch — 58,809-char self-referential string pool, 1,612 identifiers, numeric-keyed tables, ROT13/anti-debug workers — and the token is **fully client-minted with zero network calls**: a binary-tree checksum (`Rw`) over a rotating hash. It took real RE effort to deobfuscate, but the effort only priced the bypass; a public ecosystem (FuckCastle/TakionAPI) mints tokens from the public key. Client is a carrier, not a proof. **Production pass (x.com, 2026-11-02): the token cipher is fully reversed** — Castle ships its own `tv()` decryptor in the same bundle, and it inverts the runtime token **byte-for-byte** to TLV + per-field base64 (`tt()`/`tv()` 16-bit-lane inverse pair, `54213·13069 ≡ 1 mod 2^16`). And **13 prototype-level lies landed verbatim in a valid, submitted token** on the live auth flow (webdriver→undefined, SwiftShader→NVIDIA RTX 3060, screen 1280x800→1920x1080, UA-CH/plugins/mimeTypes); both baseline and tampered tokens POSTed to `/v1/filter`. Inner per-field ciphers (`f4/tN/lb/lM`) + `/v1/filter` score still gated. Crypto-RE: **8**. Resistance: **63**.

**Status:**
- 🟢 Read payload: - vanilla SDK: token observable client-side; **production: token cipher `tt()`/`tv()` reversed — decrypts runtime token to plaintext TLV + per-field values**.
- 🟢 Tamper with payload: - re-mint / edit and ship; public bypass ecosystem; **production: 13 live prototype-level lies in a valid submitted token**.
- 🟢/🟡 Mint token: - token is a client-computed checksum over a public key; **production: forge by running `tt()` forward** (caveat: session-accumulation + relay-side IP/TLS context).


### [Fingerprint Pro](https://github.com/JWally/dfp-redteam-fingerprint-pro) — resistance ~80+ (identity)
**Effort (time/RE-depth):** 13 scripts · 3 work-phases · 7 commits · effort 22.8

The architectural exception of the batch: identity is **server-computed and signed** (`_vid_` cookie), not client-minted. Deep pass (2026-11-01) cracked the *payload* side: the loader builds the request as `Tf-serialize → deflate-raw → kl(payload, KE, JE, xE)` — a self-decrypting XOR envelope whose **9-byte key rides inside the ciphertext** — so hooking `QK` (the encrypt boundary) reveals the **full 9,401-byte plaintext JSON: 153 signals** (canvas pixels `s209`, WebGL params `s75`, UA `s101`, automation detectors `s157`, timezone `s9`, WebAuthn `s215`, font metrics `s51`, audio FFT `s49`, computed styles `s87`, plus a leaked JS stack trace `s119`). Every input is now readable and tamperable at the client boundary; the earlier tamper differential already showed spoofed signals yield a different accepted `visitor_id`. What we still can't do is mint the identity itself — that stays server-owned. Crypto-RE: **n/a** (no client crypto to break — the wire envelope's key is in-band). Resistance: **~80+ (identity)**.

**Status:**
- **Read payload:** 🟢 — full 153-signal plaintext JSON captured at the `QK` (XOR-seal) boundary.
- **Tamper with payload:** 🟢 — every signal editable before sealing; tamper differential already server-accepted a different `visitor_id`.
- **Mint token:** 🔴 — identity is server-computed + signed; not client-mintable.


### [HUMAN Security / PerimeterX](https://github.com/JWally/dfp-redteam-human-px) — crypto-RE 4 / resistance 65
**Effort (time/RE-depth):** 14 scripts · 2 work-phases · 6 commits · effort 19.5

Full surface mapped on a live PX-Show block deployment: 8-host stack, base91 string table decoded (525 entries), **plaintext collector telemetry** read at the JSON boundary, and an embedded WebGL shader fingerprint. The decisive gate isn't the client at all — a **clean-launch real Chrome loads the full storefront**, while automated launches get blocked, meaning the *launch signature* (UA/GL/behavior) is what the server scores. Client obfuscation is a cost-raiser; the captcha fallback and server scoring decide. **Production pass (Bloomberg deployment, 2026-11-02): the transport is fully cracked** — single-byte XOR `0x32` (no key, no MAC; recovered via common-prefix analysis), all collector bodies decoded to the JSON telemetry array; **base91** string table (1,180 entries) + ~75/189 **64-bit-hashed signal names** mapped structurally. And the bypass result: **clean network identity beats the sensor** — vanilla real Chrome + residential/mobile exit + a fully *honest* 189-signal payload → `HTTP 200`, no captcha. The decisive gate is **IP/TLS/JA4 + a plausible browser**, not the fingerprint. Crypto-RE: **4**. Resistance: **65**.

**Status:**
- 🟢 Read payload: - plaintext collector telemetry; **production: XOR-0x32 transport cracked, all bodies decoded, base91 strings + 75/189 hashed signal names**.
- 🟢/🟡 Tamper: - re-encodable wire (no MAC) but verdict is server-side (IP/TLS/JA4 + launch signature).
- 🟢 Mint (this deployment): - **clean IP + real Chrome + honest payload passes (200, no captcha)**; server still decides.


### [Sift](https://github.com/JWally/dfp-redteam-sift) — crypto-RE n/a / resistance ~55 (client)
**Effort (time/RE-depth):** 8 scripts · 3 work-phases · 5 commits · effort 17.2

Five live deployments found and captured: **studypool, ksl, poshmark, depop, paulaschoice**. The modern v121 beacon (57,981 bytes, same file on `cdn.sift.com/s.js` and legacy `cdn.siftscience.com/s.js`) exfiltrates as a **1×1 image GET to `hexagon-analytics.com` — fully plaintext**: device UUID (`uu`, also the cross-site `__ssid` cookie), canvas/webgl/audio fingerprints, session/user/partner MD5 hashes, webdriver flag, incognito flag. **Finish-out (2026-11-01): the beacon is forgeable end-to-end** — we minted a fresh device UUID + own session MD5 + forged canvas/webgl/audio/font hashes and the collector **accepted it (HTTP 200 + GIF89a 1×1) with no signature, no HMAC, no device check**. The device identity is client-asserted and silently trusted — the weakest design class (the inverse of Fingerprint Pro's server-owned identity). The verdict/scoring layer is server-side and untouched. Crypto-RE: **n/a** (no crypto). Resistance: **~55** (client identity fully forgeable).

**Status:**
- **Read payload:** 🟢 — full plaintext beacon payloads captured on 5 live sites (image GET, no encryption).
- **Tamper with payload:** 🟢 — every field is plaintext and editable; forged canvas/webgl/audio/font values accepted.
- **Mint token:** 🟢 — fresh device UUID + session minted from scratch; collector returns 200 + GIF (identity is client-asserted).


### [Kasada (KPSDK: Twitch/Nike/FootLocker)](https://github.com/JWally/dfp-redteam-kasada) — crypto-RE 4 / resistance ~50
**Effort (time/RE-depth):** 9 scripts · 2 work-phases · 4 commits · effort 14.0

The cautionary PoW is now a full bypass. `x-kpsdk-cd` arrives as **plaintext JSON** and garbage/empty/absurd `cd` all pass (`gql.twitch.tv/integrity` returns 200 + a fresh v4.local token), and the **deep pass (2026-11-01)** proved a **fresh `x-kpsdk-ct` is mintable with one GET** — there is no `/tl` POST on this deployment; the cleared ct comes back in the `ips.js` response header and the bytecode VM is never verified. We also devirtualized the VM itself (86-opcode ISA, 68,748-word stream, verified disassembler) — but it turns out the server doesn't check it. IP/TLS binding is the only lever left (untested cross-host). Crypto-RE: **4**. Resistance: **~50**.

**Status:**
- 🟢 Read payload: - `cd` JSON plaintext + `ct` from `ips.js` header.
- 🟢 Tamper with payload: - garbage/empty/absurd `cd` accepted (presence-checked).
- 🟢 Mint token: - fresh `ct` minted with one GET; full session mint verified on 3 merchants.



### [reCAPTCHA v2](https://github.com/JWally/dfp-redteam-everydollar) — resistance ~85
**Effort (time/RE-depth):** 9 scripts · 2 work-phases · 3 commits · effort 13.8

Dissected the 829 KB engine on the Auth0 login instance: a deliberately **minimal** signal surface (navigator, canvas, screen, timing — no webdriver/webgl/audio/fonts) plus one explicit **iframe-detection** check (`parent != self ? true : frameElement != null`). The defense is interaction scoring + the image challenge — the token only exists after the server accepts the interaction, which is why our automated-but-smooth clicks were **rejected with no token**. It's the same philosophical design as hCaptcha: server-issued proof, nothing client-side to break. Crypto-RE: **n/a**. Resistance: **~85**.

**Status:**
- ⚪ Read payload: - no client payload exists; the token only appears after the server accepts the interaction.
- 🔴 Tamper with payload: - never obtained a token to modify.
- 🔴 Mint token: - server-issued proof (interaction scoring + image challenge).


### [ClearSale](https://github.com/JWally/dfp-redteam-clearsale) — crypto-RE 1 / resistance ~30-35 (client)
**Effort (time/RE-depth):** 8 scripts · 2 work-phases · 3 commits · effort 12.8

ClearSale ships a **192 KB obfuscated** fingerprint engine
(`device.clearsale.com.br/p/fp.js`, `@clear.sale/behavior-analytics-fingerprint-sdk`
v1.0.255, public sandbox appkey `r3j6a9f10bmssayw2thp`) that reports via
**plaintext 1×1 image GETs**. `fp2.png` carries attributes in the clear
(`aa`=UA, `ab`=language, `ac`=colorDepth, `ae/af`=screen, platform) plus
`ar/as/ay/h1/h4/h6/h7/az` = 32-hex **per-signal hashes** (canvas/WebGL/
fonts/audio). `fp1.png` carries the identity: `ba` = `__udf_j` cookie value,
`bb` = `__udf_l` localStorage value (both 128-hex GUIDs), `a2` = computed
device fingerprint hash, `sm` flag. The string table (1,125 strings, obfuscator.io
shuffle-checksum `0xd5fbb`) decoded in seconds. **The identity is
client-generated and client-persisted** — no server issuance (ci.png sends no
Set-Cookie). **Tamper accepted**: fp2/fp1 rewritten mid-flight (UA/lang/screen/`sm`)
all 200. **Fully mintable**: pre-seed `__udf_j=ff…ff` + `__udf_l=aa…aa` and
fp1.png ships them **verbatim** with 200 — no format/identity validation
(contrast Kount's 400-reject). Moat is server-side ClearSale Total scoring +
behavior graph (not assessed). Crypto-RE: **1**. Resistance: **~30-35**. **Live deployments:** ~179 scans/30d, Brazil-heavy; live appkeys inline in page HTML (havaianas `32j1samy8kaepiyf7dqc`, sephora `cf940e9b26`, esfera `9ov321577mqpmizuoe4q`).

**Status:**
- 🟢 Read payload: - engine string table decoded (1125); fp2 plaintext + per-signal hashes; ba/bb identity readable.
- 🟢 Tamper with payload: - fp2/fp1 rewritten mid-flight → accepted (200).
- 🟢 Mint token: - device ID entirely client-forgeable (cookie + localStorage); shipped verbatim + accepted.
- 🟢 Synthesize: - hashes are MurmurHash3 x64_128 of enumerable inputs (h6 reproduced byte-exact); a coherent fake-device fingerprint (RTX-4090) is accepted (200) — collector can't cross-check.
### [Kount (Equifax)](https://github.com/JWally/dfp-redteam-kount) — crypto-RE 0.5 / resistance ~40 (client)
**Effort (time/RE-depth):** 7 scripts · 2 work-phases · 2 commits · effort 11.5

Kount's device collection ships in three pieces: a 6 KB `ka.ClientSDK`
(`/collect/sdk?m=<merchant>`), a server-config script (`/collect/kasupport`)
carrying the **server-issued FPCV** device cookie, and a 24.5 KB collector
engine in a hidden `/logo.htm` iframe that runs 5 modules and POSTs the
fingerprint to `/md`. **The wire is plaintext form data** — `n=clientdata`
(screen/color-depth, `wd` webdriver flag, `rm` reduced-motion, DST timezone
probe, the 32-hex cross-session `ss` anchor), `n=canvasdata` (unmasked WebGL
vendor/renderer + `zt`=MD5 of canvas), `n=webrtc` ×2 (full SDP + ICE → local
and srflx IP leak), `/fin` completion. No encryption, no signature — the only
hashes are MD5s of collected inputs. **Tamper is accepted**: `/md` bodies
rewritten mid-flight (webdriver/reduced-motion/screen/GL flipped) all return
200. **`ss` is mintable**: a forged 32-hex anchor ships verbatim and is
accepted (200) — no server cross-check. **Full device replay works**: a stolen
(ss + FPCV) pair replays with all-200s and the FPCV is not reissued. The one
server-side check: a garbage FPCV gets **400** from `/collect/cookiestore` —
the FPCV itself can't be forged. The real moat is server-side FPCV validation
+ the RIS/persona graph (scoring layer not assessed — no merchant creds).
Crypto-RE: **0.5**. Resistance: **~40**. **Live deployments:** ~1,325 urlscan scans/30d (incl. `my.equifax.ca/login` — Equifax dogfoods it); merchant IDs ride in the clear in `?m=NNNN`. No `ss`↔FPCV linkage (mismatch accepted).

**Status:**
- 🟢 Read payload: - full plaintext `/md` decode (clientdata/canvasdata/webrtc); engine + SDK readable.
- 🟢 Tamper with payload: - `/md` rewritten mid-flight → accepted (200).
- 🟢 Mint token: - `ss` anchor forged + accepted; stolen (ss + FPCV) device pair replays cleanly (FPCV forge rejected 400 — server-validated).

### [Forter](https://github.com/JWally/dfp-redteam-forter) — crypto-RE 7 / resistance ~55
**Effort (time/RE-depth):** 6 scripts · 2 work-phases · 6 commits · effort 11.5

**Fully decrypted (2026-11-01).** The 593 KB CFG-flattened sensor is *not* a VM, and the `/events` payload is **AES-256-CTR with a static key recovered from the sensor's init** — flow is JSON→gzip→AES-CTR, and the earlier "`Be` magic" was just the first ciphertext bytes, not an envelope. We now read the full 19-signal plaintext (canvas `cfp`, `webgl`/`wgp`, `wrtc`, `hdls`, the `pow` token challenge) and proved a **tamper round-trip locally**: decrypt → modify (hardwareConcurrency, UA) → re-encrypt → structurally identical wire blob. Transport gives zero protection because the key ships in the client; the server-issued `pow` cost gate is the only thing left. Crypto-RE: **7**. Resistance: **~55**.

**Status:**
- 🟢 Read payload: - full 19-signal plaintext via recovered AES-CTR key (JSON→gzip→CTR).
- 🟢 Tamper with payload: - decrypt → modify → re-encrypt round-trip proven locally.
- 🟡 Mint token: - payload forgeable, but the `pow` proof token is server-issued (PoW cost gate, not minted).


### [SEON (v6 JS SDK)](https://github.com/JWally/dfp-redteam-seon) — crypto-RE 7 / resistance 60
**Effort (time/RE-depth):** 10 scripts · 1 work-phases · 4 commits · effort 11.0

Recovered the 2,857-string obfuscation table and read the full **9,713-byte pre-encryption fingerprint tree** at the `JSON.stringify` boundary — so every signal (tz/canvas/webdriver) could be **minted and tampered before the custom cipher sealed it**. The session (`W;ver;salt;cipher`) is fully client-minted with zero network calls, and the SDK even does DNS-exfil via `*.seondnsresolve.com`. Custom cipher unbroken — but we never needed to break it. Crypto-RE: **7**. Resistance: **60**.

**Status:**
- 🟢 Read payload: - full pre-encryption tree read plaintext at `JSON.stringify`.
- 🟢 Tamper with payload: - mint + tamper any signal before the custom cipher seals it.
- 🟢 Mint token: - session (`W;ver;salt;cipher`) fully client-minted, 0 network.


### ThreatMetrix (LexisNexis Risk Solutions) — resistance ~75 (server-graph) *(private)*
**Effort (time/RE-depth):** 4 scripts · 2 work-phases · 3 commits · effort 8.8

The legacy industry-reference device-intel engine, assessed as embedded inside a commercial fraud product's `lite` sensor. **Deep break (2026-11-01): the client obfuscation is fully broken** — all 47 string-table modules decoded (wire params, WASM murmur3 exports, SID ECDSA signing scheme, worker protocol, canvas/WebGL shaders), the beacon URL template + `td_6l` XOR key recovered, and the `td_3F` body encoder inverted (`len&payload XOR key&10 → hex → base64url`, round-trip verified). The wire is **readable and mintable ourselves** — it's a reversible XOR, not encryption. What keeps TMX strong is NOT the client: the estate's session-data oracle shows the server-side `profile_data` (device_id, EC `crypto_browser_id`, fuzzy_browser_id, lie_count, named MAJOR anomalies, IP proxy split, timezone offset-vs-computed, device_first_seen history), and the headless-vs-clean differential shows launch-quality detection working precisely (lie_count 3→0, anomalies ×3→none, fresh server-assessed device_id per environment). The moat is the **server-side device graph + signed SID + WASM-hashed canvas probes**, not the wire. Crypto-RE: **n/a** (reversible XOR, no real crypto). Resistance: **~75**.

**Status:**
- **Read payload:** 🟢 — full 47-table decode + wire format recovered; beacon URL + XOR key known.
- **Tamper with payload:** 🟢 — `td_3F` is a reversible XOR (key = session id); encode any payload.
- **Mint token:** 🔴 — `device_id` is server-assessed with graph history; client can't mint trusted identity.
- **Mint token:** 🔴 — `device_id` is server-assessed with history; client cannot mint trusted identity.


### [Kraken (Pro)](https://github.com/JWally/dfp-redteam-kraken) — crypto-RE 6 / resistance 70
**Effort (time/RE-depth):** 3 scripts · 2 work-phases · 6 commits · effort 8.5

The rare "client-computed defense done right": an in-house **proof-of-work** (`/pow` → double SHA-256 leading-zero nonce → `x-pow` header) that is fresh per request, **server-verified**, and worthless without the work — tampering just produces an invalid answer. Stacked with Cloudflare edge, WebAuthn passkey step-up, and a server-side risk API, with no client fingerprint bundle at all. The strongest client-side defense of the batch, and the honest baseline for judging Kasada's PoW against. Crypto-RE: **6**. Resistance: **70**.

**Status:**
- 🟢 Read payload: - `x-pow` nonce visible client-side.
- 🔴 Tamper with payload: - PoW is fresh + server-verified; tampering yields an invalid answer.
- 🔴 Mint token: - the work *is* the verification; cost-based, can't be minted or replayed.


### [NoFraud](https://github.com/JWally/dfp-redteam-nofraud) — crypto-RE 0.5 / resistance ~35-40 (client)
**Effort (time/RE-depth):** 4 scripts · 2 work-phases · 2 commits · effort 8.5

NoFraud's Device JavaScript (account `76913`, public sample from their docs)
is an **18 KB minified, non-obfuscated script** that builds a single
**9,132-byte plaintext JSON POST** to `d-ipv4.mmapiws.com/ant_squire`
(configurable via `window.__mmapiws.apiHost`). It's the **rawest wire of the
batch**: `navigator.webdriver` ships **in the clear**, plus full 79-prop
navigator enumeration, full screen enumeration, 17 Math-function precision
probes, 28 CSS system colors, audio/video codec matrix, installed fonts,
speechSynthesis voices, battery, plugins — only canvas/WebGL are MurmurHash3'd.
The identity is **server-issued**: the POST response `<domain>;<uuid>:<sha1>`
becomes the 2-year `__mmapiwsid` (cookie + localStorage + IndexedDB). Tamper
accepted: the JSON rewritten mid-flight (webdriver/UA/screen/fonts/hashes) →
200. Identity: a **stolen** pair is echoed verbatim (device replay works); a
**garbage** pair is reissued (server-validated — can't forge). Moat is the
server-side id validation + pass/fail/review scoring (not assessed).
Crypto-RE: **0.5**. Resistance: **~35-40**. **Live deployments:** ~4,900 scans/30d (largest); **rebranded to Wyllo** (Shopify app `wyllo-formerly-nofraud`, `device.js?shop=<store>`).

**Status:**
- 🟢 Read payload: - single plaintext JSON POST decoded; webdriver flag + raw signals in the clear.
- 🟢 Tamper with payload: - JSON rewritten mid-flight → accepted (200).
- 🟢 Mint token: - stolen `__mmapiwsid` pair replayed (echoed/held); forge rejected (server reissues).


### [Verisoul](https://github.com/JWally/dfp-redteam-verisoul) — crypto-RE 9.5 / resistance 72
**Effort (time/RE-depth):** 7 scripts · 1 work-phases · 5 commits · effort 8.2

The `dd` cipher was reversed and **validated 100%** against a live oracle, yielding a fully readable **195-field** bot-detection payload. The real challenge isn't the crypto — it's that Verisoul **rotates identifiers and constants per delivery**, so static deobfuscation breaks on the next request and hooks have to be structural rather than value-based. Client-mintable yes; replay is bound by `session_token` and server-side binding checks. Crypto-RE: **9.5**. Resistance: **72**.

**Status:**
- 🟢 Read payload: - 195-field payload read in full plaintext (cipher reversed 100%).
- 🟢 Tamper with payload: - tamper validated against live oracle.
- 🟢 Mint token: - fully client-mintable (structural hooks defeat the per-delivery rotation).


### [DataDome](https://github.com/JWally/dfp-redteam-datadome) — crypto-RE ~2 / resistance ~50 (client)
**Effort (time/RE-depth):** 4 scripts · 1 work-phases · 4 commits · effort 5.0

DataDome's JS Tag (`js.datadome.co/tags.js`, keyed by `window.ddjskey`) collects behaviour + device signals and POSTs them to `api-js.datadome.co/js/` (public endpoint + key). **The `jspl` payload is not crypto — it's a reversible xorshift-XOR stream cipher** (`n^=n<<13;n^=n>>17;n^=n<<5`, seeded from a hash of the **public** `ddjskey` + `11027890091` + the request timestamp, custom base64 `-_0-9A-Za-z`; no HMAC/AEAD/public key). A **prior v5.6.6 harness decrypted real payloads** and extracted **203 signals** — mouse/pointer counters, **keyboard dynamics** (`k_hA/k_hSD`…), stroke stats (`es_*`), the full `nt_*` **network-timing** set, viewport, **canvas/CSS fingerprint** (`css*`,`fph`), self-stack-integrity (`ccs*`), codec probes, storage quota, UA-CH, WebGL, **AI-agent detectors** (`gai`,`sqt`). The current v5.11.2 bundle still carries the `11027890091` constant but refactored the generator (v5.6.6 decoder → 41% printable), so the exact current keystream is re-derivable, not yet done. Live: the tags endpoint is a **promiscuous trust endpoint** — unmodified, tampered, **flipped-byte `jspl`**, and fake-`cid` POSTs all return `200` + a fresh `datadome` cookie; the cookie + invisible Device Check PoW are server-issued. Crypto-RE: **~2**. Resistance: **~50** — obfuscated, not protected; the moat is server-side.

**Status:**
- 🟢 Read flow / endpoint / key + **203-signal inventory** (`SIGNALS-SHIPPED.md`).
- 🟢/🟡 Read payload: reversible XOR obfuscation, not crypto (v5.6.6 inverted; v5.11.2 re-derivable).
- 🟢 Tamper (envelope): tags POST accepted unmodified/tampered/flipped-byte/fake-cid → `200` + fresh `datadome` cookie.
- 🔴 Mint session: `datadome` cookie + Device Check are server-issued — not client-mintable.

### [Binance](https://github.com/JWally/dfp-redteam-binance) — crypto-RE 5 / resistance 62
**Effort (time/RE-depth):** 3 scripts · 1 work-phases · 4 commits · effort 4.0

Stacked defense: AWS WAF JS challenge (`aws-waf-token`) at the edge plus an in-house `BNC_FV_KEY` fingerprint — canvas/webgl/audio/fonts/hardware hashed to a 32-hex string in `localStorage.__BNC_FP_INFO__` — which we **read in full and tamper-proven** (rewriting a signal flips the hash). The fingerprint is a correlation signal, not a proof: the WAF stops automated launches and HMAC/2FA guards the API. Crypto-RE: **5**. Resistance: **62**.

**Status:**
- 🟢 Read payload: - `__BNC_FP_INFO__` fingerprint read in full plaintext.
- 🟢 Tamper with payload: - signal rewrite flips the hash (tamper-proven).
- 🟡 Mint token: - can mint the fingerprint hash, but not the session/WAF token (edge + HMAC/2FA).


### [Riskified](https://github.com/JWally/dfp-redteam-riskified) — crypto-RE 5 / resistance ~55 (client)
**Effort (time/RE-depth):** 3 scripts · 1 work-phases · 3 commits · effort 3.8

Riskified's "Beacon for Web" is a client-sent behavioral sensor: it collects `{cart_token, shop, page_id, href, events}` and ships it to `c.riskified.com/v2/client_infos` (XHR + `img.riskified.com` image fallback + `/e.gif` error pings). We captured the real beacon (`beacon.riskified.com?shop=<domain>`), fully deobfuscated its `_0x` string table (3,329 strings), and recovered the "obfuscation": **`payload_obfuscate = b64(encodeURIComponent(JSON)) + a static char shuffle — no key, no HMAC, reversible from the wire alone.** Same design class as SEON's custom cipher: it hides the payload from casual inspection, not from a determined RE. The events queue flushes every 3s; the fraud verdict is server-side. Crypto-RE: **5** (no crypto — just an encoding). Resistance: **~55** (client payload fully readable/tamperable; decision layer server-owned).

**Status:**
- **Read payload:** 🟢 — full payload format + obfuscation recovered from the shipped beacon (b64+shuffle, reversible).
- **Tamper with payload:** 🟢 — any event/cart_token/shop field editable before send; encoding gives no integrity.
- **Mint token:** 🟡 — payload is client-constructed (forgeable), but a live beacon POST wasn't captured to prove server acceptance; verdict is server-side.


### [Cloudflare Turnstile](https://github.com/JWally/dfp-redteam-turnstile) — crypto-RE n/a / resistance ~70 (client)
**Effort (time/RE-depth):** 3 scripts · 1 work-phases · 3 commits · effort 3.8

Cloudflare's CAPTCHA-alternative, the same **Challenge Platform** behind its WAF "Just a moment…" pages. The loader is `challenges.cloudflare.com/turnstile/v0/api.js` (302 → a versioned build); the widget renders in a **same-origin iframe** at `/cdn-cgi/challenge-platform/…/turnstile/…` where non-interactive **proof-of-work, proof-of-space, Web-API probing and browser-quirk/human-behaviour** challenges run, reporting back via `postMessage`. The token (`cf-turnstile-response`) is **single-use, 300 s TTL, validated server-side** at `/turnstile/v0/siteverify` with a **secret that never ships** — so the client **cannot mint or forge** it (public test keys return the literal `XXXX.DUMMY.TOKEN.XXXX` on pass; the force-interactive key withholds the token behind the wall). The only client lever is to **drive a real (patched) browser** and solve it legitimately (public [`Theyka/Turnstile-Solver`](https://github.com/Theyka/Turnstile-Solver) / patchright reads `[name=cf-turnstile-response]`). Crypto-RE: **n/a**. Resistance: **~70** — a genuinely good design: client is a shuttle, the proof is server-issued and single-use.

**Status:**
- 🟢 Read flow: loader + same-origin challenge iframe + postMessage + token issuance.
- 🔴 Mint/forge token: server-side, single-use, secret-gated — not forgeable. Wire is opaque (no readable payload); testkey Siteverify returns `result_with_testing_key:true` and accepts any token, production does not.
- ⚪ Bypass: a patched real browser solves it (browser automation, not crypto).

### [Arkose Labs (FunCaptcha)](https://github.com/JWally/dfp-redteam-arkose) — crypto-RE ~7 / resistance ~70-75 (client)
**Effort (time/RE-depth):** 3 scripts · 1 work-phases · 3 commits · effort 3.8

A challenge-based proof vendor, assessed at the client via a production site's **public** key. `client-api.arkoselabs.com/v2/<pk>/api.js` (295 KB, proprietary VM) → `GET /v2/<pk>/settings` → `POST /fc/gt2/public_key/<pk>` whose `c` field is the **encrypted BDA** (~9.6 KB). The `==` mid-string is the signature of concat base64 chunks — `b64(iv)+b64(tag)+b64(rsa-wrapped key)+b64(ct)` — i.e. **AES-256-GCM(fingerprint)** with the key **RSA-OAEP(SHA-256)**-wrapped under the **per-site RSA public key**. That key is reconstructed at runtime inside the obfuscated VM (not a static string), so forging `c` needs it lifted — and **cross-site use is rejected** (`400 {"error":"DENIED ACCESS"}`). Crypto is real and unbroken; the envelope was characterised, not decrypted. Crypto-RE: **~7**. Resistance: **~70-75** — real client crypto sealed to a server key + challenge VM + per-site binding.

**Status:**
- 🟢 Read flow / envelope structure: loader, settings, `gt2` POST, `c` layout.
- 🔴 Read BDA plaintext: genuine RSA-OAEP + AES-GCM — not broken.
- 🔴 Forge `c` / mint token: needs the per-site RSA public key (runtime, obfuscated VM); cross-site binding rejects. Tamper caught: replay/tamper/garbage `c` all → `400 DENIED ACCESS`.

### [Ravelin / Socure / Deduce](https://github.com/JWally/dfp-redteam-fraud-sensors) — client recon (resistance ~45–60)
**Effort (time/RE-depth):** 0 scripts · 0 work-phases · 1 commits · effort 0.2

Three fraud-decision vendors, client-side recon. **Ravelin** (`ravelinjs` v2.3.0, **open source Apache-2.0**): the device id is a **client-minted** `rjs-`+UUID (cookie `ravelinDeviceId`) — Sift-class, mintable — while card data is genuinely sealed (**AES-GCM + RSA** to a server `rsaKey`, same family as Arkose); it also ships headless/webdriver/Selenium bot detectors, and explicitly removed its old client fingerprinting. **Socure** (Digital Intelligence Web SDK, public `device-risk-sdk.js`): first-party `_s_did` cookie + a **server-issued sessionToken** for the RiskOS API; live capture is **key-gated**. **Deduce**: server-to-server only ("no JavaScript") — no client sensor to dissect. Read 🟢 (Ravelin source, Socure SDK) / live-capture ⚪ (needs tenant keys) / Deduce ⚪ (server-side).

**Status:**
- 🟢 Read: Ravelin full source (device-id + card crypto + bot detectors); Socure public SDK.
- 🟢 Mint device id: Ravelin device id is a client UUID (`rjs-…`); Socure/others server-issued.
- 🔴 Card crypto / live path: Ravelin AES-GCM+RSA not broken; Socure needs a tenant SDK key.


## Also in this project

- [Cross-vendor crack table](https://github.com/JWally/dfp-redteam-tiktok-web/blob/main/CROSS_VENDOR_CRACK_TABLE_2026_10_26.md)
  — the full scored table with per-vendor detail, source-writeup map, and
  cross-cutting takeaways.
- Private repos, unlinked, listed above under codenames ("Pendulum") + ThreatMetrix.
