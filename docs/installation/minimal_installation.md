# Basic installation of an x86_64 machine
This page describes how to install a fresh and minimal Arch Linux system for x86_64 machines on a single drive (SSD or HDD).

Only an EFI configuration is supported.
> You can check in which mode you've booted: If the folder `/sys/firmware/efi` exists, the kernel has booted in EFI mode, else it's booted in BIOS mode.

## Download the most recent iso
Go to https://www.archlinux.org/download/ and download the most recent iso file

## Write the iso file to a USB stick
```bash
dd if=<isofile> of /dev/sda bs=4M
```

... boot the device from the USB stick

## Determine and define the drive to install on
You can use `lsblk -f` to determine the drive to install to.
When the drive is determined as i.e. `/dev/sdX` do:
```bash
export DRIVE=/dev/sdX # or i.e. /dev/mmcblk0 for eMMC devices
BOOT=${DRIVE}1 # or ${DRIVE}p1 for eMMC devices
ROOT=${DRIVE}2 # or ${DRIVE}p2 for eMMC devices
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
1. `w`
    1. Y

## Preparing filesystems
### Format Boot partition
```bash
mkfs.fat -F32 $BOOT
```

### Format & Mount Root partition
```bash
mkfs.ext4 -L ROOT $ROOT
mount -o noatime $ROOT /mnt
```

### Mount Boot partition
```bash
mkdir /mnt/boot
mount -o noatime $BOOT /mnt/boot
```

## Creating minimal environment
```bash
pacstrap -c /mnt linux pacman sed
genfstab -Up /mnt >> /mnt/etc/fstab
```

## Initialize bootloader configuration in Bootstrap environment
### Bootstrap into target environment
```bash
bootctl install /mnt/boot
```

### Initialize bootloader configuration
```bash
cp /usr/share/systemd/bootctl/loader.conf  /mnt/boot/loader/loader.conf
tee -a /mnt/boot/loader/entries/arch.conf > /dev/null <<EOF
title          Arch Linux
linux          /vmlinuz-linux
initrd         /initramfs-linux.img
options        root=LABEL=ROOT rootfstype=ext4 rw init=/usr/lib/systemd/systemd
EOF
```

### Finalize
```bash
reboot
```

For configuration of installation essentials, please see [Installation Essentials](essentials-installation.md)
