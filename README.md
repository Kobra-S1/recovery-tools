# Recovery tools

Collection of SWUs for Anycubic Kobra printers to collect logs, clean settings files or dump flash content.

## How to use an SWU

1. Format a USB drive as FAT32 with an MBR partition table.
2. Create a folder named `aGVscF9zb3Nf` in the drive root.
3. Download the SWU for the exact printer model and copy it into that folder
   as `update.swu`.
4. Eject the drive cleanly, insert it into the powered printer, and wait for
   the updater to finish.

Use only one SWU at a time. These packages run with the stock updater's
permissions; choose the exact printer model and read the description first.

## Release v1.0.0

### Full partition dumpers

These create a complete flash backup on the USB drive, including the identity
snapshot and the large storage partition split into FAT32-safe numbered files.
Keep the resulting `flash-dump-*` folder intact.

> **Important:** These SWUs only create backups; they cannot restore them. A
> restore requires separate low-level xrock flashing from a PC.

Read the [dumper usage guide](releases/v1.0.0/dumpers/dumpers.md) before
starting. It explains USB preparation, progress beeps, successful completion,
and failure handling.

| Printer | Download |
|---|---|
| Kobra 2 Pro | [dump-partitions-K2P.swu](releases/v1.0.0/dumpers/dump-partitions-K2P.swu) |
| Kobra 3 | [dump-partitions-K3.swu](releases/v1.0.0/dumpers/dump-partitions-K3.swu) |
| Kobra 3 Max | [dump-partitions-K3M.swu](releases/v1.0.0/dumpers/dump-partitions-K3M.swu) |
| Kobra 3 V2 | [dump-partitions-K3V2.swu](releases/v1.0.0/dumpers/dump-partitions-K3V2.swu) |
| Kobra S1 | [dump-partitions-KS1.swu](releases/v1.0.0/dumpers/dump-partitions-KS1.swu) |
| Kobra S1 Max | [dump-partitions-KS1M.swu](releases/v1.0.0/dumpers/dump-partitions-KS1M.swu) |

### Maintenance tools

| Printer family | What it does | Download |
|---|---|---|
| Kobra 3 | Collects logs and configuration files to the USB drive. | [k3_k2p_log_and_config_collector.swu](releases/v1.0.0/maintenance/k3_k2p_log_and_config_collector.swu) |
| Kobra 2 Pro | Collects logs and configuration files to the USB drive. | [k3_k2p_log_and_config_collector.swu](releases/v1.0.0/maintenance/k3_k2p_log_and_config_collector.swu) |
| Kobra 3 | Removes mutable printer data. This is destructive. | [k3-printer_mutable_remover-update.swu](releases/v1.0.0/maintenance/k3-printer_mutable_remover-update.swu) |
| Kobra S1 | Collects logs and configuration files to the USB drive. | [ks1_log_and_config_collector.swu](releases/v1.0.0/maintenance/ks1_log_and_config_collector.swu) |
| Kobra S1 | Removes mutable printer data. This is destructive. | [ks1-printer_mutable_remover-update.swu](releases/v1.0.0/maintenance/ks1-printer_mutable_remover-update.swu) |

The release files stay under [`releases/v1.0.0/`](releases/v1.0.0/) so later
releases can be added without changing existing download links.
