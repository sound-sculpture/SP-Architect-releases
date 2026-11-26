# SP Architect 1.0.0-beta.8

Build `zvgqkhUa` · 25 November 2026

Desktop **live-set manager and librarian** for the Roland **SP-303**, **SP-404 (OG)**,
**SP-404SX / A**, **SP-404MK2** and **SP-555** — plus the **iPad** editor on TestFlight.

## Downloads

| Platform | File | Notes |
|---|---|---|
| macOS 11+ (Apple Silicon + Intel) | `SP-Architect-macOS-universal.dmg` | Signed with a Developer ID, **notarised and stapled**: it opens without any warning. |
| Windows 10/11 x64 | `SP-Architect-Windows-x64-portable.zip` | Portable, no installer. Not code-signed: SmartScreen shows a warning → *More info → Run anyway*. |

Versioned copies of this build:

```
SP-Architect-macOS-universal_2026-11-25_2201_zvgqkhUa-notarized.dmg
SP-Architect-Windows-x64_2026-11-25_2206_zvgqkhUa.zip
```

SHA-256 (build `zvgqkhUa`):

```
7d4d78dde77fdd47c063020df84d1ade5f9dfed4fc57e9ed106041162d69b8a6  SP-Architect-macOS-universal.dmg
4ce4b5925422e296e2426e23dc179009ec1303186972f96b768e9433956628da  SP-Architect-Windows-x64-portable.zip
```

## Fixed since 1.0.0-beta.7

### Sending to the MK2: the project switch race (the real cause)

`select project` answers `1A 00` as soon as the request is **accepted**, not when
the project is loaded — and the MK2 loads it after that. The erase command was
sent during that load: the unit ignored it, the UI stayed on *Erasing the target
slot…* for two minutes, then failed with `Timeout: prepare restore`. The same
race broke "delete a project" whenever the unit was on another project.

SparkyMK2 documents the missing signal: the loaded state arrives as the
unsolicited `1A proj 00 00 00` notification, and settings read before it can
still describe the old project. The app now:

- waits for that notification when it comes;
- accepts the short acknowledgement **and confirms by polling the status** when
  the unit is already on the target (it sends no notification in that case);
- gives the storage layer 300 ms before the erase.

Timeouts raised to 45 s for a project switch, on top of the 120 s erase.

### Session recovery and clear errors

- **A leftover import session is cleared on connect** (`FC` + `9D`): a failed
  send used to leave the unit waiting for file data, after which it answered
  nothing at all — only a power cycle recovered it. It is now cleared when the
  app connects, and again before the link is closed after a timeout.
- **Device errors are surfaced immediately** (`32 code`), instead of waiting the
  full timeout for a reply that will never come.
- **Sending or erasing while the MK2 is playing is refused** ("Stop playback on
  the MK2 first") instead of hanging, as SparkyMK2 does.

## Validated on a real SP-404MK2 (macOS, USB)

With a complete card project (311 files, 250 MB, including PTN and PICTURE) sent
into an empty internal slot:

| Step | Result |
|---|---|
| `send-begin` (project switch + erase) | 2.6 s |
| 311 files / 249.9 MB written | 64.7 s (~3.9 MB/s) |
| `send-end` (patterns and pictures restored, project reloaded) | 16.5 s |
| unit reports the new project | `PROJECT_01FF`, 154 samples, 249 743 400 bytes |
| project read back from the unit | OK |
| delete a **non-empty** project while the unit is on another slot | 6.4 s |
| four erase commands in a row, same session | 1.3–2.3 s each |
| read a real project (727 KB) | 1.4 s |

The unit is left exactly as it was found.

## Known limits

- **iPad**: editor and librarian only — no USB link (iPadOS does not allow it).
- **Windows**: the ZIP is not code-signed, so SmartScreen warns on first launch;
  the MK2 USB link there is experimental.
- **SP-404SX/A**: patterns are not exported yet (phase 3).
- **One serial queue**: while the MK2's storage is busy, other commands wait —
  the app pauses the bank follow deliberately and logs which operation held the
  link.

Previous builds stay available from their own release pages.

> The *Source code (zip/tar.gz)* files GitHub attaches to this release contain
> only the documentation of this repository — no application source code. The
> source code is not published.
