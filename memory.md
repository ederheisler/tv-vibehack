# Project memory

Read this file at the start of every session and before updating upstream code or installing the launcher.

## ADB connection prerequisites

Validated on 2026-10-22: the existing trusted Python helper connected through classic TCP ADB on port `5555`; `getprop ro.product.model` returned `Smart TV Pro`. Use the current verified TV address as `TV_HOST`, not an assumed old address:

```sh
uv run --with adb-shell==0.4.4 python ~/tv-customization/tools/tv_adb.py --host "$TV_HOST" shell 'getprop ro.product.model'
```

This uses the existing trusted key without displaying it. Port `5555` is distinct from Wireless debugging's dynamic TLS endpoint. Check this known-working path before replacing it with a relay or a different ADB mode.

Earlier attempts returned `No route to host` for LAN TCP while internet TCP worked and the TV answered IPv6 ping. After a successful retry, Eder confirmed that the culprit was an outdated Herdr app. Check the session host as well as ADB when diagnosing this pattern; changing ADB clients or adding relays did not fix this incident. The exact Herdr version and correction steps were not captured. No TV installation or settings change was performed by this connection check.

If classic TCP ADB is unavailable and Wireless debugging is needed, discover its endpoint before connecting (`adb mdns services`; on macOS also try `dns-sd` for `_adb-tls-connect._tcp`). If discovery finds no TV, promptly ask Eder to enable Wireless debugging on the TV; do not assume it is on just because the TV and launcher are on. Retry discovery after he enables it. If still undiscovered, ask for the address and port shown on the Wireless debugging screen, and pairing details only if required.

Own long-running helpers as named, non-blocking background services; do not make Eder run a relay when the agent can run it. A relay only helps if its process can reach the TV: an ordinary-terminal `No route to host` is evidence to diagnose the underlying network path, not to repeat the same relay workaround.

## Personal configuration — preserve across updates

Eder confirmed on 2026-10-22 that these are required configuration-only personalizations, not custom launcher code:

- Dock order: `com.google.android.youtube.tv`, `com.crunchyroll.crunchyroid`, `com.spotify.tv.android`, `com.google.android.youtube.tvmusic`.
- Hidden apps: `com.tcl.dashboard`, `com.tcl.tv`.
- Live TV package: `com.tcl.tv`.
- Optional launcher features, explicitly selected by Eder (items 1–6 of the offered checklist) with a 5-minute Home-only idle clock: `app_library=true`, `tile_menu=true`, `focus_overshoot=false`, `home_idle_clock_minutes=5`, `dock_glass=true`, `app_glow=true`.

Preserve these optional options on future updates, like the settings above. Status: recorded in `personal/tcl-g15/config.json`, deployed to the TV and verified on the TV, including the 5-minute Home-only idle clock (see deployment status below).

Keep these through upstream's supported configuration mechanism. Do not replace them with upstream defaults or require a Java fork. Before installing an update, check the release migration guidance and verify that the configuration being deployed retains these exact values. If preservation cannot be verified, stop before installation and explain the missing prerequisite.

Use the theme overlay documented in `themes/README.md`, deployed with `tools/theme.sh <folder-containing-config.json>`. The tracked source of truth is `personal/tcl-g15/config.json`; deploy with `tools/theme.sh personal/tcl-g15`. If it is missing or differs from the exact values above, restore it from this file before any installation. Read every release note between the previous upstream base and the target, not just the latest release. Do not use `--reset` or replace this overlay with a stock theme unless the required settings are preserved. A theme push resets on-TV layout/motion choices and replaces wallpaper files, so inspect those before pushing.

## Retired customization

Eder explicitly retired the custom foreground button handler and Prime-button-to-Crunchyroll mapping on 2026-10-22: unused and not worth maintaining. Do not restore that handler, its paired resource arrays, or an equivalent remapping without a new request. This retirement does not apply to the dock, hidden apps or tuner configuration above.

## Configuration location and deployment status

On 2026-10-22 the TV was updated in place from launcher 2.0 to 2.6 (versionCode 7, targetSdk 34), with matching signing certificates and no uninstall or data clearing. The tracked overlay `personal/tcl-g15/config.json` was deployed through the supported ThemeProvider and read back to verify the dock order, hidden apps and TCL tuner. No prior overlay, launcher wallpaper or layout/motion overrides existed; no `theme_revision` reset was needed.

The launcher remained default HOME. Real-TV screenshots verified Home, Down navigation into the grid and the Inputs dropdown; input switching, physical buttons and reboot behavior were not retested. Disabled packages and enabled accessibility services were unchanged. No optional feature or retired button remapping was enabled during that initial upgrade (historical). Rollback APK, preferences, system baseline and screenshots are private in `~/tv-customization/backup/pre-upstream-update-20261022/`.

Later on 2026-10-22 the six optional keys (`app_library=true`, `tile_menu=true`, `focus_overshoot=false`, `home_idle_clock_minutes=5`, `dock_glass=true`, `app_glow=true`) were deployed and read back from the TV unchanged, with the original dock, hidden apps and tuner retained. Real-TV screenshots visually verified the glass dock, app-coloured focus, the long-press tile menu and the App library (13 apps). No menu actions were taken and no apps were changed. Disabled-package and accessibility-service lists matched the backup after deployment.

The first attempt to verify the 5-minute Home-only idle clock did not complete: a timed Python screenshot timed out and native ADB then reported the device offline. After the TV was ready and native ADB reconnected, the retry succeeded. A fresh D-pad movement began the idle observation, with no further keys for 305 seconds. The native screenshot `options-1-6-idle-clock-retry.png` visually showed a full-screen clock and date with a blue ambient glow. One `KEYCODE_DPAD_RIGHT` then dismissed it: `options-1-6-clock-dismissed.png` visually showed Home with Crunchyroll still focused and no app opened. System `screen_off_timeout=600000` and `sleep_timeout=1139000` were read only and not changed. All six selected options are therefore deployed and verified on the TV. Reboot behavior and physical remote buttons were not tested.

On 2026-10-22, after being told that classic TCP ADB on port `5555` is plaintext and exposed on the LAN, and being asked to close it at the end of the session, Eder said "do not disable debugging on the tv". For this session, TV debugging is left enabled: no debugging was turned off, no authorisations were revoked and no reboot was done as cleanup. Eder gave no reason. This applies to this session only and is not a standing policy.

On 2026-10-22 Eder asked to "try 7", the Cinematic Home option. `"cinematic_home": true` was added to `personal/tcl-g15/config.json` as a user-requested trial, then deployed to the TV and visually verified. The on-TV JSON read-back equals the previous overlay plus only `cinematic_home: true`, and the source config matches the deployed JSON exactly. The screenshot `cinematic-home-trial.png` showed an abstract red/gray colour wash across the upper screen, a larger app name and dock-position dots. After one injected `DPAD_RIGHT`, `cinematic-home-crunchyroll.png` showed an orange colour wash, a larger Crunchyroll title and the second dock-position dot selected. No app launched, the other settings were preserved and debugging was untouched. This is still a trial, not a record of permanent approval by Eder.

Correction: an earlier version of this note described the upper area as softened YouTube artwork. That overstated it. Eder pointed out that the screenshots show colour washes, not identifiable artwork. This is the expected result, not a deployment failure: `HeroArt.java` (lines 16 and 35-46) reduces the banner to 48x27, saturates it, applies three paired box-blur passes and stretches it.

For subsequent updates, inspect the existing overlay and on-TV choices first. If the overlay already matches the tracked source, preserve it rather than re-pushing a theme and resetting choices. Checkout configuration changes alone do not update the TV.
