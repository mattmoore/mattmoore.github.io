---
title: "ZSA Voyager"
summary: "ZSA Voyager split keyboard"
date: 2024-03-06T10:23:11-05:00
draft: false
---

udev rules:

`/etc/udev/rules.d/50-zsa.rules`:

```text
# Rules for Oryx web flashing and live training
KERNEL=="hidraw*", ATTRS{idVendor}=="16c0", MODE="0664", GROUP="plugdev"
KERNEL=="hidraw*", ATTRS{idVendor}=="3297", MODE="0664", GROUP="plugdev"

# Keymapp Flashing rules for the Voyager
SUBSYSTEMS=="usb", ATTRS{idVendor}=="3297", MODE:="0666", SYMLINK+="ignition_dfu"
```
