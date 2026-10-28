# Evidence and privacy policy

This public release intentionally **does not include unredacted serial transcripts**. Original logs can contain device names, IP addresses, serial numbers, configuration identifiers, secret material or runtime-generated keys. User screenshots may also include serial labels or credentials. Do not post any full raw log or memory dump to a public repository without review and redaction.

### Included images

- `board-overview.jpg`: physical component overview, resized and stripped of camera EXIF; small OEM serial label area concealed.
- `bios-sticker.jpg`: AMI Aptio sticker, cropped/resized and stripped of EXIF.
- `sata-connectors.jpg`: storage-connectors detail, with the original card's label obscured and EXIF stripped.

These images are illustrative evidence only. No image demonstrates working third-party firmware.

### Original evidence (not packaged)

- Serial capture: two-stage Linux boot, CPU, SATA and serial data.
- Physical images: CPU cooling, board marking, DIMM labels and CFast SKU.
- Linux VM terminal captures: USB bridge ID, UAS to usb-storage change, sector-zero error and both cards' ATA identify/security data.
- Direct observed hardware tests: original card works; no card three beeps; substitute card four beeps.

**Do not publish unique hardware serials or active network configuration.** Raw logs remain with the researcher/organisation subject to their own handling policies. Any future contribution should include evidence provenance and distinguish observation from inference.
