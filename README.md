# Intel i7-10700K Gigabyte Z490 VisionG Hackintosh

Fork from https://github.com/samuel21119/Intel-i9-10900-Gigabyte-Z490-Vision-G-Hackintosh

Tested working version: macOS Big Sur 11.7.1, macOS Ventura 13.0.1

## Bootloader

**[OpenCore](https://github.com/acidanthera/OpenCorePkg) 0.8.6**

## Working

- **USB**: All ports working

- **Ethernet**: Intel 82574L 1GbE

  - This NIC is natively supported. However the different subvendor ID prevents the system from loading the driver. So you need to use Linux to flash the EEPROM. Boot into Live Ubuntu. Use ```ip a``` to find out the Ethernet Adapter. In my case, it's ```enp8s0```. Then run the following command:

    ```
    sudo apt install ethtool
    sudo ethtool -E enp8s0 magic 0x10D38086 offset 0x16 value 0x00
    sudo ethtool -E enp8s0 magic 0x10D38086 offset 0x17 value 0x00
    sudo ethtool -E enp8s0 magic 0x10D38086 offset 0x1A value 0xF6
    sudo ethtool -E enp8s0 magic 0x10D38086 offset 0x16 value 0x00
    sudo ethtool -E enp8s0 magic 0x10D38086 offset 0x17 value 0x00
    sudo ethtool -E enp8s0 magic 0x10D38086 offset 0x18 value 0x86
    sudo ethtool -E enp8s0 magic 0x10D38086 offset 0x19 value 0x80
    ```

    Reboot the PC.
    
    Install [dortania/OpenCore-Legacy-Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher/tree/main/payloads/Kexts/Ethernet)

- **Ethernet**: Intel I225-V
  Flash a custom firmware: https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md
  
- Bluetooth: IOGEAR Bluetooth Dongle

  - Download latest BrcmPatchRAM from https://github.com/acidanthera/BrcmPatchRAM
  - Load 3 kexts in config.plist in the following order: BcrmFirmwareData should be first, BcrmPatchRAM3 second and finally BlueToolFixup third.

- Native NVRAM

- Sleep/Wake

- Reboot/Shutdown

## BIOS Setting

- Disable

  - CSM Support
  - Secure Boot
  - Re-Size BAR Support
  - Settings -> Super IO Configuration -> Serial Port: Disabled

- Enable

  - VT-x

  - VT-d (**DisableIoMapper** OFF in config.plist Kernel/Quirks)

  - Above 4G Decoding

  - Internal Graphics

  - DVMT Pre-Allocated: 64M

  - Hyper-Threading

    

## Credits

- [Apple](https://www.apple.com/) : Awesome macOS
- [Acidanthera](https://github.com/acidanthera) : OpencorePkg, kexts, tools etc.
- [Dortania](https://github.com/dortania) : Opencore guide
- https://github.com/SchmockLord/Hackintosh-Intel-i9-10900k-Gigabyte-Z490-Vision-D
- https://dortania.github.io/OpenCore-Install-Guide/extras/monterey.html#bluetooth: Bluetooth
- https://github.com/acidanthera/BrcmPatchRAM: Bluetooth
- https://www.reddit.com/r/hackintosh/comments/qgshzj/bluetooth_on_macos_monterey_1201/: IOGEAR Bluetooth dongle
- https://dortania.github.io/OpenCore-Install-Guide/ktext.html#ethernet: Vendor Id of Intel 82574L
- https://github.com/BrushXue/EP2C602-4LD16-E5-2667v2-Hackintosh: Intel 82574L Ethernet Adapter
- https://www.tonymacx86.com/threads/guide-asrock-rack-ep2c602.289060/: Intel 82574 Ethernet Adapter
- https://www.tonymacx86.com/threads/guide-replace-non-working-intel-i225-with-82574l-pcie-nic-monterey-opencore.319056/: Replace non-working Intel I225 with 82574L PCIe NIC (Monterey + OpenCore)
- https://www.tonymacx86.com/threads/solved-intel-82574l-nic-make-it-work-again.322802/: Intel 82574L PCIe NIC on Ventura
- https://www.tonymacx86.com/threads/asus-z690-proart-creator-wifi-thunderbolt-4-i7-12700k-amd-rx-6800-xt.318311/page-79#post-2313032: Spoof the AMD Radeon RX 5700 XT to the AMD Radeon Pro W5700X via DeviceProperties
- https://github.com/osx86-ijb/amdradeonrx5700xt-macos-fix-spoof: Spoof the AMD Radeon RX 5700 XT to the AMD Radeon Pro W5700X via DevicePropertie
- https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore: I225-V Fix