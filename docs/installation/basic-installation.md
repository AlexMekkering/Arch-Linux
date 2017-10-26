# Basic installation
This page describes how to install a fresh and minimal Arch Linux system on a single drive (SSD or HDD).

Both BIOS and EFI boot configurations are supported, only the boot loader (GRUB) needs some different arguments. (see [BIOS](#bios) and [EFI](#efi))
> You can check in which mode you've booted: If the dir `/sys/firmware/efi` exists, the kernel has booted in EFI mode, else it's booted in BIOS mode.

> You can even install a hybrid EFI/BIOS system (thus bootable in both EFI and BIOS mode) by installing in one mode (i.e. BIOS), then reboot in the other (i.e. EFI) and do the [Install GRUB bootloader](#install-grub-bootloader) part again.

Both Ext4 and Btrfs root file systems are described, so you can make a choice (I'd recommend Btrfs for its snapshot and volume support, transparent compression and check-summed error detection).

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
```
mkfs.fat -F32 $BOOT
```

### Format & Mount Root partition
#### Btrfs
```
mkfs.btrfs -L OS $ROOT
mount $ROOT /mnt
btrfs subvolume Create /mnt/subvol_root
umount /mnt
mount -o noatime,space_cache,compress=lzo,subvol=subvol_root $ROOT /mnt
```

#### Ext4
```
mkfs.ext4 -L OS $ROOT
mount -o noatime $ROOT /mnt
```

### Mount Boot partition
```
mkdir /mnt/boot
mount -o noatime $BOOT /mnt/boot
```

## Creating minimal environment
```
pacstrap -c /mnt linux grub sed pacman btrfs-progs efibootmgr
genfstab -Up /mnt >> /mnt/etc/fstab
```
> You can leave the btrfs-progs package when not using btrfs.

> You can leave the efibootmgr package when booting in BIOS mode

## Initialize bootloader configuration in Bootstrap environment
### Bootstrap into target environment
```
arch-chroot /mnt
```

### Install GRUB bootloader
#### BIOS
```bash
grub-install --recheck $DRIVE
```

#### EFI
```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub --recheck
```

### Initialize GRUB bootloader configuration
```
sed -i 's\GRUB_CMDLINE_LINUX_DEFAULT="\&init=/usr/lib/systemd/systemd \' /etc/default/grub
grub-mkconfig -o /boot/grub/grub.cfg
exit
```

### Finalize
```
reboot
```

For configuration of installation essentials, please see [Installation Essentials](essentials-installation.md)
