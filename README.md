# SP Architect

**Live-set manager and sample librarian for the Roland SP-303, SP-404 (OG), SP-404SX / A, SP-404MK2 and SP-555.**

SP Architect lets you build a set on your computer, edit every pad parameter, convert audio to each machine's proprietary format, and write it to a card folder, a ZIP or a project file. No account, no cloud, no telemetry: everything runs locally.

> Roland and Boss are trademarks of their respective owners. SP Architect is **not affiliated with or endorsed by Roland or Boss**. Always back up your cards before writing anything, and use the app at your own risk.

## Downloads

Binaries are published in the [Releases](../../releases) tab:

- **macOS** — universal DMG (Apple Silicon + Intel). Notarised.
- **Windows** — portable ZIP (x64). Unsigned: Windows shows a warning, click *More info → Run anyway*.
- **iPad** — TestFlight beta (link announced with the first public build).

The source code is not published.

## What it does

Common to every module:

- prepare and organise a set, bank by bank, with notes, tempo and levels;
- edit every pad parameter of the machine (volume, pan, pitch, loop/gate, filter, AHR, routing…);
- import WAV/AIFF/MP3/FLAC, resample and convert to the machine's own format;
- read a real card (SD, CompactFlash, SmartMedia) and open its content;
- export to a card folder, a ZIP, or a self-contained project file.

### SP-404MK2 only

- **LOCAL / LIVE** link over USB: LOCAL keeps the app independent, LIVE mirrors the machine project;
- pattern editor: read, edit, write and export patterns (MIDI, Bounce, MULTIPAD);
- screen editor: startup animation and screen saver, with a library of sets.

## Status

- macOS and Windows: **1.0.0** (beta label for the first public builds).
- iPad: **TestFlight beta** — editor/librarian, no USB link (iPadOS does not allow it).

Known limits:

- SP-404MK2 USB link is unavailable on iPad by design (iPadOS has no CDC-ACM access);
- SP-404SX / A patterns are not implemented (samples and `PAD_INFO.BIN` are);
- Windows builds are not code-signed.

## Credits

Thanks to the beta testers: **Owae.ga (thank you A LOT)**, Vinyl Sim Collective, 97Specials, Annawak, Flanders, Jesus Crise, Just Charly Brown, Mayaewk, Tohaj and Tsy.

Formats and protocol were established from public documentation and our own readings of the machines. No third-party source code is included. See [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md).

## Privacy

No data is collected. See [PRIVACY.md](PRIVACY.md).

## License

Proprietary, free of charge as a binary. See [EULA.md](EULA.md).
