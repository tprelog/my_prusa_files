# Skelestruder Firmware Notes for Prusa MK3/MK3S

I'm using [JLTX's Skelestruder](https://www.thingiverse.com/thing:2845416) on my Prusa MK3S.

This repository contains updated patch files, sourced from [JLTX's Skelestruder Supplemental](https://jltxplore.dozuki.com/Wiki/Skelestruder_Information#main) with these additional modifications

- Dismiss [Language switch unavailable](https://github.com/prusa3d/Prusa-Firmware/issues/4024) after 1 second
- Menu change - PET renamed to PETG
- Includes [patch for MMU2](https://www.thingiverse.com/thing:3255143) (Found in the settings: here)
- Changed printer name to Skelestruder MK3/MK3S
- Using stock Prusa Z height - `210`

Patch files can be found in [fw_patch_files](https://github.com/tprelog/prusa_files/tree/master/fw_patch_files)

I've also included a copy of my compiled Skelestruder MK3S Firmware in [skelestruder_fw](https://github.com/tprelog/prusa_files/tree/master/skelestruder_fw)

---

## Basic steps for patching the firmware using Linux

```bash
# create an empty directory and `cd` into it
mkdir fw_3122
cd fw_3122

# download and extract the source code for prusa firmware
wget -O Prusa-Firmware-3.12.2.zip https://github.com/prusa3d/Prusa-Firmware/archive/v3.12.2.zip
unzip Prusa-Firmware-3.12.2.zip

# `cd` into unzipped directory and download the Skelestruder fw patch
cd Prusa-Firmware-3.12.2
wget -O skelestruder-3.12.2.patch https://raw.githubusercontent.com/tprelog/prusa_files/master/fw_patch_files/skelestruder-3.12.2.patch
```

### *Optional* - Additional Firmware Changes

<details><summary>Click to show</summary>

Edit the corresponding file for your printer

- MK3S - `Firmware/variants/1_75mm_MK3S-EINSy10a-E3Dv6full.h`
- MK3 - `Firmware/variants/1_75mm_MK3-EINSy10a-E3Dv6full.h`

Change the Printer Name

- Name dispays on the LCD screen
- Search for, then edit `#define CUSTOM_MENDEL_NAME`

```C
// Printer name
#define CUSTOM_MENDEL_NAME "SKELESTRUDER MK3S"
```

Change the Max Z Height

- JLTX's firmware patch uses `220`
- Search for, then edit `#define Z_MAX_POS`

```C
// Travel limits after homing
#define Z_MAX_POS 210
```

</details>

### *Optional* - Test the patch file

<details><summary>Click to show</summary>

```bash
patch -p1 --dry-run < skelestruder-3.12.2.patch
```

*Example - test command with output*

```bash
$ patch -p1 --dry-run < skelestruder-3.12.2.patch

checking file Firmware/Marlin_main.cpp
checking file Firmware/mmu.cpp
checking file Firmware/ultralcd.cpp
checking file Firmware/variants/1_75mm_MK3-EINSy10a-E3Dv6full.h
checking file Firmware/variants/1_75mm_MK3S-EINSy10a-E3Dv6full.h
```

</details>

### Apply the patch file

```bash
patch -p1 < skelestruder-3.12.2.patch
```

### Compile the Firmware

You can compile the firmware using `./PF-build.sh` - This script optionally some arguments.

- First argument defines which variant of the Prusa Firmware will be compiled. Valid options for Skelestruder are
  - Prusa MK3S - `1_75mm_MK3S-EINSy10a-E3Dv6full.h`
  - Prusa MK3 - `1_75mm_MK3-EINSy10a-E3Dv6full.h`
- Second argument defines if it is an english only version. Known values `EN_FARM` or `ALL`
- Third argument is the DEV_STATUS. Valid options are `GOLD`, `RC`, `BETA`, `ALPHA`, `DEVEL` or `DEBUG`

*Options for `PF-build.sh` were found by reviewing the [source code](https://github.com/prusa3d/Prusa-Firmware/blob/400f673fe0b898d0e3ec8c43fd2011f9320cf20c/PF-build.sh#L456)*

Compile skelestruder firmware for the Prusa MK3S

```bash
./PF-build.sh -v 1_75mm_MK3S-EINSy10a-E3Dv6full.h -l EN_FARM -d GOLD
```

*Example - build command with output*

```text
$ ./PF-build.sh -v 1_75mm_MK3S-EINSy10a-E3Dv6full.h -l EN_FARM -d GOLD

Check OS
Linux 64-bit found
Check for options
Prepare build env
fatal: not a git repository (or any parent up to mount point /)
Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYSTEM not set).
Current branch is:
created PF-build.branch file

Script path : /ssd/3D_Printing/_Printer_Files/fw_scr/fw_3122/Prusa-Firmware-3.12.2
OS          :
OS type     : linux

Arduino IDE : 1.8.19
Build env   : 1.0.8
Board       : prusa_einsy_rambo
Package name: PrusaResearch
Board v.    : 1.0.6
Specific Lib: PrusaLibrary

Nothing to clean up

Printer        : MK3S
Variant        : 1_75mm_MK3S-EINSy10a-E3Dv6full
Firmware       : 3122
Build #        : 5713
Dev Check      :
DEV Status     : GOLD
Motherboard    : BOARD_EINSY_1_0a
Board flash    :
Board mem      :
Languages      : EN_FARM
Hex-file Folder: PF-build-hex/FW3122-Build5713/BOARD_EINSY_1_0a
Hex filename   : FW3122-Build5713-1_75mm_MK3S-EINSy10a-E3Dv6full


English only language firmware will be built



Start to build Prusa Firmware ...
Using variant 1_75mm_MK3S-EINSy10a-E3Dv6full
Sketch uses 232618 bytes (91%) of program storage space. Maximum is 253952 bytes.
Global variables use 5595 bytes of dynamic memory.
Copying English only firmware to PF-build-hex folder
Copying English only elf file to PF-build-hex folder
1
Restored Board Flash


PF-build.sh finished with success
Build done, please use Slic3rPE > 1.41.0 to upload the firmware
more information how to flash firmware https://www.prusa3d.com/drivers/

Files:
FW3122-Build5713-1_75mm_MK3S-EINSy10a-E3Dv6full-EN_FARM.hex  FW3122-Build5713-1_75mm_MK3S-EINSy10a-E3Dv6full-EN_FARM.elf
```

---

### Flashing the firmware

You can flash your custom firmware using [Slic3rPE](https://help.prusa3d.com/en/article/firmware-updating-and-flashing_2227) or the [Octo-Print Firmware Updater](https://github.com/OctoPrint/OctoPrint-FirmwareUpdater/blob/master/README.md#octoprint-firmware-updater).

*Optional* - In order to make sure the firmware changes are loaded, you may want to send `M502` and then `M500` to your printer after the update. This recalls defaults from firmware and saves them to EEPROM.
