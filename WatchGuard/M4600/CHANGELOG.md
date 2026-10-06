# Changelog

## v0.1.1 - 2026-10-06

Public-review update.

### Changed

- Clarified that the OPNsense serial amd64 image is preferred for the headless M4600.
- Documented the verified VGA-image quirk where the loader defaults to `Dual (Video primary)`.
- Added the required workaround: switch the OPNsense loader console to `Serial` before continuing.
- Clarified that screenshots showing the VGA installer reflect the tested path, not the recommended default.

## v0.1.0 - 2026-10-06

Initial public-review draft.

### Added

- Verified M4600 hardware inventory.
- BIOS and serial-console notes.
- Confirmed `WatchGuard!` BIOS password for the tested unit.
- Documented USB boot-precedence behaviour caused by factory CFast.
- Documented direct OPNsense installation to SATA SSD.
- Confirmed direct boot from SSD with CFast removed.
- Confirmed all eight Intel I210 interfaces under OPNsense.
- Added live-environment SSH installer workaround.
- Added fan baseline and caution notes.
