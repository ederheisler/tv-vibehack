# Decisions

## 2026-10-18: Leave the physical YouTube button as-is

The user explicitly chose to leave the physical YouTube button as-is. This decision is authoritative. Do not reopen it automatically.
Do not add an accessibility service, remap YouTube further, or investigate further without a new user request.

An isolated three-press YouTube test showed HID `0xc0057` events at the hardware layer, but not in foreground Activity dispatch.
The exact system handler remains unconfirmed. This is not a fix or verified native passthrough.
The button can remain inactive in this setup. YouTube remains available in the dock.

The user confirmed Prime → Crunchyroll works from the foreground launcher. The binding handles only keys delivered to the launcher; it is not global.
