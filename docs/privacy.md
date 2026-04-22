# Privacy Policy — KeeHolder

Last updated: 2026-04-22.

**KeeHolder does not collect personal data. There is no analytics
SDK, no telemetry on database contents, and no network call from the
cryptographic or database-handling code.** This document explains
what little data the app does touch, and where it lives.

## What the app stores

- A list of databases you've added — file bookmarks, display names,
  and per-database settings (last-opened timestamp, biometric
  opt-in flag, optional keyfile bookmark, last-known modification
  date). Stored as a single JSON file in the app's App Group
  container (`group.com.surfcx.kppoc`).
- Your master password — only when you opt in to Face ID / Touch ID
  unlock, and only in the iOS Keychain behind `.biometryCurrentSet`
  access control. Re-enrolling Face ID or changing your passcode
  invalidates the entry; the app then prompts for the master
  password again.
- App settings — clipboard timeout, idle-lock interval, launch
  biometric gate. Stored in standard `UserDefaults`.

The KDBX file you opened is **not** copied or stored by the app.
The file lives where you put it (iCloud Drive, Dropbox, on-device,
etc.); the app holds a security-scoped bookmark, which is a
filesystem handle — not a copy of the file or its contents.

## What the app does not store

- The decrypted contents of your database — held only in memory
  during a session, and zeroed when the app is locked or
  backgrounded.
- Any analytics, usage statistics, or telemetry on which entries
  you opened, which fields you copied, or how often you used the
  app.
- Account information. There is no account.

## Network access

- The app and its cryptographic / database-handling code make **no
  network calls.** This is verifiable in the source.
- The app does not contact any backend, telemetry endpoint, or
  third-party service.
- iOS may make incidental network requests outside the app's
  control — for example, when iCloud Drive syncs the underlying
  KDBX file, or when MetricKit submits aggregate system metrics
  to Apple. These are part of the operating system, not the app.

## On-device diagnostics

iOS's MetricKit framework writes crash reports, hang traces, and
aggregated performance metrics to a directory inside the app's App
Group container (`group.com.surfcx.kppoc/diagnostics/`). These
payloads contain system-level signals only — never decrypted
database content. The app does **not** upload them. If you want to
share a diagnostic payload with the maintainers, you can export it
yourself via the Files app.

The directory is capped at 5 MB; older payloads are deleted to
keep the cap.

## Children

The app is rated 4+. It does not collect personal information
from anyone, including children.

## Changes to this policy

Material changes will be noted in the GitHub repo's release notes.
The current canonical version of this policy lives at
`docs/launch/privacy.md`.

## Contact

Security issues: see [`SECURITY.md`](TBD: repo SECURITY.md URL).
General questions: [TBD: support email or repo issues URL].
