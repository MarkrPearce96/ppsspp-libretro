# ppsspp-libretro — RetroNest-specific extensions to the upstream PPSSPP
# libretro core (sibling of the upstream `libretro/` directory).
#
# Currently empty. Phase 1 ships the upstream libretro core unmodified.
# RetroNest-specific code — path overrides, host pause/fast-forward
# exports, aspect-ratio plumbing, savestate handshake, controller binding
# format glue — lands here in follow-up phases without touching upstream
# files. See `pcsx2-libretro/pcsx2-libretro/CMakeLists.txt` for the
# corresponding pattern in our PCSX2 fork.
#
# Built only when both `-DLIBRETRO=ON` (upstream's libretro target) AND
# `-DENABLE_RETRONEST=ON` (this sibling) are passed. The gate is the
# 6-line conditional at the bottom of the top-level `CMakeLists.txt`.
