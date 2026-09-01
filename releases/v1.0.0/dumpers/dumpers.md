# Full partition dumper guide

> **Important:** This SWU only dumps partitions to the USB drive. It has no
> restore function. Its purpose is to prepare for a bricked-printer recovery
> without opening the printer first. Restoring this backup requires low-level
> flashing with xrock: remove the mainboard, solder a USB connection to it,
> and connect it to a PC.

The dumper creates a raw copy of the printer's internal flash on a USB drive.
It also saves printer-specific identity files that cannot be downloaded again,
such as calibration and account-binding data.

> **Current validation:** The Kobra S1 dumper has been used to create a dump.
> A full restore has not yet been tested. The packages for Kobra 2 Pro, Kobra
> 3 variants, and Kobra S1 variants are provided for their respective models.

## Before starting

1. Use the dumper SWU for the exact printer model listed in the
   [main download table](../../../README.md#full-partition-dumpers).
2. Format a USB drive as FAT32 with an MBR partition table. The drive must have
   enough free space for the printer's complete flash plus a small reserve.
3. Create `aGVscF9zb3Nf` in the drive root and copy the selected SWU into it
   as `update.swu`.
4. Safely eject the drive, insert it into the powered printer, and leave it
   connected until the result signal.

Any partition larger than FAT32's 4 GB per-file limit is split into numbered
files, such as `useremain.img.001`, `.002`, and so on. On the Kobra S1/Kobra
3-family stock layout this is `useremain`; another model may use a different
large data-partition name. Never delete, rename, or reorder those files.

> **Privacy warning:** The large user-data partition and `identity/` folder
> contain your printer's unique identity data, including cloud-related data.
> Do not share the images blindly. Removing that information from raw partition
> images requires Linux commands and knowledge that this guide does not cover.

## Example USB-drive layout

Before insertion, the USB drive contains only the updater package:

```text
USB drive
└── aGVscF9zb3Nf/
    └── update.swu
```

After a successful Kobra S1 or Kobra 3 dump, the dumper creates a numbered
backup directory alongside the updater folder. Partition image names depend
on the printer's stock partition table, so the list below is an example rather
than a fixed list for every model.

```text
USB drive
├── aGVscF9zb3Nf/
│   └── update.swu
└── flash-dump-1/
    ├── dump.log
    ├── manifest.txt
    ├── env.img
    ├── idblock.img
    ├── uboot_a.img
    ├── uboot_b.img
    ├── misc.img
    ├── boot_a.img
    ├── boot_b.img
    ├── system_a.img
    ├── system_b.img
    ├── oem_a.img
    ├── oem_b.img
    ├── userdata.img
    ├── ac_lib_a.img
    ├── ac_lib_b.img
    ├── ac_app_a.img
    ├── ac_app_b.img
    ├── useremain.img.001
    ├── useremain.img.002
    ├── ...
    └── identity/
        ├── userdata/
        │   └── ... printer-specific configuration files ...
        └── useremain/
            └── ... printer-specific ID files ...
```

The shown partitions are the normal stock layout for the Kobra S1 and Kobra 3
family. A different model can have different partition names or no
`useremain` directory. What matters is that every image named in
`manifest.txt`, all numbered pieces, and the complete `identity/` directory
stay together in the same `flash-dump-*` folder.

## What the beeps mean

| Sound | Meaning | What to do |
|---|---|---|
| One long beep at the start | The dumper started. | Leave the USB drive connected. |
| Very short high-pitched beep, then every 5 seconds | A partition image is being copied. | The dumper is still working. |
| Very short lower-pitched beep, then every 4 seconds | The copied partition is having its MD5 checksum calculated. | The dumper is still working. |
| One short beep | One step completed: first the identity snapshot, then one partition at a time. | This is not the final success signal. |
| Three rising tones | The whole dump finished successfully. | Wait for the repeated result signal, then remove the drive. |
| Four low tones | The dump failed. | Remove the drive after the repeated error signal and read the log. |

After either final signal, the same success or failure pattern repeats every
five seconds until the USB drive is removed. That makes the result audible if
you return later.

## Expected duration and normal sequence

Do not remove the USB drive merely because the dump seems slow. A normal full
dump takes roughly **25 to 30 minutes**, but this is only a ballpark figure:
the USB drive speed and printer model affect the time substantially.

The normal audible sequence is one long start beep, regular high-pitched copy
beeps, regular lower-pitched checksum beeps, and short completion beeps between
steps. It ends only with the repeating three rising tones. The repeating four
low tones mean an error, not a slow dump.

## After success

1. Remove the USB drive after the repeated three-tone success signal.
2. On a computer, keep the entire `flash-dump-1` folder together. A later run
   creates `flash-dump-2`, then `flash-dump-3`, and so on.
3. Confirm the folder contains `manifest.txt`, `identity/`, `dump.log`, and
   all image files, including every numbered image piece.
4. Copy the complete folder to safe storage before reusing the USB drive.

`manifest.txt` records each image's original flash location and checksum. Keep
it with the images for a future low-level restore.

## Preparing split images for a later restore

Keep the numbered files as they are for archive storage. Only when preparing a
working copy for a future xrock restore, join every numbered piece into its
original single image. Do this before flashing; do not flash an individual
`.001` or `.002` piece.

For example, on Linux, from inside `flash-dump-1`:

```sh
cat useremain.img.[0-9][0-9][0-9] > useremain.img
md5sum useremain.img
awk '$1 == "useremain" { print $5 }' manifest.txt
```

The MD5 value printed by `md5sum` must match the value printed from
`manifest.txt`. The filename can differ by model: replace `useremain` with the
base name of the numbered pieces on your drive.

On Windows Command Prompt, the current two-piece example is:

```bat
cd X:\flash-dump-1
copy /b useremain.img.001+useremain.img.002 useremain.img
certutil -hashfile useremain.img MD5
```

If there are more pieces, add each one in numeric order, for example append
`+useremain.img.003`. Compare the resulting MD5 with the matching row in
`manifest.txt` before using the image for low-level flashing.

## If it fails

Do not treat a partial `flash-dump-*` folder as a full recovery backup. Keep it
for diagnosis, but make a new complete dump after fixing the problem.

Read `dump.log` in the USB drive root first. Typical causes are insufficient
free space, a drive that disconnected, or an unsupported filesystem layout.
The log names the failed step. Do not delete the existing dump until you have
copied any useful diagnostic files to a computer.
