https://www.tonymacx86.com/threads/guide-asrock-rack-ep2c602.289060/

**Components**
The only specifically required components for this build are an ASRock Rack EP2C602 motherboard and Ivy Bridge-EP processors. Your case, ram, PSU, etc should have no bearing on your success. As noted below, the LGA 2011 socket supports both Sandy Bridge-EP (E5 v1) and Ivy Bridge-EP (E5 v2) processors. You could use this guide to build a similar setup with Sandy Bridge-EP xeon processors, but since I don't have those chips, I don't know what difference will arise.

**Pre-Installation Notes**

- This is not a beginners hackintosh. You will encounter pitfalls where it will be handy to have prior experience. If you are contemplating a dual CPU motherboard with the purpose of running macOS, but have never built a hack before, I would encourage you to consider an X99 or X299 single CPU build.
- This guide will heavily draw on or reference other sources. I don't intend to reinvent the wheel. Please let me know if there are any dead links which require correction.
- This guide is for the [Asrock Rack EP2C602](http://www.asrockrack.com/general/productdetail.asp?Model=EP2C602#Specifications) using Ivy Bridge (E5 v2) processors. Although ASRock makes several boards, such as the 4L/D16, that are similar to the EP2C602, I don't own them and therefore can't test on them. Sandy Bridge processors will work (likely just as well as Ivy Bridge), but there are some distinctions that will effect the build. Since I don't own an E5 v1 chip, I can't test what those differences are. If you own an Sandy Bridge-EP, please provide feedback so that I can incorporate the information into this guide.
- Read the entire guide through, from start to finish, at least once before starting anything. Familiarize yourself with the whole process before diving in. If any problems arise it will help to know what the big picture looks like.
- This guide is for macOS 10.15.2+ Catalina. Please use the [older guide](https://www.tonymacx86.com/threads/deprecated-asrock-rack-ep2c602-install-guide-for-sierra-and-high-sierra.245421/) for prior versions of macOS. This guide will use MacPro7,1 SMBIOS, so the minimum supported OS release is 10.15.2. Please do not use any lower macOS release.
- If this helps you, awesome! More importantly, if it doesn't or you encounter any problems at all, please provide feedback. I will continue to expand or revise this guide based on comments received.

**Installation**

**Step One - Reprogram your NIC Device ID's:**

The purpose of this step is to permanently reprogram each gigabit ethernet controller with a different device-id. We are changing the device-id information to match that contained inside Intel82574L.kext so that the network interface controllers will use vanilla, unpatched macOS kexts. Using Rufus or unetbootin, create a live linux USB. I personally prefer Ubuntu, but it isn't required. Boot into the live linux USB and launch terminal.

*It is possible, although unlikely, to screw this up so badly that your ethernet controllers are rendered inoperable and irrecoverable. However, the ethtool is generally forgiving. If you do make a mistake, you can typically correct it with a subsequent flash command. Be patient, go slow, and be careful! To make things easier, I strongly recommend removing all other NIC's attached to the system before attempting this step. If you have any add-on USB or PCI / PCIe ethernet, WiFi, or Bluetooth adapters you will make this step much safer and easier by disconnecting them first.*

Your first set of commands are:

Code:

```
sudo -s
apt-get install ethtool net-tools
ifconfig
```

Identify how many LAN NIC's you have and their names. You will then run the following commands for each NIC, replacing "?????" with your NIC name.

Code:

```
ethtool -E ????? magic 0x10D38086 offset 0x16 value 0x00
ethtool -E ????? magic 0x10D38086 offset 0x17 value 0x00
ethtool -E ????? magic 0x10D38086 offset 0x1A value 0xF6
ethtool -E ????? magic 0x10D38086 offset 0x16 value 0x00
ethtool -E ????? magic 0x10D38086 offset 0x17 value 0x00
ethtool -E ????? magic 0x10D38086 offset 0x18 value 0x86
ethtool -E ????? magic 0x10D38086 offset 0x19 value 0x80
```

You can now shutdown and reuse the same USB to create your macOS installer. You should not need the live linux USB again.



**Step Two - Prepare your USB installer:**

Using the [installation guide](https://www.tonymacx86.com/threads/unibeast-install-macos-sierra-on-any-supported-intel-based-pc.200564/), create a USB drive with macOS. Choose "UEFI Boot Mode". After everything is complete, you need to [mount the ESP for your USB drive](https://www.tonymacx86.com/threads/how-to-mount-efi-partition.174321/) and place the unzipped contents of the attached BIOS.zip file in the root. Next, completely replace the /EFI/CLOVER/ directory with the attached CLOVER directory. After you copy over the contents of the CLOVER directory, you will need to update all of the installed kexts 

EXCEPT

 for USBInjectAll. USBInjectAll is out of date and no longer maintained, so the version attached to this post has been modified to work with MacPro7,1 SMBIOS. The standard version available to download from RehabMan will not work with the MacPro7,1. The other kexts (Lilu, Whatevergreen, VirtualSMC, CPUFriend) should all be updated to the newest releases before proceeding.



**Step Three - Configure config.plist:**

Using [Clover Configurator](http://mackie100projects.altervista.org/) (or manually, if thats your thing) you need to make a few changes to the config.plist. If you aren't familiar with a basic Clover installation, I recommend [reading up first](https://clover-wiki.zetam.org/Configuration). Regenerate the SMBIOS MacPro**7**,1 information in order to create your own serial number etc. This is a change from the older guide and is not a typo. We are using MacPro7,1 in order to best achieve GPU acceleration. Make sure that Clover Configurator does not change the value for "Platform Feature". If it does, change the value back to 0x18. The memory tab will not appear under "About This Mac" if this is incorrectly set. Also, create a custom UUID under System Parameters. Copy that UUID to the SmUUID field under SMBIOS and again generate a new UUID under System Parameters. [This thread](https://www.tonymacx86.com/threads/guide-how-to-configure-your-systems-smbios-correctly.198155/) provides far more depth on properly configuring your SMBIOS. No other changes to config.plist necessary unless otherwise specifically needed for your GPU.



**Step Four - Update BIOS and \**Marvell SE9230 Firmware\**:**

Update the EP2C602 BIOS using the file contained in the ESP of your USB drive. The instructions on how to do so are contained in the manufacturer's manual as well as [here online](http://www.asrockrack.com/support/faq.asp#InstantFlash).

*Flashing BIOS, especially modded or unofficial builds, has an inherent degree of risk. Neither I, nor anyone else on the planet, is responsible if you damage your computer, self, or relationship as a result. The provided BIOS has been modified to add NVMe boot support and to unlock the MSR.*

Next, update the Marvell SE9230 storage controller firmware. [Here is the link](http://www.asrockrack.com/support/faq.asp#InstantFlash) to the files and instructions.



**Step Five - Configure BIOS:**

Using the attached PDF guide, make all necessary changes to your BIOS configuration. Can you deviate from this guide? Maybe, depends on what you want to change. Some settings will not change the ability to install macOS but will effect performance. Some changes will complete break your ability to install macOS. My recommendation is to follow my BIOS settings exactly until you have a stable system. Feel free to tweak and play iteratively later. If you are curious about a specific setting, ask in this thread. After everything is correctly configured, save and reboot.



**Step Six - Booting into Clover:**

We are now ready to boot into Clover but not quite ready to install macOS. If you continued on at this stage, you would encounter a kernel panic. While in the Clover main boot screen, [press F4 to dump the ACPI](https://www.tonymacx86.com/threads/pressing-f4-when-booting-clover.140954/#post-867355). You will need it later. You do not need to calculate a slide value or provide a fixed slide value in your config.plist as was suggested in older versions of this guide.



**Step Seven - Install macOS:**

Now that we have our configured Clover boot loader, macOS should boot into the installer without issue. OS installation should be relatively straight forward to anyone who has installed any version of OS X / macOS previously. If you need help, use [this guide](https://www.tonymacx86.com/threads/unibeast-install-macos-sierra-on-any-supported-intel-based-pc.200564/#install).


**Post-Installation**

**Step Eight - Install Clover in the boot drive ESP:**

Using the installation guide referenced in Step Two, install Clover on to the drive with your macOS installation so that you can boot without the USB drive. Copy all the files you used on the USB to the EFI partition for the boot drive. Remove the USB drive and reboot.

**Step Nine - Fix CPU Power Management:**
Using [Piker-Alpha's instructions](https://github.com/Piker-Alpha/ssdtPRGen.sh), generate a SSDT to fix CPUPM. However, I recommend using the "custom" flag method so that you can provide the script your original, unpatched ACPI. In order for the custom flag to function, you must copy the contents of /EFI/CLOVER/ACPI/origin to your desktop. If the origin folder is empty, you didn't dump the ACPI using F4 at the Clover boot menu as described in Step Six. Run the script with the "-h" help flag to see all available flags. I suggest using the relevant flags for model and board-id (copy from your config.plist), cpu's, mode, and processor model. After the script generates your SSDT, you may delete the ACPI files from your desktop and delete NullCPUPowerManagement from /EFI/CLOVER/kexts/Other/. Rename the newly generated SSDT to SSDT-CPUPM.aml and move it to /EFI/CLOVER/ACPI/patched/. After creating the SSDT-CPUPM.aml, you will want to modify the file to work with CPUFriend. [Follow the guide](https://github.com/acidanthera/CPUFriend/blob/master/Instructions.md) from the makers of CPUFriend. Reboot once everything is done.

**Step Ten - Fix UEFI Boot:**
ASRock motherboards have trouble correctly recognizing your Clover installation in the ESP as a UEFI bootable operating system. Without a fix, you will be stuck booting Clover from your USB drive. To fix the problem, you will need to use bcfg commands from within the UEFI shell available in Clover. A basic guide is [here](https://www.tonymacx86.com/threads/anyone-with-asrock-z77-pro3-motherboard.176172/#post-1130914).

**Change Log**
12/20/2019 - Initial post.
01/14/2020 - Added newest attachments.
03/07/2020 - Replaced BIOS Guide.