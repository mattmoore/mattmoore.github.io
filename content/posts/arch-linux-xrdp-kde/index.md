---
title: "Arch Linux XRDP KDE"
summary: "Install xrdp on Arch Linux KDE"
date: 2024-01-03T15:31:27-05:00
draft: false
---

Install `xrdp` and `xorgxrdp` from AUR:

```shell
yay -S xrdp xorgxrdp
```

{{< alert >}}
**Note:** This part is easy to miss and important. During install of `xrdp` and `xorgxrdp`, you might notice errors about needing to finish installing libs with `libtool --finish`. These are some of the directories I've noticed that needed it:

```shell
libtool --finish /usr/lib
libtool --finish /usr/lib/xrdp
libtool --finish /usr/lib/xorg/modules
libtool --finish /usr/lib/xorg/modules/drivers
libtool --finish /usr/lib/xorg/modules/input
```

If you get issues with TLS/SSL failures, black screen, etc...pay attention to the above.
{{< /alert >}}

Enable `xrdp` and `xrdp-sesman`:

```shell
sudo systemctl enable xrdp xrdp-sesman
```

`/etc/X11/Xwrapper.config`:

```shell
allowed_users=anybody
needs_root_rights=no
```

`/etc/xrdp/sesman.ini`:

```shell
param=/usr/lib/Xorg
```

`~/.xinitrc`:

```shell
/usr/lib/plasma-dbus-run-session-if-needed startplasma-x11
```

If you encounter authorization errors upon connecting remotely:

`/etc/polkit-1/rules.d/45-colord.rules`:

```shell
polkit.addRule(function(action, subject) {
    if ((action.id == "org.freedesktop.color-manager.create-device" ||
         action.id == "org.freedesktop.color-manager.create-profile") &&
        subject.isInGroup("wheel"))
    {
        return polkit.Result.YES;
    }
});
```

Then, reboot the system:

```shell
sudo reboot
```
