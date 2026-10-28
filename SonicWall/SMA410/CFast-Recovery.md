# OEM CFast 4 GB — retention, security and possible reuse

**Status: password-locked and NOT yet reusable.** This appendix does not contain an erase script because the necessary password has not been obtained and the success/safety of erasing via the present USB bridge has not been established.

## Observed state

| Field | Original SonicWall CFast | WatchGuard comparison card |
|---|---|---|
| Capacity | 4 GB | 2 GB |
| Model | Innodisk CFast 3SE | Innodisk CFast 3SE |
| ATA firmware | `S130710S` | `S130710` |
| Security feature set | Supported | Supported |
| Security enabled | **Yes** | No |
| Security locked | **Yes** (after power connection to reader) | No |
| Security level | **Maximum** | Not applicable |
| Normal raw sector read | Rejected | Readable |

The `hdparm -I` command is a read-only IDENTIFY query; it can work on locked drives when normal sector reads fail. The earlier read errors were consistent with ATA security blocking access. Swapping USB drivers did not remedy them.

## Supported ATA security distinctions

- With the **correct user password**, standard ATA commands can unlock the device, and security can be disabled; preserve secure handling of the password.
- With **security level Maximum**, the **correct master password**, if known, may allow a **destructive ATA SECURITY ERASE UNIT** operation. It cannot ordinarily reveal the existing data by unlocking it.
- With **neither password**, normal `fdisk`, `dd`, formatting and OS flashing cannot override an active ATA lock. Neither the `65534` master-password revision code nor the device model is a usable password.
- Blind guessing can exhaust an ATA unlock attempt counter. **Do not brute-force.**
- ATA SECURITY ERASE is **not interchangeable** with writing zeros using `dd`; any erasure must be independently verified after a full power cycle.

## Low-risk inspection procedure (read-only)

Use a Linux machine where the CFast card is attached with a compatible reader. Prefer direct SATA where hardware permits to eliminate USB ATA passthrough ambiguity. Verify the device by **size, serial and model**, not a remembered `/dev/sdX` letter.

```sh
lsblk -o NAME,SIZE,MODEL,SERIAL,TRAN,TYPE,MOUNTPOINTS
lsusb -t
sudo hdparm -I /dev/sdX
```

The `/dev/sdX` string is an illustrative placeholder, **not** a command to copy literally. Save the resulting security-state summary without publishing the serial number.

## Decision tree for authorised custodians

1. **no valid password:** mark **locked / unsanitised / not reusable**, store securely or have the organisation dispose of it. Spending hours on speculative unlocking is not justified by the value of a 4 GB CFast card.
2. **Known, authorised user password:** consult the exact `hdparm`/ATA command specification and perform any unlock or security-disable procedure only on a physically identified card with direct SATA/validated ATA pass-through and owner approval. Validate post-power-cycle state and reads before treating the card as usable. Do not place password in shell history or process command line.
3. **Known, authorised master password at Maximum level:** a destructive secure erase may be possible. Require owner authorisation, validated command transport, a tested recovery plan and independent verification. The card and all contents will be destroyed logically by the operation.
4. **Erased, unlocked card:** verify security `not enabled / not locked`, capacity, successful sector reads, partition creation and a full read/write test before any project reuse. Record sanitisation in accordance with the owner's policy.

**No ATA erase or disable command has been executed in this research.**

## Useful external standards and documentation

- `hdparm(8)` ATA Security options: https://man7.org/linux/man-pages/man8/hdparm.8.html
- ATA command and security behaviour (legacy ATA-ATAPI text): https://www.seagate.com/support/disc/manuals/ata/d1153r17.pdf

These sources describe the interface; they do not establish that a particular USB reader correctly forwards every command or that any password is known.
