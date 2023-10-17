# C64PECTRE

Research & development on Commodore and related technologies
https://github.com/c64pectre.


# Installing GEOS on a CMD-HD with a C64 and GEORAM in VICE Emulator

We'll use a geoRAM 512KB and not a REU, for a change, and because we do own a
physical 512KB geoRAM and a 4MB neoRAM, the latter one having a battery backup.

Different approaches are possible:
we could start with the 1541 D64 GEOS disk images (plural) or with the one 1581
D81 disk image.
We take the D64 route, for no particular reason other than it representing the
most authentic scenario.

Note that we could use DirMaster/Style to skip the Convert step — just drag a
`.CVT` file onto the D64 — but we want the authentic experience.

Also note that you could use two 1541 drives at `8` and `10` to speed up the
process.

https://github.com/c64pectre/c64-cmd-hd-geos

## Preparation

- Find and download GEOS from the official website https://cbmfiles.com/geos/:

    - At "Other useful things": GEOS 64 (`GEOS64.ZIP`). It contains four D64
        files `APPS.D64`, `GEOS.D64`, `SPELL64.D64` and `WRUTIL64.D64`.
        `GEOS.D64` is the boot image for booting on a C64 from a 1541.
        It it is standard without REU or GEORAM or CMD-HD support.
        The boot image already contains Convert 2.5, so we do not need to
        download it.

    - At "Special configurations":
        - `CONGR64.CVT`: this is CONFIGURE for C64 with CMD-HD and geoRAM.
        - `GR4164.CVT`: this is GEOBOOT CONFIGURE for C64 with 1541 and geoRAM.
        - `GRHD64.CVT`:  this is GEOBOOT CONFIGURE for C64 with CMD-HD and geoRAM.

- Find and download the "HD GEOS UTILS" at
    https://commodore.software/downloads/download/94-cmd-hard-drive/8017-cmd-hard-drive-geos-utilities
    as `cmd-hd-geos-utils.zip` and extract `cmdhd-geos-utils.d64`.
    This is a GEOS disk.

- Set your machine to a stock machine, we use a "C64 PAL".

- Set up drive `8` as a 1541-II.
    (We were having difficulties with the SpeedDOS kernal and 1541, not sure
    what was the problem. Afterwards, using the SpeedDOS worked fine when using
    GEOS on the HD.)

- Set up drive `9` as a hard drive (see tutorial "c64-cmd-hd").

## Steps

### 1. Create GEOS HD partition

On the hard drive, create a partition for GEOS.

1. Use `HD-TOOLS.64` to create a partition for GEOS of type `1581` name it
    `GEOS` or another name to your liking.

1. Set the default partition to this GEOS partition.
    This is not strictly necessary, as we can navigate partitions, but makes the
    process easier.
    You can always change the default partition afterwards.

### 2. Set up geoRAM

In VICE, in Settings, at Cartridges, at GEO-RAM:

1. Set up the geoRAM cartrigde to `512K`, enable it, and enable "write on
   detach/quit".

### 3. Set up Mouse

In VICE, in Settings, at Input devices, at Control port:

1. At Control Port 1 choose "Mouse (1351)".

Do not check "Save battery-backed real time clock data when changed", because
we'll use the hard drive for that.

### 4. Create Helper Disk Images

Create these helper D64's for transfering the upgrade files:

1. Create a D64 for upgrading the 1541 GEOS boot disk:

        c1541 -format "c64-1541-georam,x1" d64 c64-1541-georam.d64

1. Rename and copy these files into the disk image:

    - `REN CONGR64.CVT CONGR64.CVT.PRG` and copy to the D64.

    - `REN GR4164.CVT GR4164.CVT.PROG` and copy to the D64.

1. Create a D64 for upgrading the CMD-HD GEOS boot disk:

        c1541 -format "c64-hd-georam,x2" d64 c64-hd-georam.d64

1. Rename and copy these files into the disk image:

    - `REN GRHD64.CVT GRHD64.CVT.PRG` and copy to D64

### 5. Setup GEOS Mouse

Before we can upgrade, we need to configure the mouse and convert the upgrade
files on the helper disk images.

1. Mount `8` `GEOS.D64`.

1. Boot GEOS from `8`. Now drive `8` is drive `A` `System` in GEOS.

1. Configure the mouse in GEOS:
    
    1. Press the key combination `CBM+I`.

    1. Use the cursor keys and `RETURN` to select "COMM 1351(a)", then move the
        mouse pointer to the `OK` button and press `RETURN`.

    1. Now you can grab and release the mouse with the key combination `ALT+M`.

### 6. Convert Upgrade files

Convert the upgrade files to the GEOS format.

1. Start `CONVERT`.
1. Click `Disk`.
1. Mount `8` `c64-1541-georam.d64`.
1. Click `OK`.
1. Convert all files.
1. Do the same with `c64-hd-georam.d64`.
1. Quit
1. Mount `8` `GEOS64.D64`.

### 7. Upgrade GEOS 1541 Boot Disk

First we need to upgrade the GEOS 1541 boot disk, so we will be able to access
the HD. Then we can copy the GEOS files to the HD and upgrade the HD with its
specific `GEOBOOT`.

1. Delete `GEOBOOT` and `CONFIGURE`: select and drop into the recycle bin.

1. Close `A`.

1. Mount `8` `c64-1541-georam.d64`.

1. Open `A`.

1. Copy `GEOBOOT` and `CONFIGURE_r` to `System` via the border.
    After this, `A` is again on `System`.

1. Rename `CONFIGURE_r` to `CONFIGURE`.

1. Reboot: exit to BASIC and boot GEOS to make sure it works.

1. Exit GEOS.

1. Make a backup of `GEOS64.D64`.

### 8. Install GEOS on HD

1. Boot GEOS from `8`.

1. Open `CONFIGURE`.

1. Configure drive `B` to `1581`. Now drive `B` is the GEOS partition on the HD.

1. Copy all files, except `GEOBOOT`, from `A` `System` to `B` `GEOS`.

1. Close `A`.

1. Mount `8` on `c64-hd-georam.d64`.

1. Open `A`.

1. Copy `GEOBOOT` (from `A`) to `B` `GEOS`.

1. Close `A`

1. Mount `GEOS64.D64`

1. Open `A`.

1. Exit to BASIC.

1. Unmount `8`.

1. Make a backup of `GEOS64.D64`.

1. You can now throw away the helper disk images.

### 9. Boot GEOS from HD

1. Boot GEOS from the HD:

        LOAD "GEOS",9,1

    You now have got a working GEOS installation on the HD.

1. Make a backup of the HD: exit GEOS and copy the DHD file.

### 10. Copy Applications

Copy the applications from the three remaining GEOS disk images to GEOS on the
HD.

### 11. Setup QuickMove and Automatic Clock Synchronization.

With the "QuickMove" GEOS application you can manage partitions and files on the
HD.

The "HDTime" makes it possible to automatically synchronize the clock in GEOS
from the HD. Files on the HD have an actual date and timestamp that is
synchronized from your PC.

1. Mount `8` `cmd-hd-geos-tools.d64`.

1. Open `A`.

1. Copy `QuickMode64` and `HDTime`.

1. Open `HDTime`. You only need to do this once to enable synchronization.
