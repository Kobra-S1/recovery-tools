# Full partition dumper guide

The dumper creates a restorable copy of the printer's complete internal flash
on a USB drive. It also saves the printer-specific identity files that cannot
be downloaded again, such as calibration and account binding data.

## Before starting

1. Use the dumper SWU for the exact printer model listed in the
   [main download table](../../../README.md#full-partition-dumpers).
2. Format a USB drive as FAT32 with an MBR partition table. The drive must have
   enough free space for the printer's complete flash plus a small reserve.
3. Create `aGVscF9zb3Nf` in the drive root and copy the selected SWU into it
   as `update.swu`.
4. Safely eject the drive, insert it into the powered printer, and leave it
   connected until the result signal.

The large storage partition is split into numbered files such as
`useremain.img.001`, `.002`, and so on. This is normal and keeps every file
below FAT32's 4 GB limit. Never delete, rename, or reorder those files.

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

## After success

1. Remove the USB drive after the repeated three-tone success signal.
2. On a computer, keep the entire `flash-dump-1` folder together. A later run
   creates `flash-dump-2`, then `flash-dump-3`, and so on.
3. Confirm the folder contains `manifest.txt`, `identity/`, `dump.log`, and
   all image files, including every numbered image piece.
4. Copy the complete folder to safe storage before reusing the USB drive.

`manifest.txt` records each image's original flash location and checksum. It
is required for a correct stock restore.

## If it fails

Do not treat a partial `flash-dump-*` folder as a full recovery backup. Keep it
for diagnosis, but make a new complete dump after fixing the problem.

Read `dump.log` in the USB drive root first. Typical causes are insufficient
free space, a drive that disconnected, or an unsupported filesystem layout.
The log names the failed step. Do not delete the existing dump until you have
copied any useful diagnostic files to a computer.
