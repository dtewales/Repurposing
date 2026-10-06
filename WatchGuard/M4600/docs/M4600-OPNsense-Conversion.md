# WatchGuard M4600 to OPNsense — Verified Conversion Procedure

## 1. Scope

This document describes a successful conversion of a WatchGuard Firebox M4600 to OPNsense 26.7 using a standard SATA SSD and USB installer.

This is a community procedure. It is not vendor-supported.

## 2. Tested platform

- Model: WatchGuard Firebox M4600
- Hardware model: CL4AE24
- Board: MB-WG-8771W Ver.2G0
- BIOS: AMI Aptio, version 2.15.1236
- CPU: Intel Xeon E3-1275 v3
- RAM: 8 GB
- Factory boot device: 2 GB CFast 3SE
- Factory HDD: WD5000LPLX 500 GB
- Replacement SSD: Kingston A400 240 GB
- NICs: eight Intel I210 copper interfaces

## 3. Serial console

Use:

```text
115200 baud
8 data bits
No parity
1 stop bit
No flow control
```

The appliance exposes an RJ45 serial console.

## 4. BIOS access

During POST, press `DEL` or `ESC`.

The tested unit prompted for an administrator password. On this specific unit:

```text
WatchGuard!
```

worked successfully.

Do not assume the password is universal across all WatchGuard models or revisions.

### Relevant verified BIOS settings

- SATA mode: AHCI
- Legacy USB Support: Enabled
- USB 3.0 Support: Enabled
- XHCI Hand-off: Enabled
- USB Mass Storage Driver Support: Enabled
- Factory boot priority preferred the CFast device

## 5. Why USB initially appeared not to boot

The BIOS detected USB mass storage correctly, including multiple USB sticks, but did not expose them as the active boot path while the factory CFast card was installed.

The decisive test was to power the appliance off and remove the factory CFast card.

After doing so, the M4600 booted the OPNsense USB installer successfully.

This is one of the key findings of the project.

## 6. Storage preparation

A standard Kingston A400 240 GB SATA SSD was fitted in place of the original WatchGuard HDD.

The original CFast and HDD were physically removed during installation to eliminate the risk of overwriting them.

OPNsense / FreeBSD detected:

```text
ada0 = KINGSTON SA400S37240G
```

The USB installer was detected as:

```text
da0 = TDKMedia TF 150 Drive
```

## 7. OPNsense installation media and console mode

Prefer the OPNsense **serial amd64** installer image for the M4600 because the appliance is normally managed through its RJ45 serial console.

If the download ends in `.bz2`, extract it first. Write the resulting `.img` to the USB stick as a raw image.

During the verified conversion, a VGA installer image was also tested. It booted successfully, but the loader defaulted to:

```text
Cons: Dual (Video primary)
```

The M4600 has no useful VGA output for this workflow, so boot output later appeared to stop even though the operating system was still alive. At the loader menu, change the console mode to:

```text
Cons: Serial
```

before continuing.

This is an important installation note: if a screenshot in this project shows the VGA installer, that was part of the verified test path, but **Serial** is the preferred console mode for the M4600.

## 8. Interface discovery

OPNsense detected all eight onboard interfaces cleanly:

```text
igb0  Intel I210
igb1  Intel I210
igb2  Intel I210
igb3  Intel I210
igb4  Intel I210
igb5  Intel I210
igb6  Intel I210
igb7  Intel I210
```

On the tested unit, the interface ordering maps cleanly to the front-panel port order.

Initial assignment used:

```text
LAN -> igb0
WAN -> igb1
```

## 9. Live environment quirk

The live environment booted successfully and displayed the OPNsense welcome banner over serial, but the local serial login prompt did not behave as expected.

The workaround was simple:

1. Connect the live LAN interface to a network where the default OPNsense LAN address is reachable.
2. SSH to the live OPNsense system.
3. Login as `installer`.
4. Start the installer remotely.

This avoided wasting time on a console handoff quirk.

## 10. Filesystem choice

For this single-SSD firewall/lab appliance, UFS was selected.

Reasons:

- simple
- low overhead
- appropriate for a single-disk appliance
- no need for ZFS features for this use case

ZFS remains a valid choice if its features are specifically desired.

## 11. Install target

Select the SATA SSD:

```text
ada0  KINGSTON SA400S37240G
```

Do not select the USB installer device.

The UFS installer completed normally.

## 12. First boot from SSD

After installation:

1. Shut the appliance down cleanly.
2. Remove the USB installer.
3. Leave the factory CFast removed.
4. Boot with only the SATA SSD installed.

Result on the tested unit:

**OPNsense booted directly from the SATA SSD.**

No replacement CFast, GRUB helper, BIOS modification, or bootloader workaround was required.

## 13. First validation

Confirm:

- web GUI loads
- WAN and LAN are present
- all eight interfaces exist
- front-panel mapping is correct
- DNS / gateway behaviour is sane
- serial console still works
- temperatures and fan behaviour are acceptable

## 14. Fan baseline

The original BIOS hardware monitor showed:

```text
System temp : 33 C
CPU temp    : 54 C
Fan1        : 7848 RPM
Fan2        : 7714 RPM
Fan3        : 7627 RPM
```

Factory fan configuration was:

```text
Smart Fan1-3 Mode : Manual Mode
Fan out value      : 192
```

The BIOS also offers `SMART FAN IV Mode`.

Do not reduce fan output blindly. The chassis is designed around directed high-static-pressure airflow. Validate thermals under OPNsense before making fan-control or hardware changes.

## 15. Rollback / preservation

For a reversible conversion, preserve:

- original CFast
- original WatchGuard HDD
- photos of labels and wiring
- BIOS baseline screenshots

If rollback is irrelevant to your own use case, the original media is not required for normal OPNsense operation on the verified unit.

## 16. Known-good outcome

Verified final state:

```text
M4600 BIOS
   ↓
Kingston A400 SATA SSD
   ↓
OPNsense 26.7
```

No CFast required.

## 17. What not to do

- Do not flash the BIOS just to install OPNsense.
- Do not assume USB is unsupported merely because it does not appear in the boot list while CFast is fitted.
- Do not overwrite original media until you have intentionally decided rollback does not matter.
- Do not assume settings or passwords from this M4600 apply to other WatchGuard models.
- Do not replace fans before recording the original thermal and RPM behaviour.
