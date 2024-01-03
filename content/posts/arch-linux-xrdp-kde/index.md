---
title: "Arch Linux XRDP KDE"
date: 2024-01-03T15:04:27-05:00
draft: false
---

Install `xrdp`:

```shell
yay -S xrdp xorgxrdp
```

Enable `xrdp` and `xrdp-sesman`:

```shell
sudo systemctl enable xrdp xrdp-sesman
```
