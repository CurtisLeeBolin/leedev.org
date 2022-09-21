---
layout: post
title: 'MS Windows: Convert MBR/BIOS to GPT/UEFI'
date: '2022-09-21 08:32:21'
---

This is a huge pain with MS Windows. The MS Windows tools often fail with no way to finish with only using MS Windows tools. The setup I wanted is not even possible with only MS tools.  I had to use a live Linux ISO to make some changes.  I am running MS Windows in a VM.

#### NOTE: One boot of a Live Linux ISO and running a few commands is all that would be needed for Linux.


# Clone the VM in Libvirt Manger

Right click on the VM and click clone

# Create a MS Windows `recovery drive`

Boot the new VM

Login to MS Windows

Create a 4GB emulated USB storage device in Libvirt Manager

Click the Start Button

Search `create a recovery drive`

Click the search result of the same name

I unchecked `Back up system files to the recovery drive.`

Finish creating the `recovery drive`

It actually only used 430MB, but MS's documentation suggested 4GB

Shutdown the VM


# GPT Requires Space at the End of the Storage Device and More Space at the Beginning than MBR

MS's `mbr2gpt` is incapable of correcting this.

Boot a Live Linux ISO with `GParted`

Add empty space to the beginning and end of the storage device

I added 257MB of empty space to the beginning for the future UEFI partation

I added 1MB of empty space to the end

It took a while to move the Windows Partition 257MB to the right

Shutdown the VM


# Windows Won't Boot, Fix It

MS's `mbr2gpt` also fails until this is corrected.

Boot the MS Windows `recovery drive`

Troubleshoot -> Command Prompt

Rewrite the Boot Sector

```
> bootsect /nt60 all /mbr
```

Add the entry into MS Windows's Boot Menu

```
> bcdboot C:\windows /s C:
```

Shutdown the VM


# Remove the Old MS Boot Menu Entry

Boot the new VM

Select the MS Windows boot menu boot entry with `Volume 1`

Login to MS Windows

Open CMD as Administrator

View boot menu entries

```
> bcdedit
```

Find the `identifier` of the old entry, not `{current}` or `{bootmgr}`

Delete the old entry

```
> bcdedit /delete {eb9a84dd-716a-11eb-8036-f51dc98f7c33}
The operation completed successfully.
```

Shutdown the VM


# Convert the MBR to a GPT

Boot the MS Windows `recovery drive`

Troubleshoot -> Command Prompt

Check and repair the MS Windows filesystem, or `mbr2gpt` will surely fail

```
> chkdsk /F C:
```

Now actually run the conversion

```
> mbr2gpt /convert
```

`mbr2gpt` annoyting shrinks the MS Windows partition and places the UEFI partition at the end.

Shutdown the VM


# Delete the Newly Created UEFI Partition and Create a New One at the Beginning

Boot a Live Linux ISO with `GParted`

Delete the UEFI partition

Create a new partition at the beginning with the `fat32` filesystem

Resize the MS Windows filesystem and partition to fill the empty space at the end

Apply these changes

Now set the `esp` and `boot` flags for the newly created UEFI partition

Shutdown the VM


# Recreate the UEFI data in the filesystem of the UEFI partition

Boot the MS Windows `recovery drive`

Troubleshoot -> Command Prompt

List `disk volume`s

```
> diskpart
DISKPART> select disk 0
DISKPART> list volume
```

You will see the UEFI partition's filesystem is now mounted

Mount the filesystem

```
DISKPART> select volume 2
DISKPART> assign letter Z:
DISKPART> exit
```

Write the data to the UEFI partition's filesystem

```
> bcdboot C:\windows /l en-us /s Z: /f UEFI
```


# Booting as UEFI

Delete the new VM in Libvirt Manger

Do not delete the OS image

Create a new VM with UEFI boot using this image
