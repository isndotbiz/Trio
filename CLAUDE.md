# Trio (trio-dev checkout)

## What this is

This checkout is a **clean, unmodified clone of upstream `nightscout/Trio`**, `dev`
branch. `HEAD` currently matches `origin/dev` exactly with zero local commits and a
clean working tree — this is not (yet) a customized fork, just a working copy for
building/testing the upstream app as-is. If you start making local changes, update
this section to describe what's actually different from upstream.

Trio itself is an open-source automated insulin delivery (AID) iOS app built on the
OpenAPS algorithm (see `trio-oref/` submodule), descended from FreeAPS X / iAPS and
built on the LoopKit framework family. Full project background: `README.md`,
https://triodocs.org/.

## Stack

- Swift, Xcode project (`Trio.xcodeproj` / `Trio.xcworkspace`)
- Structured as many local Swift packages/kits (one per pump/CGM integration):
  `LoopKit`, `LoopAlgorithm`, `CGMBLEKit`, `G7SensorKit`, `LibreTransmitter`,
  `OmnipodKit`, `MinimedKit`, `DanaKit`, `MedtrumKit`, `RileyLinkKit`,
  `dexcom-share-client-swift`, `TidepoolService`, etc. — largely git submodules
  (see `.gitmodules`)
- `Trio Watch App` / `Watch App Extension` / `Watch Complication` — watchOS targets
- Ruby/Fastlane (`Gemfile`, `fastlane/`) for CI/build automation
- `trio-oref/` — the OpenAPS algorithm core (JS, adapted for Trio)

## Build

```bash
git clone --branch=<branch> --recurse-submodules https://github.com/nightscout/Trio.git
echo 'DEVELOPER_TEAM = xxxxxxxxxx' > ConfigOverride.xcconfig   # your Apple Developer ID
xed .                                                          # open in Xcode and build
```

Non-Xcode build path (GitHub Actions / Fastlane): see `fastlane/testflight.md` on
the relevant branch, or `triodocs.org/install/build/browser/`.

## Directory notes

- `Trio/` — the main app target source
- `TrioTests/` — unit tests
- `Model/` — shared data model
- `LiveActivity/` — iOS Live Activity widget extension
- `BuildTools/`, `Config.xcconfig`, `BuildDetails.plist` — build configuration
- `patches/`, `scripts/` — build-time patches and helper scripts
- `blacklisted-versions.json`, `oref0_source_version.txt` — pinned/blocked version
  metadata for the algorithm core

## Invariants / gotchas

- **This is a medical device app.** Per `README.md`: "not CE or FDA approved for
  therapy," used at the user's own risk. Treat algorithm and dosing-adjacent code
  (`trio-oref/`, `LoopAlgorithm/`) with corresponding care.
- Heavy submodule structure — `--recurse-submodules` is required on clone/pull, and
  most of the pump/CGM kits live in their own repos.
- Two branches matter upstream: `main` (release) and `dev` (active development) —
  this checkout tracks `dev`.
