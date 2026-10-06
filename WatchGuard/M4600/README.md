# WatchGuard Firebox M4600 → OPNsense

A practical, reversible community conversion guide for repurposing a WatchGuard Firebox M4600 as an OPNsense firewall / lab gateway.

> **Status:** Verified on one WatchGuard M4600 hardware unit (CL4AE24 / MB-WG-8771W Ver.2G0). This is a community project and is not supported by WatchGuard or OPNsense.

## Why this exists

The M4600 is still very capable x86 hardware, but public information on repurposing it is sparse. This project documents a conversion that was completed successfully without BIOS flashing, soldering, bootloader hacks, or modifying the original WatchGuard firmware media.

The useful discovery is simple:

> **With the factory CFast card installed, the appliance prefers that boot path. Remove the CFast and the BIOS can fall through to USB boot. After OPNsense is installed to SATA SSD, the M4600 boots directly from the SSD with no CFast required.**

## Verified result

The following were verified on the test unit:

- AMI Aptio BIOS accessible through serial console.
- BIOS administrator password on this unit: `WatchGuard!`.
- Serial console works at **115200 8N1, no flow control**.
- Factory CFast can prevent USB from becoming the active boot path.
- Removing the factory CFast allows USB boot.
- OPNsense 26.7 boots successfully from USB.
- If the VGA installer image is used, the loader console must be changed from `Dual (Video primary)` to `Serial` on this headless appliance.
- Standard 2.5-inch SATA SSD works as the OPNsense target disk.
- Installed OPNsense boots directly from SATA SSD with the USB and CFast removed.
- All eight onboard Intel I210 NICs enumerate correctly as `igb0` through `igb7`.
- Front-panel port numbering maps cleanly to the OPNsense interface order on this unit.
- Web GUI, WAN and LAN operation confirmed.
- UFS install verified.

## Tested hardware

- WatchGuard Firebox M4600
- Hardware model: `CL4AE24`
- Board: `MB-WG-8771W Ver.2G0`
- BIOS: AMI Aptio / Core 4.6.5.4 / version 2.15.1236
- CPU: Intel Xeon E3-1275 v3
- RAM: 8 GB
- Factory CFast: 2 GB `CFast 3SE`
- Factory HDD: WD5000LPLX 500 GB
- Replacement SSD: Kingston A400 240 GB (`SA400S37/240G`)
- Onboard NICs: 8 × Intel I210 copper

## Quick conversion summary

1. Capture a baseline and photograph the internals.
2. Connect serial console at 115200 8N1, no flow control.
3. Enter BIOS with `DEL` or `ESC`.
4. On the verified unit, BIOS password is `WatchGuard!`.
5. Power down.
6. Remove the factory CFast card.
7. Remove the original WatchGuard HDD if you want to preserve it.
8. Fit a standard SATA SSD.
9. Prefer the OPNsense **serial amd64 image** for this appliance. If you use the VGA image, the loader may default to `Dual (Video primary)` even though the M4600 has no useful VGA output for this workflow.
10. Insert the USB stick and cold boot.
11. At the OPNsense loader, confirm the console mode. If it shows `Dual (Video primary)`, change it to **Serial** before continuing.
12. Boot the live environment and keep the serial session at 115200 8N1, no flow control.
13. Assign LAN/WAN as required.
14. If local serial login is awkward, connect to the live LAN and SSH as `installer`.
15. Install OPNsense to the SATA SSD.
16. Use UFS for a simple single-disk appliance build, unless you specifically want ZFS.
17. Shut down, remove the USB stick, and boot with only the SATA SSD installed.
18. Complete the OPNsense setup wizard and validate all interfaces.

Full procedure: [docs/M4600-OPNsense-Conversion.md](docs/M4600-OPNsense-Conversion.md)

## Important cautions

- **Do not assume every WatchGuard model behaves identically.**
- `WatchGuard!` is confirmed only on the tested M4600 unit.
- Do not flash or modify the BIOS just to install OPNsense.
- Preserve the factory media if rollback, resale, forensic comparison, or recovery matters to you.
- Do not use `F3 Optimized Defaults` casually; document factory settings first.
- Fan behaviour should be baselined before changing cooling hardware.

## Fan baseline from the tested unit

BIOS hardware monitor readings at idle during testing:

- System temperature: 33 °C
- CPU temperature: 54 °C
- Fan 1: 7848 RPM
- Fan 2: 7714 RPM
- Fan 3: 7627 RPM
- Factory fan mode: Manual
- Fan output value: 192

The BIOS also exposes a `SMART FAN IV Mode`. No recommendation to change the factory fan values is made yet; thermal behaviour should be validated under OPNsense first.

## Other WatchGuard models

This guide proves the M4600 path only.

The same general method may be worth testing on other x86 WatchGuard M-Series appliances, but model-specific validation is required. Future candidates include M4800, M5600 and M5800.

The T40W is **not** in the same category: it is an ARM appliance, so this M4600 x86/amd64 OPNsense method should not be expected to transfer directly.

## Project philosophy

**Repurpose, preserve, document.**

Capture the knowledge once so the next person does not have to rediscover the same boot quirks.

## License

MIT. See [LICENSE](LICENSE).
