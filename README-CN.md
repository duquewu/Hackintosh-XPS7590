## Hacbook XPS 7590

中文/[English](README.md)

### 引导

**OpenCore Version**: [0.5.6](https://github.com/acidanthera/OpenCorePkg/releases)

**macOS Version**: macOS Catalina 10.15.3(19D76) - 10.15.4(19E266)

### 配置信息

| Key                | Value                                                        |
| ------------------ | ------------------------------------------------------------ |
| SKU                | [XPS-7590](https://www.amazon.com/Dell-XPS7590-7572SLV-PUS-i7-9750H-DDR4-2666MHz-GeForce/dp/B07T1DTF4F/ref=sr_1_2?keywords=xps7590&qid=1584450937&sr=8-2) |
| CPU                | Intel Core i7 9750H                                          |
| 核心显卡           | Intel Graphics UHD 630                                       |
| Builtin Screen     | 15.6"  4K OLED 非触屏                                        |
| RAM                | 海力士 HMA81GS6JJR8N 8G x 2                                  |
| 内置 SSD - Windows | 东芝 KXG60ZNV256G NVMe 256GB                                 |
| 外置 SSD - macOS   | 三星 PM961 500Gb + SSK NVME 硬盘盒                           |
| Audio              | Realtek ALC298                                               |
| 无线网卡           | 更换为 Dell Wireless DW1560                                  |
### 工作情况

* 核心显卡：工作正常
* 独立显卡：无解，已屏蔽
* 无线网卡:  WiFi、蓝牙 工作正常，AirDrop 可用
* 板载声卡：麦克风、扬声器 工作正常
* 摄像头：工作正常
* 输入设备：键盘、触摸板 工作正常
* 外接显示器：HDMI 冷启动正常，热插拔异常，解决方式见下条
* USB：端口已定制， 两个 Type-A 端口（最高 5 Gbps）；一个 Type-C 端口（最高 10 Gbps）
* 指纹：无解，已屏蔽

### 已知问题

1. OLED 屏幕亮度调节：亮度调节进度条正常显示、亮度调节快捷键正常，但屏幕亮度不会改变，OLED 原生亮度调节暂时无解，可使用第三方软件 [NightTone](https://apps.apple.com/us/app/nighttone-night-light-filter/id1135472584?mt=12) 来调整亮度
2. 睡眠/唤醒: 可以睡眠，但唤醒时内建屏幕持续黑屏，无法点亮。PS：可能仍是 OLED 屏幕亮度调节问题，其他屏幕版本有待测试
3. HDMI 热插拔问题：
   * 问题: 开机状态下移除 HDMI 设备时，屏幕无法自动恢复至单显示器模式，且 HDMI 设备的桌面将会丢失；重新插入 HDMI 设备后，HDMI 设备黑屏，只能看到鼠标指针
   * 解决方式：
     1. 移除 HDMI 设备前，在 系统偏好设置-显示器-排列 中开启镜像显示器模式，避免 HDMI 设备中的桌面丢失
     2. 再次插入 HDMI 设备后，在 系统偏好设置-显示器 中切换 HDMI 设备的分辨率，多切换几次即可解决 HDMI 设备的黑屏问题

### 等待测试

> 目前缺乏以下设备进行测试，如果你使用这个 EFI 对如下设备进行了测试，不妨将结果告知于我

1. 雷电 3 设备：显示器、扩展坞待测试
2. SD 读卡器
3. XPS 7590 非 OLED 型号的 亮度调节、睡眠唤醒功能


## 项目文件概述
> 以下是本项目中使用的 efi、kexts、ssdt 以及 config.plist 文件的说明

### EFI 驱动 

> EFI/OC/Drivers 目录

* [OpenCorePkg - 0.5.6](https://github.com/acidanthera/OpenCorePkg/releases)
  * FwRuntimeServices.efi
* [AppleSupportPkg - 2.1.6](https://github.com/acidanthera/AppleSupportPkg)
  * ApfsDriverLoader.efi: APFS 文件系统驱动
  * VBoxHfs.efi：HFS 文件系统驱动

### ACPI 热补丁

> EFI/OC/ACPI 目录

1. **SSDT-EC**： [EC 修复](https://khronokernel.github.io/Getting-Started-With-ACPI/Desktops/desktop-ec.html) 。用于取代 Rename EC0、EVDC to EC 等重命名补丁

2. **SSDT-USBX**： [USB 电源补丁](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/AcpiSamples/SSDT-EC-USBX.dsl)，基于 SSDT-EC。使用后可将 USB 电源输出提高至 2000 毫安（需设备支持），推荐使用

3. **SSDT-PLUG**： [电源管理修复](https://khronokernel.github.io/Getting-Started-With-ACPI/Universal/plug.html)

4. **SSDT-PNLF_BRT6**:：亮度调节 by [xxxzc](https://github.com/xxxzc/xps15-9570-macos)，需配合 Rename BRT6 to BRTX 使用。OLED 屏无效

5. **SSDT-TPDX**:：触控板 GPIO 补丁 by [xxxzc](https://github.com/xxxzc/xps15-9570-macos) 。触控板有两种工作模式，使用此补丁可开启 GPIO 模式
   1. GPIO 模式：kernel_task 闲时 1\~4% ，忙时 10\~30% 的 CPU 占用
     
   2. Polling 模式：kernel_task 常驻 10\~15%  的 CPU 占用

6. **SSDT-DNVME**: 屏蔽 NVME 设备，仅用于在 macOS 系统中屏蔽 PM981a 等故障 SSD

### KEXT 内核扩展

> EFI/OC/Kexts 目录

1. 驱动平台： [Lilu.kext - 1.4.2](https://github.com/acidanthera/Lilu/releases)

   1. 显卡驱动: [WhateverGreen.kext - 1.3.7](https://github.com/acidanthera/WhateverGreen/releases)

   2. 模拟 SMC: [VirtualSMC.kext - 1.1.1](https://github.com/acidanthera/VirtualSMC)

      * 电池管理：SMCBatteryManager.kext
      * CPU监控：SMCProcessor.kext
      * IO   监控：SMCSuperIO.kext

   3. 无线驱动：[AirportBrcmFixup.kext - 2.0.6](https://github.com/acidanthera/AirportBrcmFixup)

   4. 蓝牙驱动：[BrcmPatchRAM - 2.5.1](https://github.com/acidanthera/BrcmPatchRAM)

      * BrcmBluetoothInjector.kext

      * BrcmFirmwareData.kext

      * BrcmPatchRAM3.kext
2. 声卡驱动：[AppleALC.kext - 1.4.7](https://github.com/acidanthera/AppleALC)
3. USB驱动：XPS 7590 定制 USBPorts.kext
4. 键盘驱动：[VoodooPS2 2.1.2](https://github.com/acidanthera/VoodooPS2) 

   * VoodooPS2Controller.kext
5. 触控板驱动：[VoodooI2C - 2.3](https://github.com/alexandred/VoodooI2C/releases)

   * VoodooI2C.kext
   * VoodooI2CHID.kext
6. 指纹驱动： 无解，使用 NoTouchID.kext 屏蔽

### config.plist 描述

> 仅描述 XPS 7590 使用到的配置项

#### ACPI 配置项

* Add：选择性启用本项目中提供的 SSDT 热补丁，其中 SSDT-EC、SSDT-PLUG 必须开启，其他自选
* Patch：若要使用启用亮度调节快捷键，则必须开启 Rename BRT6 to BRTX 并在 Add 中启用 SSDT-PNLF_BRT6

##### Booter / Quirks 配置项

* AvoidRuntimeDefrag：修复主板 UEFI
* EnableSafeModeSlide：安全模式下启用连续性的内存注入方式
* EnableWriteUnprotector：保证 nvram 能正常写入而不受到 UEFI 内的一些服务的影响
* ProvideCustomSlide：

* SetupVirtualMap：建立虚拟内存并对物理内存进行映射

##### DeviceProperties / Add 配置项

* PciRoot(0x0)/Pci(0x1f,0x3) 声卡注入
  
  * layout-id：`30`，
* PciRoot(0x0)/Pci(0x2,0x0) 显卡注入
  * AAPL,ig-platform-id 设备 ID，UHD 630 <3E9B 0000>
  * dpcd-max-link-rate：链接速率上限，4K 屏幕使用 <1400 0000> ,其他使用 <1000 0000>
  * enable-dpcd-max-link-rate-fix：启用 dpcd-max-link-rate
  * enable-hdmi-dividers-fix：修复 4K HDMI 屏幕异常
  * enable-hdmi20：启用 HDMI 2.0
  * enable-lspcon-support：启用 LSP 端口转换
  * framebuffer-conX-alldata：自定义端口数据，详情查看 [GPU Patching](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/extras/gpu-patches#iGPU-BusID-Patching)
  * framebuffer-conX-enable：启用自定义数据
  * framebuffer-conX-has-lspcon：在 conX 上使用 LSP 转换
  * framebuffer-unifiedmem：核显显存相关

  * framebuffer-portcount：端口数量

#### Kernal 配置项

* Add ：添加 Kext 驱动，注意点如下
  1. Lilu.kext 必须在第一个，
  2. VoodooGPIO.kext、VoodooI2CServices.kext 需要放在 VoodooI2C.kext 之前
  3. 蓝牙驱动的顺序：
     1. BrcmBluetoothInjector.kext
     2. BrcmFirmwareData.kext
     3. BrcmPatchRAM3.kext
  4. VIrtualSMC 必须放在 SMCxxxx 之前
* Quirks：
  * AppleCpuPmCfgLock：未解锁 CFG ，选 `YES`
  * AppleXcpmCfgLock：未解锁 CFG ，选 `YES`
  * CustomSMBIOSGuid：Dell 专属，选 `YES`

#### Misc 配置项

* Boot
  * HideAuxiliary：在 OC 启动项中隐藏 Recovery 等分区，选 YES
  * HideSelf：在 OC 启动项中隐藏自身 EFI ，选 YES
  * ShowPicker：OC 启动过程是否显示系统选择器，选 YES
  * Timeout：OC 启动项选择器的超时时间，3 秒
* Security
  * AllowNvramReset：允许在 OC 启动过程中使用 `Cmd+Opt+P+R` 重置 NVRAM
  * AllowSetDefault：允许在 OC 启动过程中使用 `Ctrl+Enter` 或 `Ctrl+Index` 设置默认启动项
  * Vault：是否开启 FileValut，使用`Optional `不开启
  * ScanPolicy：OC 启动路径扫描策略，使用 `0` 扫描所有设备，其他策略可在 [ScanPolicy](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/extras/security#scanpolicy) 中查看

#### NVRAM 配置项

* Add
  * `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14` 固定值
    * UIScale：4K 屏幕使用 `0x02`，4K 以下使用 `0x01`
  * `7C436110-AB2A-4BBB-A880-FE41995C9F82` 固定值
    * boot-args：启动参数
      1. brcmfx-country=#a：解除 WiFI 不同地区的功率限制
      2. agdpmod=vit9696：屏蔽主版 HDMI 接口检查
      3. -wegnoegpu：屏蔽独立显卡
      4. -v：是否在 OC 启动过程中显示日志
    * csr-active-config：`E7030000` 完全关闭 SIP
    * prev-lang:kbd：`"en-US:0"`，设置键盘类型为美国
* Block：屏蔽主板原生 NVRAM 中的参数，从而使得 Add 中自定义的参数生效
  * `4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14` 固定值
    * UIScale
  * `7C436110-AB2A-4BBB-A880-FE41995C9F82` 固定值
    * boot-args
    * csr-active-config
* WriteFlash：将 NVRAM 写入到闪存中

#### PlatformInfo 配置项

* Automatic：自动填写，`YES`
* UpdateDataHub：`YES`
* UpdateNVRAM：`YES`
* UpdateSMBIOS：`YES`
* UpdateSMBIOSMode：戴尔设备，使用 `Custom`
* Generic： 以下信息可使用 Clover 或 Hackintosh 生成
  * SystemProductName
  * MLB
  * SystemSerialNumber
  * SystemUUID

#### UEFI 配置项

* ConnectDrivers：是否加载 efi 补丁，`YES`
* Drivers：与 EFI/OC/drivers 中存放的 efi 对应
* Input：
  * KeyForgetThreshold：键盘按键超时阈值，保持默认 `5` ms 
  * KeyMergeThreshold：键盘按键合并阈值，保持默认 `2` ms 
  * KeySupport：OC 启动过程中键盘是否可用，保持默认 `YES`
  * KeySupportMode：保持默认 `Auto`
* Output:
  * ProvideConsoleGop：调用显卡GOP，`YES`
  * SanitiseClearScreen：修复 4K 屏字体过小的问题，`YES`
* Protocols：
  * AppleSmcIo：是否使用 OpenCorePkg 内置的 VirtualSMC.efi，`NO`
    * VirtualSMC.efi 只在开启 FileValut 时需要，此条可直接删除

> 本项目中的 config 有所裁剪，若想了解具体裁剪信息，可使用 [Sanity check](https://opencore.slowgeek.com/?file=coffeelake056tphZcM&rs=coffeelake056) 进行查看

## 鸣谢

- [bavariancake](https://github.com/bavariancake/XPS9570-macOS)、 [LuletterSoul](https://github.com/LuletterSoul/Dell-XPS-15-9570-macOS-Mojave) 、[xxxzc](https://github.com/xxxzc/xps15-9570-macos) 提供了 XPS 9570 的黑苹果配置和教程