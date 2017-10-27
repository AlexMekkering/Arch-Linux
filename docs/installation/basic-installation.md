# Basic installation of an x86_64 machine
This page describes how to install a fresh and minimal Arch Linux system for x86_64 machines on a single drive (SSD or HDD).

Both EFI and BIOS configurations are supported. Only the boot loader (GRUB) needs some different arguments. (see [EFI](#efi) and [BIOS](#bios))
> You can check in which mode you've booted: If the folder `/sys/firmware/efi` exists, the kernel has booted in EFI mode, else it's booted in BIOS mode.

> You can even install a hybrid EFI/BIOS system (thus bootable in both EFI and BIOS mode) by installing in one mode (i.e. BIOS), then reboot in the other (i.e. EFI) and do the [Install GRUB bootloader](#install-grub-bootloader) part again.

Both Ext4 and Btrfs root file systems are described, so you can make a choice.
>  On this page, Ext4 is taken as de facto standard for installations but Btrfs could be considered for its snapshot and volume support and transparent compression.

## Determine and define the drive to install on
You can use `lsblk -f` to determine the drive to install to.
When the drive is determined as i.e. `/dev/sdX` do:
```bash
export DRIVE=/dev/sdX
BOOT=${DRIVE}1
ROOT=${DRIVE}2
```

## Partitioning
Call:
```bash
gdisk $DRIVE
```
... and configure as follows:
1. `o`
    1. Y
1. `n`
    1. _default_
    1. 4M
    1. +124M
    1. ef00
1. `n`
    1. _all defaults_
1. `n`
    1. _default_
    1. _default_
    1. _default_
    1. ef02
1. `w`

## Preparing filesystems
### Format Boot partition
```bash
mkfs.fat -F32 $BOOT
```

### Format & Mount Root partition
#### Ext4
```bash
mkfs.ext4 -L OS $ROOT
mount -o noatime $ROOT /mnt
```
#### Btrfs
```bash
OPTIONS=noatime,space_cache,compress=lzo,subvol=subvol_root
mkfs.btrfs -L OS $ROOT
mount $ROOT /mnt
btrfs subvolume Create /mnt/subvol_root
umount /mnt
mount -o $OPTIONS $ROOT /mnt
```

### Mount Boot partition
```bash
mkdir /mnt/boot
mount -o noatime $BOOT /mnt/boot
```

## Creating minimal environment
```bash
pacstrap -c /mnt linux grub sed pacman btrfs-progs efibootmgr
genfstab -Up /mnt >> /mnt/etc/fstab
```
> You can omit the btrfs-progs package when not using btrfs.

> You can omit the efibootmgr package when booting in BIOS mode.

## Initialize bootloader configuration in Bootstrap environment
### Bootstrap into target environment
```bash
arch-chroot /mnt
```

### Install GRUB bootloader
#### EFI
```bash
grub-install --target=x86_64-efi --efi-directory=/boot\
 --bootloader-id=grub --recheck
```
#### BIOS
```bash
grub-install --recheck $DRIVE
```

### Initialize GRUB bootloader configuration
```bash
sed -i 's\GRUB_CMDLINE_LINUX_DEFAULT="\&init=/usr/lib/systemd/systemd \'\
 /etc/default/grub
grub-mkconfig -o /boot/grub/grub.cfg
exit
```

### Finalize
```bash
reboot
```

For configuration of installation essentials, please see [Installation Essentials](essentials-installation.md)
