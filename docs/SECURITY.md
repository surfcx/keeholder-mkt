# Security Policy

## Project status

`kp-poc` is pre-1.0 and **unaudited**. It is not yet ready to hold real
secrets. Do not use it as your daily password manager — the fixtures
and test databases shipped in this repository exist purely for format
verification.

A scoped third-party audit of the crypto and key-handling layers
is planned for the v2.0 cycle, not v1.0. v1.0 leans on the
inspectable trust surface itself: open source under MIT, no
external Swift package dependencies in the cryptographic path,
vendored Argon2 at a fixed commit, and zero network calls from
the cryptographic or database-handling code. The v2.0 audit
report and all findings (fixed or accepted) will be published
alongside the v2.0 release.

## Reporting a vulnerability

Please report security issues privately via one of these channels,
in preference order:

1. **GitHub Security Advisories** — open a draft advisory on this
   repository. This is the preferred channel once the repository is
   public; it keeps the report confidential until we coordinate a fix
   and disclosure.
2. **Email** — `neelagiri@outlook.com`. Reports to this address are
   read by the maintainer.

Please include:

- A short description of the issue and its impact.
- Reproduction steps or a proof-of-concept if you have one.
- Your name and how you'd like to be credited in the fix changelog,
  or that you'd prefer to remain anonymous.

Please do **not** open a public GitHub issue for anything you believe
has a security impact.

We aim to acknowledge reports within 72 hours and to provide a
timeline for remediation within 7 days. Disclosure is coordinated —
we'll request an embargo window long enough to ship a fix.

## Threat model

### In scope

- Device theft with the owner not actively using the device.
- Malicious apps running on the same device (sandboxed; cannot read
  our Keychain items without biometric approval).
- Compromised cloud storage providers — we assume the KDBX file on
  iCloud Drive / Google Drive / Dropbox may be modified, truncated,
  or observed by a hostile backend. HMAC verification catches
  tampering; we can't prevent denial of access.
- Network adversaries — no meaningful attack surface here because the
  crypto and database-handling code make no network calls.
- Casual forensic access — an attacker with a passcode-unlocked
  device but without biometrics.

### Out of scope

- Jailbroken devices with root-level attackers. Once the device's
  sandboxing is broken, application-level defenses do not apply.
- State-level adversaries with working device exploits.
- Hardware-level attacks (cold-boot, side-channel analysis on the
  Secure Enclave, hardware implants). These are acknowledged as
  limits rather than ignored.

## Key handling

- The master password is entered into a `SecureField`, held in memory
  only long enough to derive the composite key, then overwritten.
- The composite key is derived via the KDBX-specified KDF (Argon2id
  preferred for new databases; AES-KDF supported for KDBX3 reads).
- On successful unlock, the derived master password is optionally
  stored in the iOS Keychain with `kSecAttrAccessControl` set to
  `.biometryCurrentSet`. Re-enrolling Face ID / Touch ID therefore
  invalidates the stored key — the user must re-enter the password.
- In-memory decrypted database and keys are wiped on app background,
  explicit lock, and after a configurable idle timeout (default 5
  minutes).

## Clipboard

- Copy operations use `UIPasteboard.general.setItems(_:options:)` with
  `.expirationDate` set to a user-configurable window (15–120s,
  default 45s).
- Copied content is never persisted or logged.
- Universal Clipboard is disabled for sensitive copies via
  `.localOnly`.

## Biometric authentication

- Biometric unlock is an unlock shortcut, not a security boundary on
  its own. The master password in the Keychain (protected by the
  device's biometric enrollment) is what's actually guarded;
  biometrics gate access to it.
- If biometrics fail or are unavailable, the user can always fall
  back to the master password.
- Biometric state is re-checked on every unlock, not cached.

## Memory hygiene

Swift's value semantics make true secure-memory handling difficult:
`String` and `Data` are copied and moved by the runtime, with no
reliable way to wipe all copies. Mitigations we apply:

- Sensitive material is kept in `Data` buffers where practical.
- Decrypted content lifetime is minimized — groups, entries, and
  protected fields are derived on demand rather than cached.
- No third-party analytics, no telemetry on database contents.

We document these limits honestly rather than claiming guarantees the
platform cannot deliver. `mlock`ed memory regions for long-lived keys
are being evaluated for a later release.

## Dependencies

See [`docs/reference/dependencies.md`](docs/reference/dependencies.md)
for the complete dependency inventory — every third-party library,
vendored C source, and first-party Apple framework used, with
license and rationale.

The only non-Apple code in the shipping binary is the vendored
`phc-winner-argon2` reference implementation, pinned at a fixed
commit hash. There are currently zero runtime Swift-package
dependencies.
