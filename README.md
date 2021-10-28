# Intel i7-10700K Gigabyte Z490 VisionG Hackintosh

Fork from https://github.com/samuel21119/Intel-i9-10900-Gigabyte-Z490-Vision-G-Hackintosh

Tested working version:

macOS Big Sur 11.6.1

## Bootloader

**[OpenCore](https://github.com/acidanthera/OpenCorePkg) 0.7.4**

## Working

- **USB**: All ports working, USB map is shown below

- **Ethernet**: Intel I225-V 2.5Gb

  - Add **dk.e1000=0** to boot-args, mentioned [here](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html#nvram)

  - Add device property:

  ```
  <key>PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)</key>
  <dict>
  	<key>device-id</key>
  		<data>
  		8hWGgA==
  		</data>
  </dict>
  ```

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

  - VT-d (Enable **DisableIoMapper** in config.plist)

  - Above 4G Decoding

  - Internal Graphics

  - DVMT Pre-Allocated: 64M

  - Hyper-Threading

    

## Credits

- [Apple](https://www.apple.com/) : Awesome macOS
- [Acidanthera](https://github.com/acidanthera) : OpencorePkg, kexts, tools etc.
- [Dortania](https://github.com/dortania) : Opencore guide
- https://github.com/SchmockLord/Hackintosh-Intel-i9-10900k-Gigabyte-Z490-Vision-D
- https://www.tonymacx86.com/threads/ohchangs-build-gigabyte-z590-vision-g-i7-10700k-amd-rx580.310986/page-18#post-2283363: Intel I225-V Ethernet Adapter