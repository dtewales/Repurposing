# Troubleshooting

## USB is detected but does not appear as a boot option

Symptoms:

- BIOS USB page shows the stick as a mass-storage device.
- Changing USB emulation between Auto / Hard Disk does not expose it as a boot target.

Verified M4600 workaround:

1. Power off completely.
2. Remove the factory CFast card.
3. Leave the OPNsense USB inserted.
4. Cold boot again.

On the tested appliance, this allowed USB boot immediately.

## OPNsense boot appears to stop after network initialization

Check the loader console mode first.

If it says:

```text
Cons: Dual (Video primary)
```

change it to:

```text
Cons: Serial
```

On the verified appliance, using the VGA image with `Dual (Video primary)` made the boot appear to stall at the serial console even though the system remained alive and responded to ACPI shutdown.

The M4600 workflow relies on the RJ45 serial console, so the **serial installer image is preferred**. If the VGA image is used, explicitly switch the loader to Serial before booting.

## Live environment reaches the welcome banner but no useful serial login appears

The OS may still be healthy. A short press of the power button producing a graceful ACPI shutdown is a useful clue that the system is alive.

Workaround used successfully:

- connect through the live LAN interface
- SSH to OPNsense
- login as `installer`
- continue installation remotely

## Do I need a replacement CFast boot helper?

Not on the tested M4600.

A replacement CFast / GRUB helper was considered during investigation, but ultimately proved unnecessary. Once the original CFast was removed, USB boot worked and the final OPNsense installation booted directly from SATA SSD.

## USB stick compatibility

Two different USB sticks were detected successfully by BIOS. The decisive issue was not the USB brand or size; it was the presence of the original CFast device in the boot path.
