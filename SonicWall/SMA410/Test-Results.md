# Test results and evidence confidence

## Baseline

- Original SonicWall CFast booted with full normal management menu.
- Serial capture begins with `PS BIOS v001` and proceeds directly to Linux without an observed setup prompt.
- Post-login UI presented setup wizard, reboot, restart SSL VPN services, logout, save TSR, display EULA and SafeMode.

## Alternate CFast tests

- **Empty slot:** 3 audible beeps, then unit powers off.
- **WatchGuard-sourced Innodisk CFast 3SE, 2 GB:** 4 beeps, powers off.
- **Original SonicWall Innodisk CFast 3SE, 4 GB:** boots normally.

No exact manufacturer beep-code map was found or verified. The cards differ by more than security state: capacity, ATA firmware and stored data also vary. **Do not infer a firmware-enforced secure-drive whitelist from these tests alone.**

## Linux VM / StarTech reader tests

Reader: StarTech `CFASTRWU3C`, USB bridge `174c:55aa` (ASMedia family); card appeared as `CFast 3SE`, ~3.7 GiB. The node was `/dev/sdb` during this session, but device nodes are not stable.

Read-only commands used:

```sh
lsblk -o NAME,SIZE,MODEL,TRAN,FSTYPE,MOUNTPOINTS
lsusb
lsusb -t
sudo fdisk -l /dev/sdb
sudo dmesg -T | tail -80
sudo hdparm -I /dev/sdb
```

`fdisk` failed with `Input/output error`. The kernel recorded `Sense Key: Illegal Request`, `Invalid field in cdb`, and a `READ(10)` sector-zero failure. With UASP (`Driver=uas`) active, it failed; after a targeted temporary switch to traditional USB mass storage (`Driver=usb-storage`), it **still failed**. `hdparm -I`, which reads drive identification, worked and disclosed the ATA security lock.

**Do not publish a guessed BIOS password, unlock key, or generic beep interpretation.**

## Explicitly untested

- Installing OpenWrt, OPNsense or Debian to a replacement 4 GB CFast and booting it in the SMA.
- Booting from external USB or spare onboard SATA.
- BIOS modification, SPI programmer read or write, CMOS clearing.
- Verifying the ATA security state from inside a booted SonicWall.
- Reliable recovery/erase of the OEM CFast.

## Outcome

The investigation was intentionally closed after evidence gathering; no functional alternative OS path was found. Do not confuse **untested** with **impossible**.
