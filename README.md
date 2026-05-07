# How To Fix Windows Not Booting from GRUB in Windows/Linux Dualboot (if the dualboot is done on a singular drive)

This is a guide on how to fix Windows not booting when selected from the GRUB menu, in a Windows/Linux dualboot, if the dualboot is done on the same drive. Additionally, this guide also assumes you use the GRUB menu to boot into both Linux and Windows, and not your BIOS's boot menu. It also assumes your EFI partition is shared by both Windows and Linux/GRUB. This guide will not work for those who use their boot menu to boot into Windows, or who have separate EFI partitions for both Windows and GRUB.

# Guide

### Fix

### 1. Keep attempting to boot into Windows

If you are on a black screen, with no loading whatsoever, or if you are stuck on a screen that says "Preparing Automatic Restart", forcefully restart your computer by holding the power button to turn it off and on again, and then keep trying to boot into Windows. Windows should eventually tell you that your PC needs to enter recovery mode. To do so, press `F1` on your keyboard. It will then restart, and take you to your GRUB menu. Select Windows. It should show text saying "Please Wait".

After some time, Windows should take you there. Select "Troubleshoot", and then, select "Command Prompt". A terminal window will open. A majority of the guide will be done here.

### 2. Identify Partitions

- Open diskpart: `diskpart`
  
- List your volumes: `list vol`
  

Look for the number of your EFI partition. It should be the partition that is only 100 MB. Afterwards, select it:

`select vol EFI_VOLUME_NUMBER` 
(Replace `EFI_VOLUME_NUMBER` with the volume number of your EFI partition)

### 3. Assign Temporary Letters

Assign this temporary drive letter to your EFI partition:
`assign letter=S`

Go back to the output of `list vol`, or run the command again. Afterwards, identify your Windows partition. It should be the largest one. 
Select your Windows partition:

`select vol WINDOWS_VOLUME_NUMBER` (Replace `WINDOWS_VOLUME_NUMBER` with the volume number of your Windows partition)

Assign this temporary drive letter to the Windows partition:

`assign letter=Z`

Exit diskpart:

`exit`

### 4. Verify

To make sure you've selected your Windows installation, run this:

`dir Z:\` 

You should see folders placed within the Windows root, such as Program Files, Users, etc.

### 5. Rebuild Windows Boot Files

Now, this is the part where you actually fix your Windows boot files. Run this command:

`bcdboot Z:\Windows /s S: /f UEFI`

### 6. Finishing Up

Now, exit out of the terminal, and select "Continue". This will restart your computer. Select Windows in the GRUB menu entry, and it should now boot.
