---
title: "Arch Linux XRDP KDE"
summary: "Install xrdp on Arch Linux KDE"
date: 2024-01-03T15:31:27-05:00
tags:
    - "linux"
    - "arch linux"
draft: false
---

xrdp is for connecting remotely to a Linux desktop environment via remote desktop protocol.

{{< alert >}}
**Note:** You cannot connect via xrdp while also logged in physically to the machine.
{{< /alert >}}

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

{{< alert >}}
**Note:** Some folks say to add these entries `/etc/X11/Xwrapper.config`:

```text
allowed_users=anybody
needs_root_rights=no
```

You can do this, but it's not recommended (even though the Arch Wiki lists it). Instead, you should change `param` in `/etc/xrdp/sesman.ini`:

```text
param=/usr/lib/Xorg
```

{{< /alert >}}

Add this to `~/.xinitrc`:

```text
/usr/lib/plasma-dbus-run-session-if-needed startplasma-x11
```

After editing `~/.xinitrc`, `chmod +x ~/.xinitrc`.

If you encounter authorization errors upon connecting remotely, add the following Policy Kit rule:

```shell
/etc/polkit-1/rules.d/45-colord.rules
```

```text
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
