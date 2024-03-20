# kebab
OnePlus 8T (KB2003): https://wiki.lineageos.org/devices/kebab/

## Upgrade LineageOS for microG (20.0 to 21.0)

### Prerequisites

#### Software
1. [OTA payload dumper](https://github.com/ssut/payload-dumper-go), [Archlinux: AUR package `payload-dumper-go-bin`](https://aur.archlinux.org/packages/payload-dumper-go-bin)
1. [Android SDK platform tools](https://developer.android.com/studio/releases/platform-tools#downloads), [Archlinux: Extra package `android-tools`](https://archlinux.org/packages/extra/x86_64/android-tools/)
1. [GNU coreutils](https://www.gnu.org/software/coreutils/), [Archlinux: Core package `coreutils`](https://archlinux.org/packages/core/x86_64/coreutils/)

#### Resources
1. [OxygenOS firmware image `KB2003_13.1.0.583(EX01): KB2003_11.F.70_2700_202312042154`](https://xdaforums.com/t/oneplus-8t-rom-ota-oxygen-os-repo-of-oxygen-os-builds.4193183/#post-83971385)
1. [Additional firmware partition images `dtbo.img` and `vbmeta.img`](https://wiki.lineageos.org/devices/kebab/install#flashing-additional-partitions), [Download](https://download.lineage.microg.org/kebab/)
1. [LineageOS for microG 21.0](https://download.lineage.microg.org/kebab/)
    - Recovery image file
    - OS zip file
1. Check all downloads with `sha256sum` or `md5sum` and compare them to the ones online

### Full upgrade guide
1. Extract firmware images
    1. Unzip the `payload.bin` file out of the OxygenOS firmware image zip
    1. Dump the partition images: `payload-dumper-go -o . payload.bin` (Caution: This may overwrite existing files like `dtbo.img` and `vbmeta.img`!)
1. Enable USB Debugging
    1. Enable: `System -> Settings -> Developer options -> Debugging -> USB-Debugging`
    1. Check connection: `adb devices`
1. Update firmware
    1. Reboot to recovery: `adb reboot recovery`
    1. Enable: `Advanced -> Enable ADB`
    1. Check for DDR type: `adb shell cat /proc/devinfo/ddr_type`
        1. Output `Device version: DDR4` (use `xbl_config.img` and `xbl.img`)
        1. Output `Device version: DDR5` (use `xbl_config_lp5.img` and `xbl_lp5.img`)
    1. Enter fastboot: `adb reboot fastboot`
        1. Check connection: `fastboot devices`
    1. Update firmware
        1. If the [latest required firmware](https://wiki.lineageos.org/devices/kebab/install/) is already installed this is optional only for vendor firmware. <br/>
           **Do continue with the required additional partitions!**
        1. Update firmware, part 1: [Appendix A](#appendix-a)
        1. Update firmware, part 2: [Appendix B](#appendix-b) (WARNING: Use the correct files for your DDR type!)
        1. Update additional partitions: [Appendix C](#appendix-c) **(This is always required!)**
1. Update Recovery
    1. Flash LineageOS recovery image: `fastboot flash --slot=all recovery lineage-21.0-xxxxxxxx-microG-kebab-recovery.img`
    1. Reboot: `fastboot reboot bootloader`
1. Update OS
    1. Start recovery
    1. Enable: `Advanced -> Enable ADB`
    1. Initiate sideloading: `Apply Update -> Apply from ADB`
    1. Sideload: `adb sideload lineage-21.0-xxxxxxxx-microG-kebab.zip`
    1. Prompt: `To install additional packages... Do you want to reboot recovery now? -> NO`
    1. Start: `Reboot System now`
1. First start of the new OS
    1. Check user data
    1. Check self test in microG settings
    1. Reboot to recovery and redo the steps from `Update OS` to also update the second slot

### Continuous firmware upgrade guide (after the full upgrade)
1. Download newer OxygenOS firmware from the link in [Resources](#resources)
1. Download the latest additional firmware partition images from the link in [Resources](#resources)
1. Follow the [Full upgrade guide](#full-upgrade-guide) for these steps
    1. Extract firmware images
    1. Enable USB Debugging (only relevant if disabled)
    1. Update firmware (Tip: For a possible error situation, remember the current slot shown in recovery mode beforehand!)
1. Reboot: `fastboot reboot bootloader`
1. Start recovery
1. Start: `Reboot System now`

### Appendix

#### Appendix A
Source: [Update firmware on kebab: 7.](https://wiki.lineageos.org/devices/kebab/fw_update)

```
fastboot flash --slot=all abl abl.img
fastboot flash --slot=all aop aop.img
fastboot flash --slot=all bluetooth bluetooth.img
fastboot flash --slot=all cmnlib64 cmnlib64.img
fastboot flash --slot=all cmnlib cmnlib.img
fastboot flash --slot=all devcfg devcfg.img
fastboot flash --slot=all dsp dsp.img
fastboot flash --slot=all featenabler featenabler.img
fastboot flash --slot=all hyp hyp.img
fastboot flash --slot=all imagefv imagefv.img
fastboot flash --slot=all keymaster keymaster.img
fastboot flash --slot=all logo logo.img
fastboot flash --slot=all mdm_oem_stanvbk mdm_oem_stanvbk.img
fastboot flash --slot=all modem modem.img
fastboot flash --slot=all multiimgoem multiimgoem.img
fastboot flash --slot=all qupfw qupfw.img
fastboot flash --slot=all spunvm spunvm.img
fastboot flash --slot=all storsec storsec.img
fastboot flash --slot=all tz tz.img
fastboot flash --slot=all uefisecapp uefisecapp.img
```

#### Appendix B
Source: [Update firmware on kebab: 8.](https://wiki.lineageos.org/devices/kebab/fw_update)

> WARNING: Use the correct files for your DDR type!

For type `DDR4`:
```
fastboot flash --slot=all xbl_config xbl_config.img
fastboot flash --slot=all xbl xbl.img
```

For type `DDR5`:
```
fastboot flash --slot=all xbl_config xbl_config_lp5.img
fastboot flash --slot=all xbl xbl_lp5.img
```

#### Appendix C
Source: [Install LineageOS on kebab: Flashing additional partitions](https://wiki.lineageos.org/devices/kebab/install#flashing-additional-partitions)

```
fastboot flash --slot=all dtbo lineage-21.0-xxxxxxxx-microG-kebab-dtbo.img
fastboot flash --slot=all vbmeta lineage-21.0-xxxxxxxx-microG-kebab-vbmeta.img
```
