# Hackintosh-Intel-i5-10400-Gigabyte-Z490M-Gaming-X

# ![Screen Shot 2020-06-11 at 11.48.59 PM](./Screen Shot.png)Hardware:

CPU: [Intel Core 10 Gen i5-10400]
RAM: [HyperX Fury 32GB 2666MHz DDR4 CL16 DIMM (Kit of 2)]
Motherboard: [GIGABYTE Z490M Gaming X]
GPU: [SAPPHIRE PULSE Radeon RX 5600 XT]
SSD: [XPG SX8200 Pro 512GB M.2 SSD]
Wifi/Bluetooth: [fenvi FV-T919 Hackintosh macOS Catalina AC1750 PC PCI-E BCM94360CD Wifi]
PSU: [CORSAIR CX-M Series CX450M 450W 80 PLUS BRONZE]
Fans: [Noctua NF-P14s redux-1500 PWM]
CPU Cooler: [Cooler Master Hyper 212 Black Edition]
Case: [darkFlash DLM21 White Micro ATX Mini Tower]

# Bootloader:

Opencore 0.5.9

# Not Working:

**Audio:** Tried multiple alcid for the ALC1200 from the motherboard, recognised hardware, but no audio output.

# Installation Steps:

**Most of these steps comes from the dortania installation guide.** Make sure to read those instructions.

## GibMacOS

> Making a bootable MacOS USB drive from Windows.

1. Run gibMacOS.bat as administrator
2. Press "r" to toggle recovery only
3. Download the most recent full install version
4. Open MakeInstall.bat as Admin and select your drive with option O for OpenCore( ex: 1O).
5. After formatting the disk, copy and paste the path of RecoveryHDMetaDMG.pkg inside the folder of "macOS Downloads" in the "gibMacOS" directory.

## Cleanup Drivers and Tools

- **Remove from Drivers:**

  - OpenUsbKbDxe.efi
    - Used for OpenCore picker on **legacy systems running DuetPkg**, [not recommended and even harmful on Ivy Bridge and newer](https://applelife.ru/threads/opencore-obsuzhdenie-i-ustanovka.2944066/page-176#post-856653)
  - UsbMouseDxe.efi
    - similar idea to OpenUsbKbDxe, should only be needed on legacy systems using DuetPkg
  - NvmExpressDxe.efi
    - Used for Haswell and older when no NVMe driver is built into the firmware
  - XhciDxe.efi
    - Used for Sandy Bridge and older when no XHCI driver is built into the firmware
  - HiiDatabase.efi
    - Used for fixing GUI support like OpenShell.efi on Sandy Bridge and older
  - OpenCanopy.efi
    - This is OpenCore's optional GUI, we'll be going over how to set this up in [Post Install](https://dortania.github.io/OpenCore-Desktop-Guide/extras/gui.html) so remove this for now
  - Ps2KeyboardDxe.efi + Ps2MouseDxe.efi
    - Pretty obvious when you need this, USB keyboard and mouse users don't need it
    - Reminder: PS2 ≠ USB

  I also deleted crscreenshot.efi, keeping only AudioDXE.efi and OpenRuntime.efi in the folder.

- **Remove everything from Tools:**

  - Way to many to list them all, but I recommend keeping OpenShell.efi for troubleshooting purposes

## Get Firmware Drivers

------

[Gathering files](https://dortania.github.io/OpenCore-Desktop-Guide/ktext.html)

- Added HfsPlus.efi for reading Hfs filesystems. In total there are three drivers, AudioDXE.efi, OpenRuntime.efi and HfsPlus.efi.

## Get Kexts

------

[OneDrive](https://onedrive.live.com/?authkey=!APjCyRpzoAKp4xs&id=FE4038DA929BFB23!455036&cid=FE4038DA929BFB23)

Kexts Repo

![img](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/7736dc89-4bef-4efb-adef-19ae9561532c/_%282%29.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200612%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200612T064540Z&X-Amz-Expires=86400&X-Amz-Signature=66ee5839cf1d6b5d71648947a96475acec46c270492e210de673564589254bc6&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22_%282%29.png%22)

## ACPI SSDT

------

[corpnewt/SSDTTime](https://github.com/corpnewt/SSDTTime)

- Run SSDTTime
- Press 4 for DumpDSDT
- Press 2 for Fake EC
- Copy results excepte DSDT.ami to ACPI folder under EFI/OC

## config.plist

------

- Download OpenCorePkg's releases.

- Rename the sample.plist file in "Docs" under the OpenCorePkg folder

- Copy the file to "EFI/OC" on the usb

- Download ProperTree and start the program

  [corpnewt/ProperTree](https://github.com/corpnewt/ProperTree)

- Open your config.plist in the app

- And change all configurations mentioned in

  [Comet Lake](https://dortania.github.io/OpenCore-Desktop-Guide/config.plist/comet-lake.html#pciroot0x0pci0x20x0)

- Use CorpNewt's GenSMBIOS application to generate SMBIOS info, and add it to config.plist.

Disabled: SetupVirtualMap may be needed depending on the firmware, generally this quirk should be avoided but most Gigabyte users and older hardware(Broadwell and older) will need this quirk to boot.

Added SSDT-AWAC.aml to config.plist

## Bios Settings

------

**Disable:**

- Fast Boot
- VT-d (can be enabled if you set `DisableIoMapper` to YES)
- CSM
- Thunderbolt(For initial install, as Thunderbolt can cause issues if not setup correctly)
- Intel SGX
- Intel Platform Trust
- CFG Lock (MSR 0xE2 write protection)(**This must be off, if you can't find the option then enable both `AppleCpuPmCfgLock` and `AppleXcpmCfgLock` under Kernel -> Quirks. Your hack will not boot with CFG-Lock enabled**) **NOT FOUND**

**Enable:**

- VT-x
- Above 4G decoding
- Hyper-Threading
- Execute Disable Bit
- EHCI/XHCI Hand-off
- OS type: Windows 8.1/10 UEFI Mode **(Windows 10 Feautres: Ohter)**
- DVMT Pre-Allocated(iGPU Memory): 64MB

# Post Installation

------

1. Boot without USB: Copy the EFI folder on the usb to the mounted efi on main computer using "Mount EFI".

   [corpnewt/MountEFI](https://github.com/corpnewt/MountEFI)

2. Exclude Verbose Mode

3. Fixing Audio