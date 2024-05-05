---
title: "Ubuntu 24.04 LTS with btrfs subvolumes"
summary: "The Ubuntu 24.04 LTS installer does not support installing to btrfs subvolumes. Here's a workaround."
date: 2024-05-05T19:07:38-04:00
draft: true
---

## Backup your system

```text
tar -cvpzf ubuntu-24.04-lts-backup.tar.gz --exclude=/ubuntu-24.04-lts-backup.tar.gz --one-file-system / 
```

## Restore Your Backup

```text
# Mount @ubuntu subvolume
sudo mount -o subvol=@ubuntu /dev/sda2 /mnt

# Extract ubuntu backup to mounted subvolume
sudo tar -xvpzf ubuntu-24.04-lts-backup.tar.gz -C /mnt --numeric-owner
```

## Install bootloader, initramfs

```text
# Mount EFI partition
sudo mount -o /dev/sda1 /mnt/boot/efi

# Mount /dev, /proc, /sys
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys

# chroot into restored ubuntu environment
sudo chroot /mnt

# Install grub
install-grub

# Update initramfs
update-initramfs -u -k all

# Update grub config
update-grub

# Exit chroot environment
exit
```

## Create UEFI entry

```text
# View existing UEFI entries
efibootmgr

# Create Ubuntu UEFI entry
sudo efibootmgr -c -d /dev/sda2 -L Ubuntu -l '/EFI/ubuntu/shimx64.efi'

# Delete Ubuntu UEFI entry if you messed it up
sudo efibootmgr -B -b 0000
```
