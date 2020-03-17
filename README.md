# Overview

Run macOS Catalina(10.15) on a Dell XPS 7590 with OpenCore.

The EFI folder can be used to boot into a USB installer, or for regular booting.

You may refer to [[EN\] bavariancake/XPS9570-macOS](https://github.com/bavariancake/XPS9570-macOS) and [[CN\] LuletterSoul/Dell-XPS-15-9570-macOS-Mojave](https://github.com/LuletterSoul/Dell-XPS-15-9570-macOS-Mojave) and [[EN\] xxxzc/xps15-9570-macos](https://github.com/xxxzc/xps15-9570-macos) for the installation guide and solutions to some common issues.

But note that please create an issue **in this repository** if you encounter any problem when **using this config** (Please don't disturb others). My writing in English is poooooor:(, but I can read :).

## Tested hardware configuration

| Key               | Value                                                        |
| ----------------- | ------------------------------------------------------------ |
| SKU               | [XPS-7590](https://www.amazon.com/Dell-XPS7590-7572SLV-PUS-i7-9750H-DDR4-2666MHz-GeForce/dp/B07T1DTF4F/ref=sr_1_2?keywords=xps7590&qid=1584450937&sr=8-2) |
| CPU               | Intel Core i7 9750H                                          |
| GFX               | Intel Graphics UHD 630                                       |
| Builtin Screen    | 15.6"  4K UHD OLED Non-Touch                                 |
| RAM               | 16GB DDR4-2666MHz, 2x8G                                      |
| Internal SSD      | Toshiba 256Gb NVME SSD(Windows)                              |
| Portable SSD      | Samsung PM961 500Gb with SSK NVME Enclosure Adapter(macOS)   |
| Audio             | Realtek ALC298                                               |
| WiFi/BT(replaced) | Dell Wireless DW1560                                         |
## Tested software configurations

* bootloader: OpenCore 0.5.6

* macOS: Calinate 10.15.3(19D76)

## Working

* Intel Graphics
* Wireless:  WiFi/BT works perfect,  [**Continuity**](https://www.apple.com/macos/continuity/) is usable
* Audio: Speaker/Mic works good
* Camera
* Keyboarad
* Touchpad works perfect with VoodooI2C in GPIO mode
* USB port:  2 TYPE-A port(max 5Gbps) , 1 TYPE-C(max 10Gps)

## Not Working

1. OLED Backlight Control: There is no native backlight control for oled, wait for Apple to support it
2. Sleep/Wake: Sleep is ok, but black screen when wake(OLED issue)
3. Fingerprint
4. Discrete GPU

## Waiting Test

1. Thunderbolt: I have no device to test, if you test with this EFI, please let me know the result.
2. SDCard: I have no device to test, if you test with this EFI, please let me know the result.



# OpenCore's File Description 

## ACPI Patch

1. **SSDT-EC** : [Fixing Embedded Controller](https://khronokernel.github.io/Getting-Started-With-ACPI/Desktops/desktop-ec.html) **Required**
2. **SSDT-USBX**: USB power supply, based on SSDT-EC 
3. **SSDT-PLUG**: [Fixing Power Management](https://khronokernel.github.io/Getting-Started-With-ACPI/Universal/plug.html) **Required**
4. **SSDT-PNLF_BRT6**: LCD Backlight Control. *OLED no working(sadly)*
5. **SSDT-TPDX**: Touchpad GPIO patch
   1. GPIO mode with SSDT-TPDX.aml
      * 1\~4% kernel_task cpu usage when idle but cause 10~30% cpu usage when in use
   2. Polling mode without SSDT-TPDX.aml
      * always 10\~15% kernel_task cpu usage
6. **SSDT-DNVME**: Never use this unless you have a bad SSD like PM981A

## Kernal Extension

1. **Platform**: Lilu.kext
2. **Graphics**: WhateverGreen.kext
3. **Audio**: AppleALC.kext
4. **SMC**: VirtualSMC.kext
5. **WiFi**: AirportBrcmFixup.kext
6. **Bluetooth**: BrcmBluetoothInjector.kext、BrcmFirmwareData.kext、BrcmPatchRAM3.kext
7. **USB**: Customized USBPorts.kext for XPS 7590
8. **Sensors**: SMCBatteryManager.kext、SMCProcessor.kext、SMCSuperIO.kext
9. **Keyboard**: VoodooPS2Controller.kext
10. **Touchpad**: VoodooI2C.kext & VoodooI2CHID.kext
11. **Fingerprint**: disable with NoTouchID.kext to avoid kernal panic



## Credits

- [acidanthera](https://github.com/acidanthera) for providing almost all kexts and drivers
- [khronokernel](https://github.com/khronokernel) for [OpenCore Guide](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/)
- [alexandred](https://github.com/alexandred) for providing VoodooI2C
- [headkaze](https://github.com/headkaze) for providing the very useful [Hackintool](https://www.tonymacx86.com/threads/release-hackintool-v2-8-6.254559/)
- [daliansky](https://github.com/daliansky) for providing the awesome hotpatch guide [OC-little](https://github.com/daliansky/OC-little/) and the always up-to-date hackintosh solutions [XiaoMi-Pro-Hackintosh](https://github.com/daliansky/XiaoMi-Pro-Hackintosh) [黑果小兵的部落阁](https://blog.daliansky.net/)
- [RehabMan](https://github.com/RehabMan) for providing numbers of [hotpatches](https://github.com/RehabMan/OS-X-Clover-Laptop-Config/tree/master/hotpatch) and hotpatch guides
- [bavariancake](https://github.com/bavariancake/XPS9570-macOS) and [LuletterSoul](https://github.com/LuletterSoul/Dell-XPS-15-9570-macOS-Mojave) for providing detailed installation guide and configuration for XPS15-9570
- [xxxzc](https://github.com/xxxzc/xps15-9570-macos) for providing simplify OpenCore configuration
- And all other authors that mentioned or not mentioned in this repo