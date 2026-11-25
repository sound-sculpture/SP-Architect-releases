# SP Architect 1.0.0-beta.7

Build `hzR9IypT` · 25 November 2026

Desktop **live-set manager and librarian** for the Roland **SP-303**, **SP-404 (OG)**,
**SP-404SX / A**, **SP-404MK2** and **SP-555** — plus the **iPad** editor on TestFlight.

## Downloads

| Platform | File | Notes |
|---|---|---|
| macOS 11+ (Apple Silicon + Intel) | `SP-Architect-macOS-universal.dmg` | Signed with a Developer ID, **notarised and stapled**: it opens without any warning. |
| Windows 10/11 x64 | `SP-Architect-Windows-x64-portable.zip` | Portable, no installer. Not code-signed: SmartScreen shows a warning → *More info → Run anyway*. |

Versioned copies of this build:

```
SP-Architect-macOS-universal_2026-11-25_2121_hzR9IypT-notarized.dmg
SP-Architect-Windows-x64_2026-11-25_2126_hzR9IypT.zip
```

SHA-256 (build `hzR9IypT`):

```
d98c0e59a991f6e25d20eef2f3a05214bb5a4fcff16caa62b5e022c5acc4f755  SP-Architect-macOS-universal.dmg
5954e8d3f28162b28909caedc8eb2445799bb6aba17d42f0e081c357632c8cbc  SP-Architect-Windows-x64-portable.zip
```

## Fixed since 1.0.0-beta.5

### Sending a project to the MK2, and erasing a project

Two bugs, both found and fixed on a real SP-404MK2:

- **A second import in the same session was never acknowledged.** The first
  erase of a session worked; the next one (`send-begin` after a delete, or a
  second send) got no `12` answer and waited until the timeout, leaving the UI
  on *Erasing the target slot…*. The official application (and SparkyMK2) send
  `9D` — leave the previous import session, answer `1D` — **before** a new
  import. The app now does the same, best effort: an older firmware that does
  not answer cannot fail the import. Verified on the machine: four
  `prepare restore` commands in a row, 1.3 s to 2.3 s each, no timeout.
- **A failed send crashed before the rollback.** `backupFiles` was declared
  inside the `try` but read in the `catch`, so any error raised
  `ReferenceError: backupFiles is not defined` and the backup was never
  restored — the target slot stayed empty. Fixed: the rollback runs.

### USB link robustness

- **A timed-out command no longer poisons the stream.** The protocol has no
  request id: abandoning a command left its reply to be consumed by the next
  one, shifting every answer by one (this is what turned a slow read into
  `timeout: status poll`, then into stuck erases). A timeout now waits 1.5 s
  for a late reply, uses it if it lands, and otherwise **closes the link** so
  the next connection starts from a clean session.
- **No automatic resend after a timeout** — resending was exactly what
  desynchronised the replies.
- **Deadlines aligned with the hardware**: 45 s by default (the MK2's storage
  layer legitimately takes 6–13 s for `stat` and `free space`), 120 s for an
  erase, 60 s for a reload.
- **Frame watchdog**: a mis-framed byte could make the parser wait for an
  impossible length, buffering everything that followed (the machine looked
  silent). An incomplete frame older than 3 s is now dropped and the stream
  resynchronises.
- **The inbox no longer grows all session long** (unsolicited notifications
  were kept forever, slowing every wait).

### One instance at a time

macOS lets two processes open the same CDC serial port, and then each one reads
a share of the replies — the MK2 link looks broken for no reason. A second copy
now focuses the running window, and connecting refuses a port already held by
another process (`Another application is using … (PID …)`).

### LOCAL bank changes

Unchanged from beta.5: the status poll runs every **50 ms in LOCAL** (LIVE
keeps 150 ms, where each tick also reads a 327-byte pad block).

## Validated on the machine (macOS, SP-404MK2, USB)

- connect, read the 16 projects;
- delete an empty slot: 2.8 s;
- `send-begin` + a 200 KB file + `send-end`: OK;
- delete again, send again in the same session: OK;
- read a real project (pull): OK.

Please retest a full **SEND TO MK2 OVER USB** with one of your real projects —
that is the only path the app cannot exercise without your material.

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
