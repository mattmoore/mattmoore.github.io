---
title: "Arch Linux yay"
summary: "Install yay on Arch Linux"
date: 2024-01-03T15:21:34-05:00
draft: false
---

Install `yay`:

```shell
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

Verify `yay`:

```shell
yay --version
```

Clean up:

```shell
cd ..
rm -rf yay
```
