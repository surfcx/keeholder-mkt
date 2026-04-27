# Privacy Policy — KeeHolder

Last updated: 2026-04-26.

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
- For cloud-backed databases (OneDrive, Google Drive, Dropbox): a
  cached copy of the encrypted .kdbx file and its metadata (ETag,
  modification date) in the App Group container, plus an OAuth refresh
  token in the shared iOS Keychain.

For databases opened via the iOS Files app, the KDBX file is **not**
copied or stored by the app — it stays where you put it, and the app
holds a security-scoped bookmark (a filesystem handle, not a copy).

For databases opened via the built-in OneDrive, Google Drive, or
Dropbox integration, the app caches the encrypted .kdbx file locally
(in the App Group container) for offline access. The cached file is
the same encrypted blob your cloud provider stores — it is never
decrypted on disk. OAuth refresh tokens for cloud providers are stored
in the shared iOS Keychain.

## What the app does not store

- The decrypted contents of your database — held only in memory
  during a session, and zeroed when the app is locked or
  backgrounded.
- Any analytics, usage statistics, or telemetry on which entries
  you opened, which fields you copied, or how often you used the
  app.
- Account information. There is no account.

## Network access

- The **cryptographic and database-handling code** makes **no network
  calls.** Key derivation, decryption, and XML parsing are fully
  offline.
- When you use the built-in OneDrive, Google Drive, or Dropbox
  integration, the app communicates with that provider's REST API to
  download, upload, and check metadata for your .kdbx file. It also
  exchanges OAuth tokens with the provider's authorization server.
  These are the **only** network calls the app makes, and they occur
  only for cloud-backed databases — never for Files-app-backed
  databases.
- The app does not contact any proprietary backend, telemetry
  endpoint, or analytics service.
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

Security issues: see [`SECURITY.md`](SECURITY.md).
General questions: [Contact](https://github.com/surfcx/keeholder-mkt/issues).
