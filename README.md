# Intel i7-10700K Gigabyte Z490 VisionG Hackintosh

Fork from https://github.com/samuel21119/Intel-i9-10900-Gigabyte-Z490-Vision-G-Hackintosh

Tested working version: macOS Ventura 13.1

## Bootloader

**[OpenCore](https://github.com/acidanthera/OpenCorePkg) 0.8.7**

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
    
    ```
    ACPI->Add->0 DMAR.aml Enabled: YES
    ACPI->Delete->0 Drop OEM DMAR Table: NO
    DeviceProperties->Add->PciRoot(0x0)/Pci(0x1C,0x4)/Pci(0x0,0x0)->device-id F6100000
    Kernel->Quirks->DisableIoMapper: NO
    ```
    
    ~~Install [dortania/OpenCore-Legacy-Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher/tree/main/payloads/Kexts/Ethernet)~~

- **Ethernet**: Intel I225-V <br>
  Flash a custom firmware: <br>https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md <br>Conditions for Intel I225-V and 3rd party WiFi/LAN cards to work: <br>https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225_stock_vs_cstmfw.md <br>
  
  | macOS        | Vt-D   | DisableIoMapper | DMAR (OEM) | DMAR (dropped/replaced) | I225-V / 3rd party LAN/WiFi |
  | ------------ | ------ | --------------- | ---------- | ----------------------- | --------------------------- |
  | 11.4 to 13.0 | **ON** | **OFF**         | **YES**    | **NO / NO**             | **YES / YES**               |
  
- **Graphics**: Spoof the AMD Radeon RX 5700 XT to the AMD Radeon Pro W5700X via DeviceProperties

  ```
  <key>DeviceProperties</key>
  	<dict>
  		<key>Add</key>
  		<dict>
  			<key>PciRoot(0x0)/Pci(0x1,0x0)/Pci(0x0,0x0)/Pci(0x0,0x0)/Pci(0x0,0x0)</key>
  			<dict>
  				<key>@0,name</key>
  				<string>ATY,Adder</string>
  				<key>@1,name</key>
  				<string>ATY,Adder</string>
  				<key>@2,name</key>
  				<string>ATY,Adder</string>
  				<key>@3,name</key>
  				<string>ATY,Adder</string>
  				<key>AAPL00,DualLink</key>
  				<data>
  				AQAAAA==
  				</data>
  				<key>ATY,Card#</key>
  				<string>102-D32200-00</string>
  				<key>ATY,Copyright</key>
  				<string>Copyright AMD Inc. All Rights Reserved. 2005-2019</string>
  				<key>ATY,DeviceName</key>
  				<string>W5700X</string>
  				<key>ATY,EFIVersion</key>
  				<string>01.01.190</string>
  				<key>ATY,FamilyName</key>
  				<string>Radeon Pro</string>
  				<key>ATY,Rom#</key>
  				<string>113-D3220E-190</string>
  				<key>CAIL_EnableLBPWSupport</key>
  				<integer>0</integer>
  				<key>CAIL_EnableMaxPlayloadSizeSync</key>
  				<integer>1</integer>
  				<key>CFG_CAA</key>
  				<integer>0</integer>
  				<key>CFG_FB_LIMIT</key>
  				<integer>0</integer>
  				<key>CFG_FORCE_MAX_DPS</key>
  				<integer>1</integer>
  				<key>CFG_GEN_FLAGS</key>
  				<integer>0</integer>
  				<key>CFG_NO_MST</key>
  				<integer>0</integer>
  				<key>CFG_NVV</key>
  				<integer>2</integer>
  				<key>CFG_PAA</key>
  				<integer>0</integer>
  				<key>CFG_PULSE_INT</key>
  				<integer>1</integer>
  				<key>CFG_TPS1S</key>
  				<integer>1</integer>
  				<key>CFG_TRANS_WSRV</key>
  				<integer>1</integer>
  				<key>CFG_UFL_CHK</key>
  				<integer>0</integer>
  				<key>CFG_UFL_STP</key>
  				<integer>0</integer>
  				<key>CFG_USE_AGDC</key>
  				<integer>1</integer>
  				<key>CFG_USE_CP2</key>
  				<integer>1</integer>
  				<key>CFG_USE_CPSTATUS</key>
  				<integer>1</integer>
  				<key>CFG_USE_DPT</key>
  				<integer>1</integer>
  				<key>CFG_USE_FBC</key>
  				<integer>0</integer>
  				<key>CFG_USE_FBWRKLP</key>
  				<integer>1</integer>
  				<key>CFG_USE_FEDS</key>
  				<integer>1</integer>
  				<key>CFG_USE_LPT</key>
  				<integer>1</integer>
  				<key>CFG_USE_PSR</key>
  				<integer>0</integer>
  				<key>CFG_USE_SCANOUT</key>
  				<integer>1</integer>
  				<key>CFG_USE_SRRB</key>
  				<integer>0</integer>
  				<key>CFG_USE_STUTTER</key>
  				<integer>1</integer>
  				<key>CFG_USE_TCON</key>
  				<integer>1</integer>
  				<key>PP_DisableDIDT</key>
  				<integer>1</integer>
  				<key>PP_DisablePowerContainment</key>
  				<integer>1</integer>
  				<key>PP_DisableVoltageIsland</key>
  				<integer>0</integer>
  				<key>PP_FuzzyFanControl</key>
  				<integer>1</integer>
  				<key>device_type</key>
  				<string>ATY,AdderParent</string>
  				<key>hda-gfx</key>
  				<string>onboard-1</string>
  				<key>model</key>
  				<string>Radeon Pro W5700X</string>
  				<key>name</key>
  				<string>ATY_GPU</string>
  			</dict>
  		</dict>
  	</dict>
  ```

  Although it's not necessary to spoof since Monterey 12.3.1 update, **BenQ EW3270U Refresh rate** keeps reverting to ```Variable (40-60 Hertz)``` without spoofing. 

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