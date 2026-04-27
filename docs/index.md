# KeeHolder — Trustworthy KeePass for iOS

> Trust is the product.

A KeePass (.kdbx) client for iPhone and iPad that holds nothing it
doesn't need to. Your file lives in your Files provider. Your master
password lives in your head. The app is the lens.

_Coming soon to the App Store._

---

## Why trust is the product

A password manager is a single point of failure. The right question
isn't "what features does it have," it's "what would I have to trust
to use it?"

KeeHolder is built so the answer is short:

- **No server.** There is no backend and no account system. There is
  nothing to opt out of, because there is nothing to opt into.
- **Zero-knowledge by construction.** The master password, derived
  key, and decrypted entry contents never leave the device. They are
  never logged, serialized, or sent anywhere. The app and its
  cryptographic core make zero network calls.
- **Small trust surface.** No external Swift package dependencies
  in the cryptographic path. Argon2 is vendored at a fixed commit.
  Salsa20 and ChaCha20 are hand-rolled in-tree against published
  test vectors. The trust boundary is small enough to reason about
  without leaning on a transitive dependency graph.

---

## What it does

- Opens KDBX3 and KDBX4 databases from any iOS Files provider —
  iCloud Drive, on-device, anywhere the document picker reaches.
  Built-in OneDrive, Google Drive, and Dropbox integration with
  in-app file browsing, offline cache, and conflict detection.
- Unlocks with a master password, an optional keyfile, and (per
  database, opt-in) Face ID or Touch ID.
- Browses the group tree, searches across titles, usernames, URLs,
  notes, tags, and custom fields.
- Shows full entry detail with a tap-to-reveal password and a
  copy-to-clipboard that auto-clears after a configurable timeout.
- Locks automatically after configurable idle, and always locks when
  backgrounded.
- iPad: three-column split view. iPhone: stack navigation. Dark mode,
  Dynamic Type, and VoiceOver throughout.

---

## Status

v1.0. **Unaudited.** Full read/write KDBX3 and KDBX4, entry and group
editing, database creation, AutoFill, and TOTP.

The v1.0 trust story is the small trust surface itself: no
external Swift package dependencies in the crypto path, vendored
Argon2 at a fixed commit, and zero network calls from the
cryptographic or database-handling code. A formal third-party
audit is planned for v2.0; the report will publish alongside that
release.

The threat model, key handling, and known limitations live in
[`SECURITY.md`](SECURITY.md). Until the v2.0 audit
publishes, **do not store irreplaceable secrets** in databases you
only open with this app.

---

## Pricing

Free to download and use with one database. Unlock multiple databases
(and future paid features as they ship) with a one-time purchase. No
subscriptions, no ads, no upsells inside your vault.

---

## FAQ

### Why another KeePass client?

Most people don't need one — KeePassium and Strongbox are mature,
capable, and fully open source. KeeHolder is built for a narrower
audience: people moving off a hosted password manager (1Password,
Bitwarden, LastPass) who want an iOS reader that doesn't introduce a
new server, doesn't copy their KDBX into a private sandbox, and keeps
the trust surface small enough to reason about.

The wedge is the migration path. The step-by-step guides under
[`docs/migration/`](../migration/) route a 1Password / Bitwarden /
LastPass export through KeePassXC's audited desktop importers into a
KDBX file that KeeHolder reads natively. KeePassXC handles the long
tail of edge cases (custom fields, attachments, TOTP) on the desktop,
where that work is best done; KeeHolder reads the resulting file on
iOS without re-implementing those importers.

If migration isn't relevant to you and you already have a KDBX you're
happy with, the established apps will probably serve you better today.

### How is this different from Strongbox / KeePassium?

They are more mature, more feature-complete, and fully open source.
If any of those matter to you, pick one of them — they're good
software and have been around longer.

KeeHolder makes two deliberately narrower choices:

- **Smaller trust surface.** No external Swift package dependencies
  in the cryptographic path; Argon2 is vendored at a fixed commit;
  Salsa20 and ChaCha20 are hand-rolled in-tree against published
  test vectors. Less code, no transitive dependency graph to audit.
  Cloud providers (OneDrive, Google Drive, Dropbox) use standard
  OAuth2 PKCE with zero external SDKs.
- **Migration-first.** The shipping focus is getting people off hosted
  password managers, not feature parity with desktop KeePass. The
  migration guides are the product as much as the app is.

If you already live in KeePass on desktop and want feature parity on
iOS today, the established apps are the better fit. KeeHolder is for
people whose journey starts with "I want to leave 1Password."

### What's in v1.0 and what isn't?

In: full read/write KDBX3 and KDBX4, entry and group editing, database
creation, master-password change, save-conflict detection, Face ID /
Touch ID unlock, keyfile support, AutoFill for passwords, live TOTP
codes (with AutoFill on iOS 18+), direct OneDrive / Google Drive /
Dropbox integration, iPad split view, configurable clipboard and
idle-lock timeouts.

Not yet: password health checks, hardware key (YubiKey) support,
AutoFill for passkeys.

### How do I migrate from another password manager?

Use KeePassXC on desktop to convert your existing export to a
`.kdbx` file, then open the KDBX in KeeHolder. KeePassXC's
importers are mature and handle the long tail of edge cases
(custom fields, attachments, TOTP); KeeHolder reads the resulting
KDBX 4 file natively. Step-by-step walkthroughs — including what
survives the import, what may not, and how to clean up exports
safely — live under [`docs/migration/`](../migration/):

- [1Password](../migration/from-1password.md) (`.1pux`, `.1pif`)
- [Bitwarden](../migration/from-bitwarden.md) (encrypted JSON, CSV)
- [LastPass](../migration/from-lastpass.md) (CSV)

### Is it free?

Yes, with one database. KeeHolder is freemium: a one-time purchase
removes the single-database limit and unlocks additional paid
features as they ship. There are no subscriptions, no ads, and no
upsells inside your vault.

### How do I report a security issue?

Privately, via the channels documented in
[`SECURITY.md`](SECURITY.md). GitHub Security Advisories
are the preferred channel. **Please do not file a public issue.**

### How do I trust a binary I didn't compile?

This is the honest version of the answer for v1.0:

- The cryptographic surface is small **by design**. There are no
  external Swift package dependencies in the crypto path; only
  CryptoKit / CommonCrypto / Compression (Apple-provided) and a
  vendored Argon2 at a fixed commit. Salsa20 and ChaCha20 are
  hand-rolled in-tree against published test vectors.
- The cryptographic and database-handling code makes zero network
  calls. This is a property of the design — not something you can
  verify line-by-line in v1.0, since the source is not published.
- Apple's notarization and code signing prevent silent post-install
  modification — but not a compromise at build time.
- There is no source-verification escape hatch in v1.0. The trust
  assumption is: Apple's notarization and signing, plus the design
  posture stated here and in [`SECURITY.md`](SECURITY.md). If your
  threat model can't accept that, v1.0 isn't the right fit — wait
  for the v2.0 third-party audit, or use a fully open-source
  KeePass client like Strongbox or KeePassium.

---

## Get it

- **App Store**: coming soon.
- **Privacy policy**: [`privacy.md`](privacy.md)
- **Security policy**: [`SECURITY.md`](SECURITY.md)
