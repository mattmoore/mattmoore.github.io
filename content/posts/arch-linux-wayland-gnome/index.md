---
title: "Arch Linux Wayland Gnome with NVIDIA"
summary: "Setting up Wayland in Arch Linux with Gnome/GDM on NVIDIA hardware (Alienware x17 R2)."
date: 2024-01-28T02:40:36-05:00
draft: false
---

I had some difficulty setting up Wayland under gnome on my Alienware x17 R2. For reference, here's my configuration:

```shell
Alienware x17 R2
------------------- 
OS: Arch Linux x86_64 
Host: Alienware x17 R2 1.15.1 
Kernel: 6.7.1-arch1-1 
Uptime: 44 mins 
Packages: 1175 (pacman) 
Shell: zsh 5.9 
Resolution: 5120x2160, 3840x2160 
DE: GNOME 45.3 
WM: Mutter 
WM Theme: Adwaita 
Theme: Adwaita [GTK2/3] 
Icons: Adwaita [GTK2/3] 
Terminal: kgx 
CPU: 12th Gen Intel i9-12900HK (20) @ 4.900GHz 
GPU: NVIDIA GeForce RTX 3080 Ti Laptop GPU 
GPU: Intel Alder Lake-P GT2 [Iris Xe Graphics] 
Memory: 8982MiB / 63959MiB
```

{{< alert >}}
**Note**: My laptop has an Intel and NVIDIA GPU in it. There are ways to get both chipsets to work, but I haven't had the time to look into it. Additionally, since this laptop is huge and has a beefy graphics card, I tend to leave it plugged in at my desk all the time. Consequently, I decided to simply disable the Intel GPU from the BIOS, leaving the NVIDIA GPU running all the time instead.

In other words, YMMV depending on your specific hardware configuration.
{{< /alert >}}

## Enable modeset by adding kernel parameters via Grub

Edit this file:

```shell
/etc/default/grub
```

and add `nvidia-drm.modeset=1` and `nvidia-drm.fbdev=1` like this:

```shell
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet nvidia-drm.modeset=1 nvidia-drm.fbdev=1"
```

Save and exit the file. Next, regenerate grub config:

```shell
grub-mkconfig -o /boot/grub/grub.cfg
```

Then reboot.

## If Wayland still doesn't load (Continues to use X11):

I had an issue with a udev rule that prevented Wayland from loading, even with the kernel parameters above. I found [this article on the Arch Wiki](https://wiki.archlinux.org/title/GDM#Wayland_and_the_proprietary_NVIDIA_driver).

In short, as it mentions, there was a udev rule that was causing my issue. So I overrode the rule as mentioned to get this to work:

```shell
ln -s /dev/null /etc/udev/rules.d/61-gdm.rules
```

Next, make sure GDM isn't configured to disable wayland. Remove the file `/etc/gdm/custom.conf`.

Finally, reboot.

## Final Notes

After getting GDM/Gnome/Wayland to play together with the proprietary NVIDIA drivers, I found a number of applications that were rather annoyingly glitchy. Chromium and Steam were wonky, and ended up crashing from time to time. Consequently, I ended up switching back to X11, which is far more stable at this time, at least with my hardware.

I am quite hopeful to see Wayland get better. Indeed, on an older desktop (also with an NVIDIA GPU), I found that Wayland was more stable. So hopefully soon Wayland will be better with my newer laptop.
