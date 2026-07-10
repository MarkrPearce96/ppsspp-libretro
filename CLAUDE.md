# ppsspp-libretro — Claude Instructions

PPSSPP fork with the upstream libretro backend adapted for RetroNest,
loaded in-process. Branch: `main`, remote `origin` =
`markrpearce96/ppsspp-libretro` (private, standalone — NOT a GitHub fork).

## Build + arch policy
Universal core. Local build dirs: `build-libretro` (arm64),
`build-arm64-libretro`, `build-universal-libretro`. x86_64/universal CMake
invocations MUST use `arch -x86_64 /usr/local/bin/cmake` where an x86 slice
is built — bare `arch -x86_64 cmake` resolves to the arm64 Homebrew cmake
and dies with "Bad CPU type". Never pipe build output (masks the exit
status).

Deploy = copy the universal `ppsspp_libretro.dylib` to
`~/Documents/RetroNest/emulators/libretro/cores/`. The core's bundled
assets ship as `cores/ppsspp_libretro_resources/PPSSPP/` (fonts, flash0,
compat.ini, lang) — RetroNest points `system_dir` there; missing assets
don't fail boot, they corrupt fonts and silently disable compat hacks.

## Releases (CI)
`.github/workflows/libretro_release.yml` on tags (`v2026.MM.DD[.n]`)
builds a **universal**, self-contained core and zips the resources tree.

## Contract package
Not yet adopted here (deferred until the fork's scaffold gains
RetroNest-specific code) — the core speaks plain libretro plus the private
env commands it already handles. When adoption happens, vendor via
`RetroNest-Project/vendor/retronest-libretro/sync.sh` like the other forks.

## Settings options
RetroNest renders its PPSSPP settings pages FROM this core's declared
options (`libretro/libretro_core_options.h` → `SET_CORE_OPTIONS_V2_INTL`).
Changing option keys/values/defaults here flows into RetroNest
automatically after a rebuild + re-probe. Two upstream key typos
(`ppsspp_mulitsample_level`, `ppsspp_hardware_tesselation`) are load-bearing
— renaming them is a schema-breaking change, don't.

## Updating from upstream (carries patches — a sync is real work)
Unlike the stock `mgba-libretro` mirror, this fork carries RetroNest source
patches, so an upstream sync **can conflict**. `upstream` = `hrydgard/ppsspp`,
branch `main`, release arch **universal**.
```sh
git fetch upstream
git merge upstream/master        # resolve conflicts where upstream touched
                                 # the same code as our patches
# REBUILD LOCALLY + TEST IN RETRONEST — not just "compiles": confirm rendering,
# audio, settings schema still work. Watch the two load-bearing upstream key
# typos (see Settings options) — don't let a sync "fix" them.
git push origin main
git tag v2026.MM.DD && git push origin v2026.MM.DD   # CI rebuilds + republishes
```
Only sync when you actually want an upstream fix/feature — each sync costs
conflict-resolution + a full retest. Upstreaming patches (below) shrinks this.

## Branches worth knowing
- `osd-options` — preserved pre-review OSD work.
- `upstream-pr-*` — branches staged for upstream PRs; submit via the TRUE
  fork `prfork` = `MarkrPearce96/ppsspp` (e.g. audio thread-safety
  #21883). Pushing upstream-derived branches to THIS repo triggers
  upstream's CI workflows here — cancel those runs.
