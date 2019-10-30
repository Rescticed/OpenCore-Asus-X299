 What's this for? Not too many X299 hardware running macOS, let alone OpenCore so thought I'd post this ;p 
 Please use this as a base **and not a guide**. Inital setup please follow the [OpenCore Vanilla Desktop Guide](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/)
 
 
# What work and what doesn't

Works:
* Native NVRAM(super happy about this one)
* Bootcamp
* Sleep
* USB power(Including iPad Pro 12.9 Charging!)
* CPU Power Management
* iTunes and Netflix
* Handoff, AirDrop and all Apple services
* [Display Brightness and Volume with Apple Keyboard](https://github.com/the0neyouseek/MonitorControl/releases)
* macOS High Sierra, Mojave and Catalina(10.15.1 is most recent)

Doesn't work:
* CPU Name: Easy fix under `PlatformInfo->SMBIOS` but too lazy to fix ;p
* Onboard Wifi: Won't work, I removed it and use a genuine Apple Airport BCM94360CD Card with PCIe x1 adapter
* Onboard Bluetooth: Works inconsistently, replaced with BCM94360CD so didn't look into it, see [this thread](https://github.com/daliansky/XiaoMi-Pro-Hackintosh/issues/262) for some ideas
* Sidecar: Well *kinda*, this is a common issue with Catalina and can sometimes be fixed by swapping board IDs to either an iMac19,1 or MacPro7,1 with the following boot flags:
   * `shikigva=40` needed for patching
   * `shiki-id=Mac-AA95B1DDAB278B95` for iMac19,1 boardID
   * `shiki-id=Mac-27AD2F918AE68F61` for MacPro7,1 boardID
 
 
# OpenCore Specifics

X299 config.plist specifics:
* `slide=N`
* `DisableIoMapper` set to `YES`
* Ethernet set to `built-in`
* `AppleRTC` kernel Patches(reboots into BIOS safemode)
* `layout-id` set to 11
* iMacPro1,1 or MacPro7,1 SMBIOS


X299 SSDTs specifics:
* `SSDT-EC-USBX-X299`: Powers off `EC0` controller and creates a fake EC just for macOS, needed for all Catalina users
* `SSDT-PLUG-X299`: Sets `Plugin-type=1` to `CP00`
* `SSDT-HPET`: Resolves IRQ conflicts, not needed but for piece of mind
* `SSDT-SBUS-MCHC`: Creates SMbus device and resolves issues with DeviceProperties injection when using SSDTs like `SSDT-XHCI`, `SSDT-XHCX` and `SSDT-SATA`
* `SSDT-XHCI`, `SSDT-XHCX` and `SSDT-SATA`: DeviceProperties injection, mostly cosmetic

X299 kexts specifics:

* `X299-Map`: Maps USB chipset ports
* `AsMedia-Map`: Maps AsMedia 3.1 ports, both front and rear(front panel needs proper testing)
* `VoodooTSCSync`: Synchronize the TSC, can be fixed via OpenCore as well. Change `IOCPUNumber` inside the `Info.plist` to 1 less than total number of threads. This was needed to boot macOS
* `IntelMausiEthernet`: Running an Intel I219V controller

X299 Firmware Driver specifics:
* `UsbKbDxe.efi`: Built-in drivers don't work with `PollAppleHotKeys`

# Hardware Specifics

Hardware:
* i9 7920X
* Noctua NH D15
* Asus Strix X299-E Gaming
* MSI AirBoost Vega 56 with AIO cooler
* WD Black SN750 1TB NVMe
* 32GB 2666Mhz (4x8GB)
* Apple BCM94360CD

BIOS settings:
* CFG-Lock: Disable
   * If can't disable, turn on `AppleCpuPmCfgLock` and `AppleXcpmCfgLock`. Without this, you won't go far for install
* UEFI mode
* CSM: Off
* legacy USB: Off
* Mass Storage Device: Off