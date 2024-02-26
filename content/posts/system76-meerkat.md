---
title: "System76 Meerkat Dual-Boot Linux and Windows"
summary: "Dual-boot Linux and Windows on System76 Meerkat."
date: 2024-02-26T13:52:41-05:00
draft: false
---

Recently my parents got a [System76 Meerkat](https://system76.com/desktops/meerkat). They wanted to dual-boot Ubuntu and Windows 11 on it.

By the way, [System76](https://system76.com/) makes absolutely amazing Linux machines. The next time I refresh my hardware, I will be buying from them! I'm actually kind of jealous my parents got a System76 machine before I did!

I wiped the drive to partition it the way they needed, installed Ubuntu and Windows, but immediately ran into a challenge on Windows - finding the right drivers for it. In particular, finding the audio drivers was an absolute nightmare, but I was eventually able to piece together enough hardware info to find all the right drivers.

## Meerkat Hardware Notes

- System76 publishes [specs](https://tech-docs.system76.com/README.html) for their hardware. [Here's the spec sheet for Meerkat](https://tech-docs.system76.com/models/meer8/README.html).
- Meerkat is a rebranded Intel NUC system. The one my folks got is `NUC13ANBi5`.
- System76 has a [github repo for Windows drivers](https://github.com/system76/windows-drivers), but it doesn't list anything for Meerkat.
- The toughest hardware component by far to figure out in Windows was audio - Meerkat uses Intel SST (Smart Sound Technology), but finding the right drivers for it was not easy - more on that below.

## Installing Linux

Of course, it came with Ubuntu preinstalled, just as we selected, but I wanted to upgrade it to Ubuntu 23.10 (it was on the 22.04 LTS version) and repartition the drive for dual-booting with Windows. Ubuntu 23.10 installed just fine, and everything worked out of the box - I expected no less from System76! That being said, after installing Ubuntu, I also installed the `system76-driver` package - which also includes their wallpapers, by the way. :wink:

[Support Link](https://support.system76.com/articles/system76-driver)

```shell
sudo apt-add-repository -y ppa:system76-dev/stable
sudo apt-get update
sudo apt install system76-driver
```

## Installing Windows

### Bypass Windows 11 internet requirement when finishing installation

Windows 11 requires internet to finish installation - a challenge because without network drivers, you can't connect to the internet. While I was able to install network drivers with the methods above after installation, I had to bypass Windows 11's internet check to even get there.

Why Windows 11 requires internet to install is beyond me. Honestly, setting this up made me really angry at Microsoft - you pay for an expensive Windows license, and they require you to have internet, spy on everything you do, and advertise to you ad nauseam. I've never much been impressed by Windows, and I use Linux and macOS as my daily drivers, but Windows 11 truly brings new meaning to the phrase *steaming pile of garbage*. :poop:

To bypass the Windows 11 internet requirement, press `SHIFT + F10` and in the command line window that appears, enter:

```text
OOBE\BYPASSNRO
```

Let it reboot and repeat the process, picking "I don't have internet" when it gets back to that point in the installation.

### Installing Windows 11 drivers

After installing Windows, you'll need to first install the Wifi drivers.

For the rest of the drivers, I'd start with:

1. [Intel DSA](https://www.intel.com/content/www/us/en/support/detect.html) to detect and install some of the important drivers.
2. Most Windows drivers are also officially available through [Asus NUC 13 Pro Kit Support](https://www.asus.com/us/displays-desktops/nucs/nuc-kits/nuc-13-pro-kit/helpdesk_download?model2Name=NUC-13-Pro-Kit) - however, these did not all work and they didn't have everything needed.
3. [Driver Booster](https://www.iobit.com/en/driver-booster.php) - This got all the rest of the drivers, though the program does push you pretty hard to buy the pro edition - which was not necessary for my purposes.

I also found [Snappy Driver Installer](https://sdi-tool.org/) which looked promising, but for the life of me I couldn't figure out how to get it to work! So I gave up on this one in favor of Driver Booster.

## Final thoughts

Overall, the Windows driver experience was a pain. But to be clear: I do not blame System76 for the Windows experience. They do not do Windows! They are all about Linux, and I love them for that. That being said, in this case my folks needed Windows for certain things, System76's Meerkat system was nice and small (and very affordable!) so we went this route.

System76 is awesome, and the Meerkat is a really nice machine. If you're not doing any gaming or anything super crazy, this machine is very small, yet very fast and comes loaded with ports and mem/storage!
