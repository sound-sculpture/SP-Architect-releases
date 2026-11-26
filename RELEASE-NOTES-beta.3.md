# SP Architect 1.0.0-beta.3

Build `BVfBf7Y_` · 25 November 2026

Desktop **live-set manager and librarian** for the Roland **SP-303**, **SP-404 (OG)**,
**SP-404SX / A**, **SP-404MK2** and **SP-555** — plus the **iPad** editor, now on TestFlight.

## Downloads

| Platform | File | Notes |
|---|---|---|
| macOS 11+ (Apple Silicon + Intel) | `SP-Architect-macOS-universal.dmg` | Signed with a Developer ID, **notarised and stapled**: it opens without any warning. |
| Windows 10/11 x64 | `SP-Architect-Windows-x64-portable.zip` | Portable, no installer. Not code-signed: SmartScreen shows a warning → *More info → Run anyway*. |

The two files above are always the current build. The versioned copies of this
build are attached as well:

```
SP-Architect-macOS-universal_2026-11-25_1838_BVfBf7Y_-notarized.dmg
SP-Architect-Windows-x64_2026-11-25_1843_BVfBf7Y_.zip
```

SHA-256 (build `BVfBf7Y_`):

```
eae96909f5c0444868072dbeddd4f673b49450f8ce907e6d981e246715f15ae4  SP-Architect-macOS-universal.dmg
7727f522c28a481e0d4e06af236b086e926f1dcea87983d260141f238626f153  SP-Architect-Windows-x64-portable.zip
```

## New since 1.0.0-beta.2

- **Version display fixed.** `About`, the Finder *Get Info* panel and the
  settings footer now report `1.0.0-beta.3` instead of the previous beta number.

- **iPad — sound comes back.** The audio session is re-armed when the app
  returns to the foreground, after an interruption (a call, another app taking
  the output), after a route change, and after iOS resets the media services.
  The engine is restarted whenever a graph change stopped it, so a pad is
  audible again after a pattern.
- **ZIP import — no practical size limit.** A card or project ZIP that is not
  one of our own stored exports is now extracted by a streaming reader into the
  app's temporary folder (never next to the archive), then imported entry by
  entry. Archives above 600 MB used to be refused on Windows; that limit is
  gone. Our own exports are still read in place, without any extraction.
- **LOCAL — bank changes from the MK2.** The background tempo scan and the
  sample pre-decode now wait for the interface to catch up after a hardware
  bank change, instead of competing with it. Timing lines are written to
  `mk2-timings.log` (`bank … gap=…ms poll=…ms`, `bank-ui … ipc=…ms apply=…ms`)
  so any remaining latency can be measured from a tester's report.

Everything else is unchanged: SP-303, SP-404 OG, SP-404SX/A, SP-404MK2 and
SP-555 modules, card analysis, Merge / Keep card / Move, export to a card
folder, a ZIP or a project file, and the MK2 pattern and screen editors.

## Known limits

- **iPad**: editor and librarian only — no USB link, because iPadOS does not
  allow serial access to the sampler.
- **Windows**: the ZIP is not code-signed, so SmartScreen warns on first launch.
- **SP-404SX/A**: patterns are not exported yet (phase 3).
- **LIVE display**: the app follows the machine's parameters with roughly
  0.3 s of latency by design, to leave the MK2 enough air while it plays.

The previous build, **1.0.0-beta.2** (`CyZgXbme`), stays available from its own
release page: nothing is removed when a new beta ships.

> The *Source code (zip/tar.gz)* files GitHub attaches to this release contain
> only the documentation of this repository — no application source code. The
> source code is not published.
