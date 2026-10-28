# Hardware inventory — observed

| Component | Finding | Evidence / caveat |
|---|---|---|
| Model | SMA 410 | Appliance and login screen |
| Motherboard | MB-7532 V2.0; 107-000260-50 PCB marking; MB-7532R assembly | Photograph; sticker date is not firmware date |
| CPU | Atom C2758 @ 2.40 GHz, 8 logical CPUs | Linux serial boot log |
| Memory | 8 GB = 2 × 4 GB DDR3-1600 ECC Innodisk | Both module labels photographed |
| CFast | OEM 4 GB Innodisk CFast 3SE, ATA `S130710S` | Boot log / `hdparm -I` |
| SATA | 2 spare SATA data connectors on PCB; AHCI SATA controllers logged | Power/BIOS boot from spare ports untested |
| USB | Linux enumerates USB controller / hub | External ports not individually qualified |
| Video | ASPEED AST1400 device | No chassis VGA port; debug headers not mapped |
| BIOS | AMI Aptio / `PS BIOS v001` | No setup interface found over serial |
| Serial | ttyS0, 115200 8N1 | Working MobaXterm log |
| PCIe edge | Unidentified PCB gold-finger connector | **Do not treat as confirmed PCIe pinout** |

Note: Legacy Linux boot logs may print 10 GiB address-space maxima despite only 8 GiB installed. Use actual DIMM labels and runtime physical memory, not maximum address mappings, to determine installed RAM.

### Console details

Firmware screen shows `10.2.2.3-93sv`, SafeMode `5.0.0.13`. The post-login interface is a constrained configuration menu, not a general shell. No external VGA port was located. The two-stage original Linux boot sequence (3.1.0, then 3.15.0) is visible in captured serial logs. **These are kernel build details, not current security assurances.**

### Hardware risk

Intel Atom C2000 family reliability history merits independent assessment before 24/7 reuse. No evidence in this investigation establishes whether this particular V2.0 board has an LPC mitigation. Wall-power measurement was not undertaken.
