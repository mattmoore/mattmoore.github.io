---
title: "Arch Linux KDE Plasma Installation"
summary: "Install KDE Plasma on Arch Linux"
date: 2024-01-03T15:00:15-05:00
tags:
    - "linux"
    - "arch linux"
draft: false
---

Install `xorg-server`:

```shell
pacman -S xorg-server
```

Install `plasma` and `kde-applications`:

```shell
pacman -S plasma kde-applications
```

Enable the default plasma display manager `sddm`:

```shell
sudo systemctl enable sddm
```

Then either start `sddm` (or `reboot`):

```shell
sudo systemctl start sddm
```
