# SP Architect 1.0.0-beta.4

Build `BFNwb7I6` · 25 November 2026

Desktop **live-set manager and librarian** for the Roland **SP-303**, **SP-404 (OG)**,
**SP-404SX / A**, **SP-404MK2** and **SP-555** — plus the **iPad** editor on TestFlight.

## Downloads

| Platform | File | Notes |
|---|---|---|
| macOS 11+ (Apple Silicon + Intel) | `SP-Architect-macOS-universal.dmg` | Signed with a Developer ID, **notarised and stapled**: it opens without any warning. |
| Windows 10/11 x64 | `SP-Architect-Windows-x64-portable.zip` | Portable, no installer. Not code-signed: SmartScreen shows a warning → *More info → Run anyway*. |

The two files above are always the current build. The versioned copies of this
build are attached as well:

```
SP-Architect-macOS-universal_2026-11-25_1912_BFNwb7I6-notarized.dmg
SP-Architect-Windows-x64_2026-11-25_1918_BFNwb7I6.zip
```

SHA-256 (build `BFNwb7I6`):

```
5ca192bba6fa30a177401c69de201de8b28502eb9892691c28797f9e5a57451a  SP-Architect-macOS-universal.dmg
bdf8228a7efe184347bce5594ff306d8d0ead598c006193e0456866f97dc4b4a  SP-Architect-Windows-x64-portable.zip
```

## New since 1.0.0-beta.3

Everything from beta.3 is included (iPad audio fix, ZIP import without a size
limit, LOCAL bank-follow improvements). This build fixes the MK2 USB link:

- **"timeout: status poll" when sending a project to the MK2** — the free-space
  probe used to run inside the connection step. On a busy card it takes 6–12 s,
  and when it timed out it closed a link that was healthy. Worse, the late reply
  stayed in the queue and was handed to the *next* command, which then waited
  for a reply that had already been consumed. The probe is now optional and
  time-limited, and a timed-out command clears the reply queue so the session
  resynchronises instead of cascading.
- **Bank follow no longer competes with a transfer.** The background status
  poll kept running during the (minutes-long) backup that precedes a send, a
  restore or a pull. It now pauses for as long as bytes flow and resumes by
  itself; a cancelled dialog no longer leaves a long busy window behind.
- **Sending to the MK2 does not abort on a slow storage probe anymore** — if
  free space cannot be read in time, the write is attempted and the device
  reports a real error only if the slot is truly full.
- **`mk2-timings.log` names the culprit.** A bank change now logs
  `bank … gap=…ms poll=…ms blocked=<operation> <ms>`, so a slow follow can be
  attributed to the storage call that held the shared USB queue.
- **Version display.** `About`, the Finder *Get Info* panel and the settings
  footer report `1.0.0-beta.4`.

## What the first field log showed

On a LOCAL session (build `BVfBf7Y_`): the interface is no longer the
bottleneck — `apply` was 0–10 ms and `ipc` 0–1 ms. The remaining delays came
from the device's storage layer holding the single USB queue:
`stat 12606ms`, `free space 6606ms`, giving poll gaps of 8.7 s and 30 s.

## Known limits

- **iPad**: editor and librarian only — no USB link, because iPadOS does not
  allow serial access to the sampler.
- **Windows**: the ZIP is not code-signed, so SmartScreen warns on first launch.
- **SP-404SX/A**: patterns are not exported yet (phase 3).
- **LIVE display**: roughly 0.3 s of latency by design, to leave the MK2 enough
  air while it plays.
- **One serial queue**: while the MK2's own storage is busy (a large card
  listing, a backup), the bank follow still waits for that operation to end.
  The app now pauses it deliberately and logs who held the link.

Previous builds stay available from their own release pages.

> The *Source code (zip/tar.gz)* files GitHub attaches to this release contain
> only the documentation of this repository — no application source code. The
> source code is not published.
