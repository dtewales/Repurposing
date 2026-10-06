# Verified Hardware Notes

## Appliance

WatchGuard Firebox M4600

- Hardware model: CL4AE24
- Chassis/part label: 277-M463-001 Rev C
- Board: MB-WG-8771W Ver.2G0

## CPU / memory

- Intel Xeon E3-1275 v3 @ 3.50 GHz
- 8 logical CPUs
- 8 GB RAM

## Storage

Factory:

- CFast 3SE 2 GB
- WD Black WD5000LPLX 500 GB 2.5-inch SATA HDD

Replacement tested:

- Kingston A400 240 GB SATA SSD
- Model: SA400S37/240G

## Networking

Eight onboard Intel I210 copper NICs.

OPNsense / FreeBSD driver names:

```text
igb0
igb1
igb2
igb3
igb4
igb5
igb6
igb7
```

No expansion modules were installed in Bay A or Bay B on the test unit.

## BIOS / firmware

POST / setup details seen during testing:

- AMI Aptio
- Core version 4.6.5.4
- Setup version 2.15.1236
- UEFI 2.3.1 / PI 1.2
- BIOS build date visible in POST: 2016-02-16

## Hardware monitor

- Nuvoton NCT6776F detected by the factory OS
- BIOS exposes system temperature, CPU temperature, voltages and three fan RPM values
- BIOS exposes Manual and SMART FAN IV modes
