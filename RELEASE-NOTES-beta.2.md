# SP Architect 1.0.0-beta.2 — first public beta

Desktop **live-set manager and librarian** for the Roland **SP-303**, **SP-404 (OG)**, **SP-404SX / A**, **SP-404MK2** and **SP-555**.

## Downloads

| Platform | File | Notes |
|---|---|---|
| macOS 11+ (Apple Silicon + Intel) | `SP-Architect-1.0.0-beta.2-macOS-universal.dmg` | Signed with a Developer ID, **notarised and stapled**: it opens without any warning. |
| Windows 10/11 x64 | `SP-Architect-1.0.0-beta.2-Windows-x64-portable.zip` | Portable, no installer. Not code-signed: SmartScreen shows a warning → *More info → Run anyway*. |

SHA-256 (build `CyZgXbme`):

```
b2222847b9b52ac7dde3918a9658dc11fce84f61905f1bf6986f745ce2062876  SP-Architect-1.0.0-beta.2-macOS-universal.dmg
1b9bc0815f4be1e2e9ba8bf5d98fb0a0fc9dd7a2de8a9a97c927a01706650e48  SP-Architect-1.0.0-beta.2-Windows-x64-portable.zip
```

## What it does

Common to every module:

- prepare and organise a set, bank by bank;
- edit every pad parameter (volume, pan, pitch, loop/gate, filter, AHR, routing…);
- import WAV / AIFF / MP3 / FLAC and convert to the machine's own format;
- read a real card (SD, CompactFlash, SmartMedia) and open its content;
- export to a card folder, a ZIP or a project file.

SP-404MK2 only:

- **LOCAL / LIVE** link over USB;
- pattern editor: read, edit, write, export (MIDI, Bounce, MULTIPAD);
- screen editor: startup animation and screen saver.

Screenshots of every module and the detailed, part-by-part description are in
the [README](https://github.com/sound-sculpture/SP-Architect-releases#part-by-part).

> The *Source code (zip/tar.gz)* files GitHub attaches to this release contain
> only the documentation of this repository — no application source code. The
> source code is not published.

## What changed in build CyZgXbme

- **LIVE / USB**: the first storage read retries once instead of timing out ("readdir"), so the 16 projects appear without restarting the app.
- **BPM**: the load-time scan now decodes lazy samples (MK2 `.SMP`, 555 `.SPD`) and WAV-only cards, so SX/A and 555 pads no longer miss their tempo.

## What changed in build CyZgXbme

- **BPM detected on load** as well as on import: existing cards/projects without a tempo are scanned in the background, one pad at a time (the interface never freezes). Pads that already carry a tempo are left untouched.

## What changed in build BBiHNg2j

- **BPM on every module**: the tempo is detected on import and shown on each pad. The MK2, SX/A, OG and SP-555 store it in their pad table, so it also appears on the machine. The SP-303 has no tempo field.
- **MODULE SWITCH**: switching module with a project loaded no longer closes the app; a failing USB connection is reported instead.
- **LIVE**: "Refresh from MK2" stays available when the project list comes back empty.
- **Diagnostics**: `crash-diagnostics.log` is written in the app data folder.

## Known limits

- Windows builds are **not code-signed**.
- iPad (TestFlight, later) is editor/librarian only: **no USB link**, iPadOS does not allow it.
- SP-404SX / A **patterns** are not implemented yet (samples and `PAD_INFO.BIN` are).

## Before you start

**Back up your cards** (and your projects) before writing anything. Use at your own risk.

Roland and Boss are trademarks of their respective owners. SP Architect is **not affiliated with or endorsed by Roland or Boss**.

## License

Free of charge to use as a compiled binary. The source code is not published and is not distributed in any form; redistribution, resale or reuse requires written permission — see [LICENSE.md](https://github.com/sound-sculpture/SP-Architect-releases/blob/main/LICENSE.md).

## Feedback

Bug reports, card layouts that fail, odd files (long samples, accented names, very big sets) — please open an issue here with the machine, the card type and what you did.

Thanks to the beta testers: Owae.ga (thank you A LOT), Vinyl Sim Collective, 97Specials, Annawak, Flanders, Jesus Crise, Just Charly Brown, Mayaewk, Tohaj, Tsy and Zenover.
