# How To Fix Windows Not Booting from GRUB in Windows/Linux Dualboot (dualboot is done on a singular drive)

This is a guide on how to fix Windows not booting when selected from the GRUB menu, in a Windows/Linux dualboot, if the dualboot is done on the same drive. Additionally, this guide also assumes you use the GRUB menu to boot into both Linux and Windows, and not your BIOS's boot menu. It also assumes your EFI partition is shared by both Windows and Linux/GRUB. This guide will not work for those who use their boot menu to boot into Windows, or who have separate EFI partitions for both Windows and GRUB.

# ⚠️ IMPORTANT DISCLAIMER ⚠️
I do **not** reccomend using GRUB to boot into Windows. Through my own testing, I have found that after 2-3 restarts, Windows will completely fail to boot from the GRUB menu. As a result, you will need to either do this fix again, or recreate the boot menu entry in Linux. What I reccomend is setting Windows as the default bootloader, and then whenever you wish to boot into Linux, use your BIOS's boot menu for this. A guide for that is below the fix.

# Guide for Fixing Windows

## 1. Keep attempting to boot into Windows

If you are on a black screen, with no loading whatsoever, or if you are stuck on a screen that says "Preparing Automatic Restart", forcefully restart your computer by holding the power button to turn it off and on again, and then keep trying to boot into Windows. Windows should eventually tell you that your PC needs to enter recovery mode. To do so, press `F1` on your keyboard. It will then restart, and take you to your GRUB menu. Select Windows. It should show text saying "Please Wait".

After some time, Windows should take you there. Select "Troubleshoot", and then, select "Command Prompt". A terminal window will open. A majority of the guide will be done here.

## 2. Identify Partitions

- Open diskpart: `diskpart`
  
- List your volumes: `list vol`
  

Look for the number of your EFI partition. It should be the partition that is only 100 MB. Afterwards, select it:

`select vol EFI_VOLUME_NUMBER` 
(Replace `EFI_VOLUME_NUMBER` with the volume number of your EFI partition)

## 3. Assign Temporary Letters

Assign this temporary drive letter to your EFI partition:
`assign letter=S`

Go back to the output of `list vol`, or run the command again. Afterwards, identify your Windows partition. It should be the largest one. 
Select your Windows partition:

`select vol WINDOWS_VOLUME_NUMBER` (Replace `WINDOWS_VOLUME_NUMBER` with the volume number of your Windows partition)

Assign this temporary drive letter to the Windows partition:

`assign letter=Z`

Exit diskpart:

`exit`

## 4. Verify

To make sure you've selected your Windows installation, run this:

`dir Z:\` 

You should see folders placed within the Windows root, such as Program Files, Users, etc.

## 5. Rebuild Windows Boot Files

Now, this is the part where you actually fix your Windows boot files. Run this command:

`bcdboot Z:\Windows /s S: /f UEFI`

## 6. Finishing Up

Now, exit out of the terminal, and select "Continue". This will restart your computer. Select Windows in the GRUB menu entry, and it should now boot.

# Guide for Setting Windows First

If you wish to set Windows as the primary bootloader, instead of GRUB, follow this.

## 1. EFI Boot Manager
Boot into Linux, and open your terminal. Run `efibootmgr`. This will give you an output of several entries in your boot order. They are labelled `0000`,`0001`,`0002`, and so on.

## 2. Assign Windows First

Take note of the number given to Linux, and Windows. Run this command:

`sudo efibootmgr -o [WINDOWS],[LINUX]`

For example, if Windows's boot order number is `0006`, and Linux is `0001`, then this command would look like:

`sudo efibootmgr -o 0006,0001`

### Important Detail
There is no space between the comma. The command is to be formatted the exact way as described here.

## 3. Restart
Restart your computer. Windows should automatically boot. To access the GRUB menu, press `F9`, `F12`, `Esc`, `Del`, or any other key, respective to your device manufacturer. 

## 4. Verify (Optional)
Now, we will verify if Windows's boot entry is correct. However, if you have successfully booted into Windows, it is more than likely it is, and this step is entirely optional.

Inside Windows, open an admin command prompt (`cmd`, run as administrator), and run the following:

`bcdedit /enum {bootmgr}`

Within the line of `path`, it should show this directory:

`\EFI\Microsoft\Boot\bootmgfw.efi`

If not, correct this with:

`bcdedit /set {bootmgr} path \EFI\Microsoft\Boot\bootmgfw.efi`

Now, run the command again:

`bcdedit /enum {bootmgr}`

`path` should now show the correct directory.
