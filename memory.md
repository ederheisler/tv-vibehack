# Project memory

Read this file at the start of every session and before updating upstream code or installing the launcher.

## Wireless ADB prerequisite

Discover the TV's ADB endpoint before connecting (`adb mdns services`; on macOS also try `dns-sd` for `_adb-tls-connect._tcp`). If discovery finds no TV, promptly ask Eder to enable Wireless debugging on the TV; do not assume it is on just because the TV and launcher are on. Retry discovery after he enables it. If still undiscovered, ask for the address and port shown on the Wireless debugging screen, and pairing details only if required.

## Personal configuration — preserve across updates

Eder confirmed on 2026-10-22 that these are required configuration-only personalizations, not custom launcher code:

- Dock order: `com.google.android.youtube.tv`, `com.crunchyroll.crunchyroid`, `com.spotify.tv.android`, `com.google.android.youtube.tvmusic`.
- Hidden apps: `com.tcl.dashboard`, `com.tcl.tv`.
- Live TV package: `com.tcl.tv`.

Keep these through upstream's supported configuration mechanism. Do not replace them with upstream defaults or require a Java fork. Before installing an update, check the release migration guidance and verify that the configuration being deployed retains these exact values. If preservation cannot be verified, stop before installation and explain the missing prerequisite.

Use the theme overlay documented in `themes/README.md`, deployed with `tools/theme.sh <folder-containing-config.json>`. The tracked source of truth is `personal/tcl-g15/config.json`; deploy with `tools/theme.sh personal/tcl-g15`. If it is missing or differs from the exact values above, restore it from this file before any installation. Read every release note between the previous upstream base and the target, not just the latest release. Do not use `--reset` or replace this overlay with a stock theme unless the required settings are preserved. A theme push resets on-TV layout/motion choices and replaces wallpaper files, so inspect those before pushing.

## Retired customization

Eder explicitly retired the custom foreground button handler and Prime-button-to-Crunchyroll mapping on 2026-10-22: unused and not worth maintaining. Do not restore that handler, its paired resource arrays, or an equivalent remapping without a new request. This retirement does not apply to the dock, hidden apps or tuner configuration above.

## Configuration location and deployment status

The tracked overlay `personal/tcl-g15/config.json` contains the required settings. This file alone does not mean it has been applied to the TV. Verify actual deployment separately; do not claim checkout changes update the installed launcher. No TV installation or configuration push occurred during the 2026-10-22 upstream update discussion.
