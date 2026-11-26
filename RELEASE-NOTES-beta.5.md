# SP Architect 1.0.0-beta.5

Build `DsEdNY3N` · 25 November 2026

Desktop **live-set manager and librarian** for the Roland **SP-303**, **SP-404 (OG)**,
**SP-404SX / A**, **SP-404MK2** and **SP-555** — plus the **iPad** editor on TestFlight.

## Downloads

| Platform | File | Notes |
|---|---|---|
| macOS 11+ (Apple Silicon + Intel) | `SP-Architect-macOS-universal.dmg` | Signed with a Developer ID, **notarised and stapled**: it opens without any warning. |
| Windows 10/11 x64 | `SP-Architect-Windows-x64-portable.zip` | Portable, no installer. Not code-signed: SmartScreen shows a warning → *More info → Run anyway*. |

Versioned copies of this build:

```
SP-Architect-macOS-universal_2026-11-25_2022_DsEdNY3N-notarized.dmg
SP-Architect-Windows-x64_2026-11-25_2027_DsEdNY3N.zip
```

SHA-256 (build `DsEdNY3N`):

```
dab4eee0a506c2ad51a6e006039d77c22cdd0def107b6e18c67f76e1cc3d18f7  SP-Architect-macOS-universal.dmg
8ff1e12a8a46373eadf96c661b4ae04ead39488d2d3dc1d5a2e77928d1a930c0  SP-Architect-Windows-x64-portable.zip
```

## Fixed since 1.0.0-beta.4

### Sending a project to the MK2, and erasing a project

A command that timed out was abandoned, but its reply still arrived later — and
the *next* command consumed it. From that point on every reply was shifted by
one, which is what made "SEND TO MK2 OVER USB" fail with a timeout and then stay
stuck on *Erasing the target slot…*, and what made a project deletion erase
correctly but never finish. Three changes:

- **A timeout no longer abandons the answer.** When a command passes its
  deadline, the link waits 1.5 s more for a late reply and uses it if it lands;
  only then does it resynchronise. The default deadline is now 45 s instead of
  6 s — the MK2's storage layer legitimately takes 6–13 s for `stat` and
  `free space`.
- **The connection no longer probes free space.** That 6–12 s storage read sat
  in the connect step and could leave a reply in flight. The storage view asks
  for free space on its own, once the link is up.
- **A second attempt no longer queues behind the first one.** While a send is
  starting or erasing, another one is refused with "A transfer is already in
  progress" instead of piling commands behind a slow erase.

### LOCAL bank changes

The status poll now runs **every 50 ms in LOCAL** (it was 150 ms): a status
command is 3 bytes and takes about 4 ms, so a bank change on the machine shows
up almost immediately. LIVE keeps the slower 150 ms cadence, because each LIVE
tick also reads the 327-byte pad block, and 20 of those per second disturbed the
machine's own pads.

### Diagnostics

Control commands are now logged when they take more than a second
(`prepare restore 42000ms`, `reload project 61000ms`), and a failure is logged
with its duration. A report will name the exact step that held the link.

## Known limits

- **iPad**: editor and librarian only — no USB link (iPadOS does not allow it).
- **Windows**: the ZIP is not code-signed, so SmartScreen warns on first launch;
  the MK2 USB link there is experimental.
- **SP-404SX/A**: patterns are not exported yet (phase 3).
- **One serial queue**: while the MK2's storage is busy, other commands wait —
  the app now pauses the bank follow deliberately and logs which operation held
  the link.

Previous builds stay available from their own release pages.

> The *Source code (zip/tar.gz)* files GitHub attaches to this release contain
> only the documentation of this repository — no application source code. The
> source code is not published.
