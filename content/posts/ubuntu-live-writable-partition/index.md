---
title: "Ubuntu live USB drive with cross-platform writable partition"
summary: "Create an Ubuntu live USB drive with a writable data partition compatible with Linux, Windows and Mac."
date: 2024-05-09T23:10:17-04:00
draft: false
---

## Partition the USB drive

Identify which external USB drive you intend to use - for this post, I'll assume it's called `/dev/sda` - but make sure you've selected the right one. You can check the drives you have with:

```text
sudo fdisk -l
```

Using `cfdisk`, partition the drive:

```text
sudo cfdisk /dev/sda
```

Create a 6GB partition `/dev/sda1` - just larger than the Ubuntu ISO, which is 5.7GB for Ubuntu 24.04. Mark the partition type as `Linux filesystem`.

Create a second partition `/dev/sda2` in the remaining space. Mark the partition type as `Microsoft basic data` - this is crucial. While macOS can read/write an ExFAT partition, macOS can't mount the partition unless it's marked as `Microsoft basic data`.

## Write Ubuntu ISO to drive

Write the Ubuntu ISO using `dd` to the 6GB partition we created (`/dev/sda1`):

```text
sudo dd bs=4M if=~/iso/ubuntu-24.04-desktop-amd64.iso of=/dev/sda1
```

## Boot into Ubuntu live disk

Once booted into the live disk, pull up the terminal, update ubuntu's repos, then install `exfat-fuse`:

```text
sudo apt update
sudo apt install exfat-fuse
```

## Format the data partition as `exfat`

Technically you could have formatted the `/dev/sda2` partition as exfat after initially creating the partition, but I did it from within the Ubuntu live session because - in the particular situation I was faced with - I could not install the exfat package on an old machine where aptitude was severely broken, and didn't have access to another Linux machine at the time (though had access to a Mac).

Now you can format the data partition with `mkfs.exfat`:

```text
sudo mkfs.exfat /dev/sda2
```

Once this is done, you'll be able to mount the partition `/dev/sda2` and read from and write to it right from within the Ubuntu live session, which is running from the same drive, from the ISO image that you initially wrote to `/dev/sda1`. Whatever you save on `/dev/sda2` will be persisted and can be read from any other OS - including other Linux distros, macOS and Windows - as they all support the ExFAT format.
