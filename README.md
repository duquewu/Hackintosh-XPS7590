## Hacbook XPS 7590

English/[中文](README-CN.md)

### Tested software configuration

**OpenCore Version**: [0.5.6](https://github.com/acidanthera/OpenCorePkg/releases)

**macOS Version**: macOS Catalina 10.15.3(19D76) - 10.15.4(19E266)

### Tested hardware configuration

| Key                    | Value                                                        |
| ---------------------- | ------------------------------------------------------------ |
| SKU                    | [XPS-7590](https://www.amazon.com/Dell-XPS7590-7572SLV-PUS-i7-9750H-DDR4-2666MHz-GeForce/dp/B07T1DTF4F/ref=sr_1_2?keywords=xps7590&qid=1584450937&sr=8-2) |
| CPU                    | Intel Core i7 9750H                                          |
| GPU                    | Intel Graphics UHD 630                                       |
| Builtin Screen         | 15.6"  4K OLED Non-Touch                                     |
| RAM                    | SK Hynix HMA81GS6JJR8N 8G x 2                                |
| Internal SSD - Windows | Toshiba KXG60ZNV256G NVMe 256GB                              |
| External SSD - macOS   | Samsung PM961 500Gb + SSK NVME Enclosure                     |
| Audio                  | Realtek ALC298                                               |
| Wireless               | Dell Wireless DW1560 (Replaced)                              |
### Working

* iGPU：working.
* Wireless Card(**DW1560**):  WiFi&BT working.
* Audio：spkear & mic working.
* Camera：working.
* Input：keyboard & touchpad working.
* HDMI Port：cold-plug working，hotplug half-working.
* USB port： Type-A port x 2 (Max 5 Gbps) and Type-C port x 1 (Max 10 Gbps)

### Known Problem

1. OLED Brightness Control

   * Brightless Slider shown & Brightless shortcut work but OLED brightness never changed

2. Sleep & Wake
	 >  Seems like OLED brightness issues. not sure other screen is work or not.

   * Sleep is worked , but built-in screen will be black screen when wake.


3. HDMI hot-plug 
   * Problem
     * Can't resume to single monitor mode when remove HDMI monitor, and virtual desktop in external monitor will be loss.
     * HDMI monitor will be black except mouse indicator when re-plug HDMI monitor.
   * Workaround
     1. Enable mirror mode in Preferences-Monitor-Arrangement **before** remove HDMI monitor to avoid virtual desktop loss in external monitor.
     2. Use built-in screen to toggle screen resolution in Preferences-Monitor **after** re-plug HDMI monitor. External screen will light soon.
4. SDCard Reader not working

### Waiting Test

> If you use these device with my efi, fell free to let me know how it worked.

1. Thunderbolt3
3. Brightness control & Sleep/Wake in non-OLED model of XPS 7590


## EFI Description
> This is my efi folder description

### efi

> EFI/OC/Drivers

* [OpenCorePkg - 0.5.6](https://github.com/acidanthera/OpenCorePkg/releases)
  * FwRuntimeServices.efi
* [AppleSupportPkg - 2.1.6](https://github.com/acidanthera/AppleSupportPkg)
  * ApfsDriverLoader.efi: APFS File System Driver
  * VBoxHfs.efi：HFS File System Dirver

### ACPI hotpatch

> EFI/OC/ACPI

1. **SSDT-EC**： [Fixing EC](https://khronokernel.github.io/Getting-Started-With-ACPI/Desktops/desktop-ec.html) . Use to instead of  `Rename EC0\EVDC to EC` 

2. **SSDT-USBX**： [USB Power](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/AcpiSamples/SSDT-EC-USBX.dsl). USB power output will up to 2000 mA when enable (iPhone or iPad). Base on SSDT-EC.

3. **SSDT-PLUG**： [Fixing Power Management](https://khronokernel.github.io/Getting-Started-With-ACPI/Universal/plug.html)

4. **SSDT-PNLF_BRT6**:：Bightness control by [xxxzc](https://github.com/xxxzc/xps15-9570-macos)，paired with  `Rename BRT6 to BRTX`.

5. **SSDT-TPDX**:：Touchpad GPIO patch by [xxxzc](https://github.com/xxxzc/xps15-9570-macos).

6. **SSDT-DNVME**: Block bad NVME SSD，**never** enable this except you put PM981a or other non-compatible SSD in internal nvme slot.

### KEXTs

> EFI/OC/Kexts

1. Platform： [Lilu.kext - 1.4.2](https://github.com/acidanthera/Lilu/releases)

   1. Graphics: [WhateverGreen.kext - 1.3.7](https://github.com/acidanthera/WhateverGreen/releases)

   2. SMC: [VirtualSMC.kext - 1.1.1](https://github.com/acidanthera/VirtualSMC)

      * BatteryManager：SMCBatteryManager.kext
      * CPU Monitor：SMCProcessor.kext
      * IO   Monitor：SMCSuperIO.kext

   3. WiFi：[AirportBrcmFixup.kext - 2.0.6](https://github.com/acidanthera/AirportBrcmFixup)

   4. Bluetooth：[BrcmPatchRAM - 2.5.1](https://github.com/acidanthera/BrcmPatchRAM)

      * BrcmBluetoothInjector.kext

      * BrcmFirmwareData.kext

      * BrcmPatchRAM3.kext
2. Audio：[AppleALC.kext - 1.4.7](https://github.com/acidanthera/AppleALC)
3. USB：Customed USBPorts.kext for XPS 7590
4. Keyboard：[VoodooPS2 2.1.2](https://github.com/acidanthera/VoodooPS2) 

   * VoodooPS2Controller.kext
5. Touchpad：[VoodooI2C - 2.3](https://github.com/alexandred/VoodooI2C/releases)

   * VoodooI2C.kext
   * VoodooI2CHID.kext
6. Fingureprint： Not work. Blocking with NoTouchID.kext

### config.plist

> Only describe property in using

#### ACPI

* Add：Enable SSDT hotpatch from this repo.  `SSDT-EC` & `SSDT-PLUG` is required.
* Patch：Enable brightness control by  `Rename BRT6 to BRTX` paired with  `SSDT-PNLF_BRT6` hotpatch.

##### Booter / Quirks

* AvoidRuntimeDefrag
* EnableSafeModeSlide
* EnableWriteUnprotector
* ProvideCustomSlide

* SetupVirtualMap

##### DeviceProperties / Add

* PciRoot(0x0)/Pci(0x1f,0x3)  Audio Injection
  
  * layout-id：`30`，
* PciRoot(0x0)/Pci(0x2,0x0) GPU Injection
  * AAPL,ig-platform-id:  `<3E9B 0000>`
  * dpcd-max-link-rate：`<1400 0000>` for 4K ,`<1000 0000>` for other
  * enable-dpcd-max-link-rate-fix
  * enable-hdmi-dividers-fix
  * enable-hdmi20
  * enable-lspcon-support
  * framebuffer-conX-alldata：customed framebuffer data, [GPU Patching](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/extras/gpu-patches#iGPU-BusID-Patching)
  * framebuffer-conX-enable
  * framebuffer-conX-has-lspcon
  * framebuffer-unifiedmem

  * framebuffer-portcount

#### Kernal 

* Add ：
  1. Lilu.kext:  **Must be first**
  2. VoodooGPIO.kext、VoodooI2CServices.kext **before** VoodooI2C.kext 
  3. Bluetooth Kext Sequence:
     1. BrcmBluetoothInjector.kext
     2. BrcmFirmwareData.kext
     3. BrcmPatchRAM3.kext
  4. VIrtualSMC **before** SMCxxxx 
* Quirks：
  * AppleCpuPmCfgLock：CFG is locking，use  `YES`
  * AppleXcpmCfgLock： CFG is locking ，use `YES`
  * CustomSMBIOSGuid：Dell OEM，use `YES`

#### Misc

* Boot
  * HideAuxiliary：hide `Recovery`  in opencore boot picker，use `YES`
  * HideSelf：hide `EFI`  in opencore boot picker，use `YES`
  * ShowPicker：show boot picker in opencore ,use `YES`
  * Timeout：boot picker timeout, use `3` second
* Security
  * AllowNvramReset：Allows for NVRAM reset both in the boot picker and when pressing `Cmd+Opt+P+R`
  * AllowSetDefault：Allow `CTRL+Enter` and `CTRL+Index` to set default boot device in the picker
  * Vault：Disable FileValut，use `Optional `
  * ScanPolicy：`0` allows you to see all drives available, please refer to [Security]() section for further details. **Will not boot USBs with this set to default**

#### NVRAM

* Add
  * `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14` 
    * UIScale：`02` for HiDPI 
  * `7C436110-AB2A-4BBB-A880-FE41995C9F82`
    * boot-args
      1. brcmfx-country=#a: remove wifi's country limit
      2. agdpmod=vit9696: block motherboard HDMI chekcing
      3. -wegnoegpu：block discrete graphics
      4. -v：enable opencore log in booting
    * csr-active-config：`E7030000` disable SIP
    * prev-lang:kbd：`"en-US:0"` for US keyboard layout
* Block
  * `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14` 
    * UIScale
  * `7C436110-AB2A-4BBB-A880-FE41995C9F82` 
    * boot-args
    * csr-active-config
* WriteFlash：write NVRAM to flash

#### PlatformInfo

* Automatic：`YES`
* UpdateDataHub：`YES`
* UpdateNVRAM：`YES`
* UpdateSMBIOS：`YES`
* UpdateSMBIOSMode：Dell OME，use `Custom`
* Generic：
  * SystemProductName
  * MLB
  * SystemSerialNumber
  * SystemUUID

#### UEFI

* ConnectDrivers：enable efi driver, use`YES`
* Drivers
* Input：
  * KeyForgetThreshold： `5` ms 
  * KeyMergeThreshold： `2` ms 
  * KeySupport： `YES`
  * KeySupportMode： `Auto`
* Output:
  * ProvideConsoleGop：`YES`
  * SanitiseClearScreen：`YES` for HiDpi Screen
* Protocols：
  * AppleSmcIo：use OpenCorePkg built-in VirtualSMC.efi，`NO`

> Config.plist in this repo is simplify, use [Sanity check](https://opencore.slowgeek.com/?file=coffeelake056tphZcM&rs=coffeelake056) to see more

## Credit

- Thanks [bavariancake](https://github.com/bavariancake/XPS9570-macOS)、 [LuletterSoul](https://github.com/LuletterSoul/Dell-XPS-15-9570-macOS-Mojave) 、[xxxzc](https://github.com/xxxzc/xps15-9570-macos) for providing  XPS-9570 hackintosh-efi and guide

