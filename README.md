THINKPAD T530 OPENCORE FOR HACKINTOSH

ABOUT:
OpenCore EFI Folder for running macOS High Sierra all the way up to macOS Sequoia on the Lenovo ThinkPad T530. For Sequoia, you need atleast Big Sur to be installed and use OpenCore Legacy Patcher.

NON FUNCTIONAL:
>SD Card reader (Above Sonoma)

>FingerPrint Reader

>NVidia Quadro GPU (Disable in BIOS)

>VGA Port (Not supported after MacOS Mountain Lion)

SPECS OF LAPTOP I USED:

>Model	Lenovo ThinkPad T530, Model# 2392-AWG

>Chipset	Intel QM77 Express

>BIOS Version	2.77, unlocked with 1vyRain

>Processor	Intel Core i5 3360M

>Memory	16GB Samsung DDR3 1600MHz, Dual-Channel

>Minimum: 8GB

>Samsung 870 EVO SATA 1TB SSD

>Integrated Graphics	Intel HD Graphics 4000, 2048 MB RAM assigned

>Display	15.6" HD+ TFT Display (1920x1080 px)

>Audio	Realtek ALC269VC Rev.3 (Layout-id:39)

>Ethernet	Intel 82579LM Gigabit Network Connection

>Intel Centrino Ultimate-N 6300, 11a/b/g/n

>Multicard Reader	Ricoh 4-in-1 reader (MMC, SD, SDHC, SDXC)

>ExpressCard/34 slot	disabled

THINGS TO CONSIDER:

>DeviceProperties:

Enable the correct Framebuffer-Patch for your display. The T530 comes with either one of the following display panels: HD+ or HD, supporting different resolutions. Each requires a different framebuffer patch (AAPL,ig-platform-id) with different connector patches:

-AAPL,ig-platform-id 04006601 = HD+ = WSXGA and FullHD. Resolution: ≥ 1600x900 px. (Default)
-AAPL,ig-platform-id 03006601 = HD = SD. Resolution: ≤ 1366x768 px.

>If your T530 has an SD panel, do the following;

-Go to DeviceProperties
-Disable the entry PciRoot(0x0)/Pci(0x2,0x0) by placing # in front of it.
-Enable #PciRoot(0x0)/Pci(0x2,0x0) 1366x768 px by deleting the leading # and the description 1366x768 px, so that it looks this: PciRoot(0x0)/Pci(0x2,0x0).

SMBIOS (Must Change)

Under SystemProductName, select the correct SMBIOS for your CPU and generate a serial, etc. for it. My EFI utilizes Patches and kexts from OpenCore Legacy Patcher which allow using the correct SMBIOS for Ivy Bridge CPUs on macOS 11.3 and newer (Darwin Kernel 20.4+), so native Power Management and OTA System Updates are working oob which wouldn't be possible otherwise past macOS Catalina.

-For Intel i7: MacBookPro10,1 (Default)
-For Intel i5: MacBookPro10,2 

WiFi and Bluetooth (Read carefully!)

Case 1: Intel Wifi/BT Card. In stock configuration, the T530 comes with an Intel WiFi/Bluetooth card, so you need different kexts for WiFi and Bluetooth. It may work with OpenIntelWireless kexts.
Check the compatibility list to find out if your card is supported.
-Remove all kexts containing "Brcm" in the name.
-Add the required Kexts for your Intel card to EFI/OC/Kexts folder and config.plist before attempting to boot with this EFI!

Case 2: 3rd Party WiFi/BT Cards. These require the 1vyrain jailbreak to unlock the BIOS to disable the WiFi Whitelist (not required if the 3rd party card is whitelisted).
-WiFi/BT Card by Broadcom requires AirportBrcmFixup for WiFi and BrcmPatchRAM and additional satellite kexts for Bluetooth.
-BrcmFirmwareData.kext is used for injecting the required firmware for Broadcom devices. 
-Alternatively, you can use BrcmFirmwareRepo.kext which is more efficient but has to be installed into System/Library/Extensions since it cannot be injected by Bootloaders.
-If you use a WiFi/BT Card from a different vendor than Broadcom, remove the Brcm Kexts and add the Kext(s) required for your card to the kext folder and config.plist before deploying the EFI folder!

Used boot arguments and NVRAM variables
>Boot-args:

-gfxrst=1: Draws Apple logo at 2nd boot stage instead of framebuffer copying → Smoothens transition from the progress bar to the Login Screen/Desktop when an external monitor is attached.

-ipc_control_port_options=0: Fixes issues with Firefox not working and electron-based Apps like Discord in macOS 12+ when SIP is lowered.

>NVRAM variables:

OCLP Settings -allow_amfi: Does the same as boot-arg amfi_get_out_of_my_way=0x1 but only when the OpenCore Patcher App is running. Otherwise you can't run the root patcher. But this didn't work the last time I tried this setting might be deprecated.

hbfx-ahbm: Lets the system hibernate instead of using regular sleep. Requires HibernationFixup.kext. More details here

revblock:media: Blocks mediaanalysisd on Ventura+ (for Metal 1 GPUs). Required so apps like Firefox don't crash. Requires RestrictEvents.kext

revpatch:

sbvmm: Forces VMM SB model, allowing OTA updates for unsupported models on macOS 11.3 and newer. Requires RestrictEvents.kext.

memtab: Adds Memory tab to "About this Mac" section (macOS ≤ 12 only). Requires RestrictEvents.

f16c: Disables f16c instruction set reporting in macOS 13.3 or newer to prevent CoreGraphics crashing on Ivy Bridge CPUs

BIOS SETTINGS TO CONSIDER:

>USB- UEFI BIOS Support : Enabled

>Display- OS Detection for NVidia Optimus: Disabled

>Security- Security Chip: Disabled

>Virtualization- Intel Virtualization Technology: Enabled

>CSM Support: Disabled

>Boot Mode: Quick

>UEFI/Legacy Boot: UEFI only

THINGS TO CONSIDER:

I/O Port Access-

>Wireless WAN

>ExpressCard Slot

>eSerial ATA Port

>FingerPrint Reader

>Antitheft and Computrace

>Secure Boot

NOTE: Enable Boot Order Lock after you've set-up the order of the Boot Drives. This prevents WindowsBootManager from taking over the first slot of the boot drives. This way, you don't need to enable the LauncherOption in OpenCore!

