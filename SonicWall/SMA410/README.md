# SonicWall SMA 410 — Repurposing Investigation (Retired)

> **Outcome: research concluded; alternative OS boot NOT demonstrated.** This is a reproducible record of unsuccessful boot investigations, **not an installation guide**. Final research release: **v1.0.0 — 2026-10-27**.

## Purpose and decision

Investigated giving the end-of-life SonicWall SMA 410 a second life using a supported open-source operating system, preferably without a motherboard BIOS flash. Stopped when likely effort escalated into firmware/storage-security reverse engineering without a demonstrated alternate boot path. **The appliance is not confirmed impossible to repurpose**; the tested path simply did not succeed. Do not run obsolete SMA firmware exposed to untrusted networks.

**Decision:** retire the units from the project and dispose of; separately assess whether a removed CFast card may be securely sanitised. No working OpenWrt/OPNsense/Debian installation is claimed.

## Quick facts

| Category | Evidence-backed observation |
|---|---|
| Appliance | SonicWall SMA 410 |
| Main PCB | Marked `MB-7532 V2.0`, assembly sticker `MB-7532R` |
| CPU | Intel Atom C2758, 8 cores at 2.4 GHz (serial boot log) |
| RAM | 2 × 4 GB Innodisk DDR3-1600 ECC DIMMs (photographs) |
| Boot flash | Innodisk CFast 3SE 4 GB, SATA interface, ATA firmware `S130710S` |
| Other storage connections | Two unpopulated SATA data connectors observed; independent boot ability and power not tested |
| BIOS indicators | AMI Aptio sticker; early serial text `PS BIOS v001` |
| Management | RS-232 console at 115200 baud, 8N1; restricted management menu after login |
| Display | ASPEED AST1400 chip observed; **no external VGA port** on tested chassis |
| Expansion | PCIe-style board edge connection; **pinout/function unverified** |
| SMA version shown | `10.2.2.3-93sv`; SafeMode `5.0.0.13` (management screenshot) |

## Key discovery: ATA security on the OEM CFast

Outside the SonicWall, the original CFast enumerates in Linux but raw sector reads fail. `hdparm -I` reports ATA Security **supported, enabled, locked, not frozen, level Maximum**. This adequately explains the disk I/O failures without invoking a proprietary filesystem or hardware failure. A 2 GB Innodisk CFast 3SE formerly used with a WatchGuard reported security **not enabled, not locked** and was readable via the same reader.

Whether SonicWall firmware **requires** a locked card or automatically supplies a password during boot remains **unproven**. Multiple variables differ between these cards, including capacity, firmware revision and existing contents. Beep counts are observed behaviour, **not decoded diagnostics**.

## Test summary

| Setup / action | Observation | What it does and does not prove |
|---|---|---|
| Original SonicWall 4 GB card | Normal boot, including after it was removed and reinserted | Known working original configuration |
| CFast card removed | 3 beeps followed by shutdown | Reason for shutdown unknown |
| WatchGuard 2 GB CFast installed | 4 beeps followed by shutdown | Rejected in this configuration; cause unknown |
| Del, F2, F11, Esc over serial | No BIOS setup access | Not evidence that every BIOS access route is impossible |
| SonicWall serial administrator login | Restricted menu: Setup Wizard, Reboot, Restart SSL VPN Services, Logout, Save TSR to Flash, Display EULA, Boot to SafeMode | No generic Linux shell in exposed menu |
| SafeMode | Known to operator as firmware recovery/staging | Not identified as a shell or alternate OS boot method |
| StarTech CFASTRWU3C with Linux VM | Card enumerated; `fdisk` failed to read sector 0 | At first, storage/transport error |
| USB driver changed `uas` to `usb-storage` | Same I/O read failure | Switching away from UASP did not solve it |
| `hdparm -I` on OEM card | Locked / Maximum ATA security | **Confirmed** security feature in operation |
| Standard OpenWrt flashed to card | **NOT TESTED** | Do not claim OpenWrt incompatible |
| BIOS flash / SPI programming | **NOT ATTEMPTED** | No modified firmware or restoration procedure established |

## Why stop here?

The goal was practical, low-effort reuse of retired hardware, not a weeks-long firmware reverse-engineering exercise. No unmodified-firmware alternate-OS boot path was demonstrated; the OEM drive is password-locked and the serial menu provides no shell. The hardware remains potentially reusable, but the engineering return was too uncertain to justify further time.

## Contents

- [Hardware inventory](Hardware.md)
- [Test evidence and limits](Test-Results.md)
- [CFast security and possible recovery](CFast-Recovery.md)
- [Source/evidence publication policy](Evidence-and-Privacy.md)
- [Images](Images/)
- [Changelog](CHANGELOG.md)

## Sources and reproducibility

Derived from physical photographs; serial boot logs captured from the test appliance; read-only `lsusb`, `lsusb -t`, `fdisk`, `dmesg`, `hdparm -I` and physical replacement-card tests. Identifiers which could expose a work device or network have been excluded. Commands and conditions are documented; **this is not an assertion of universal behaviour across SMA 410 revisions**.

## Future contribution welcome

Document a verified, *reversible* alternative OS boot path, or a reproducible method for identifying where ATA unlock occurs, with PCB revision, storage details, serial evidence and risk warnings. Avoid undocumented BIOS flashes, private credentials and assumptions about beep meanings.

**Status:** CLOSED / RETIRED — 2026-10-27.
