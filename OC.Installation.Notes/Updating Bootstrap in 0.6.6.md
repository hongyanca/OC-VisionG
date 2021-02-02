https://dortania.github.io/OpenCore-Post-Install/multiboot/bootstrap.html#updating-bootstrap-in-0-6-6

## Updating Bootstrap in 0.6.6

For those updating to 0.6.6, you may have noticed Bootstrap.efi has been removed from OpenCore. This is due to changes with how OpenCore works; specifically OpenCore is now a UEFI application instead of a driver. This means OpenCore.efi can be loaded directly and the reliance on Bootstrap.efi is no longer needed.

### [#](https://dortania.github.io/OpenCore-Post-Install/multiboot/bootstrap.html#with-bootstrap-disabled)With Bootstrap disabled

If Bootstrap is disabled prior to updating to 0.6.6, you don't need to make any changes, just the usual file swapping. If afterwards you would like to try `LauncherOption`, you can do so with no issues.

### [#](https://dortania.github.io/OpenCore-Post-Install/multiboot/bootstrap.html#with-bootstrap-enabled)With Bootstrap enabled

If Bootstrap is enabled prior to updating to 0.6.6, and your motherboard's firmware will autodetect `EFI/BOOT/BOOTx64.efi`, you can do the following before you update:

1. Set `Misc -> Security -> AllowNvramReset` to `True` and `Misc -> Boot -> BootProtect` to `None`, then reset NVRAM (either outside of or in OpenCore) and boot. This will get rid of the old Bootstrap boot entry.

2. Update your OpenCore setup as normal, ensuring that you copy BOOTx64.efi from the OpenCore package to `EFI/BOOT/BOOTx64.efi` and set `Misc -> Boot -> LauncherOption` in your config.plist to `Full` (or `Short` if previously using `BootstrapShort`).

3. Reboot.

   On first boot you will need to boot from `EFI/BOOT/BOOTx64.efi`, but on subsequent boots you should see the LauncherOption entry created by OpenCore directly booting `OpenCore.efi`.

If your firmware does not automatically detect `EFI/BOOT/BOOTx64.efi` or you cannot put OpenCore's launcher there for any reason, you have multiple other options:

- Put `OpenShell.efi` on a USB, rename and move to `EFI/BOOT/BOOTx64.efi`, and follow the above steps, except insteading of selecting `BOOTx64.efi` from the boot menu, booting into the USB and launching OpenCore from there directly.
- Add a folder `EFI/OC/Bootstrap` and copy and rename BOOTx64.efi from the OpenCore package to `EFI/OC/Bootstrap/Bootstrap.efi`. Then, after updating your OpenCore setup, set `Misc -> Boot -> LauncherOption` to the appropriate option (`Full`, or `Short` if previously using `BootstrapShort`) and boot OpenCore. After your first boot, you should see a new OpenCore boot option added. You can then reset NVRAM in OpenCore (making sure to keep `LauncherOption` enabled so you don't delete the proper entry) to get rid of the old Bootstrap boot option.

Conversion notes:

| 0.5.8 - 0.6.5                   | 0.6.6+                         |
| :------------------------------ | :----------------------------- |
| Misc -> Security -> BootProtect | Misc -> Boot -> LauncherOption |
| Bootstrap                       | Full                           |
| BootstrapShort                  | Short                          |