---
title: Packard Bell Easy One DC on Slackware 10.0
date: 2008-07-31
authors:
    - jorge
categories:
    - Linux
---
Packard Bell Easy One DC running Slackware Linux 10.0 with Linux kernel 2.4.26

- Mobile AMD Duron 700 Mhz
- VIA Apollo KT133 chipset
- LCD 12.1” TFT SVGA , 800×600 pixel
- 192 MB SDRAM (100MHz)
- 6GB HDD
- ATI Rage Mobility-M1 4MB APG Video Controller
- Versaglide Touchpad / NX Pad
- OZ6833 PC-Card Controller
- VIA 686A PCI Audio Controller
- Internal 24x CD-ROM drive
- USB 1.0
- Askey LT Winmodem (DSP1456/MARS3)
- 2 Internet buttons
- Super Input/Output Controller SMC FDC37N869

A list of built-in PCI hardware:

```
00:00.0 Host bridge: VIA Technologies, Inc. VT8363/8365 [KT133/KM133] (rev 03)
00:01.0 PCI bridge: VIA Technologies, Inc. VT8371 [KX133 AGP]
00:06.0 Communication controller: Lucent Microelectronics LT WinModem
00:07.0 ISA bridge: VIA Technologies, Inc. VT82C686 [Apollo Super South] (rev 22)
00:07.1 IDE interface: VIA Technologies, Inc. VT82C586A/B/VT82C686/A/B/VT823x/A/C/VT8235 PIPC Bus Master IDE (rev 10)
00:07.2 USB Controller: VIA Technologies, Inc. VT6202 [USB 2.0 controller] (rev 10)
00:07.4 ISA bridge: VIA Technologies, Inc. VT82C686 [Apollo Super ACPI] (rev 30)
00:07.5 Multimedia audio controller: VIA Technologies, Inc. VT82C686 AC97 Audio Controller (rev 20)
00:0c.0 CardBus bridge: O2 Micro, Inc. OZ6832/6833 Cardbus Controller (rev 34)
00:0c.1 CardBus bridge: O2 Micro, Inc. OZ6832/6833 Cardbus Controller (rev 34)
01:00.0 VGA compatible controller: ATI Technologies Inc Rage Mobility P/M AGP 2x (rev 64)

```

#### X11R6.7.0 (X.Org)

I configured X11R6.7.0 (xorgconfig) with the ati generic driver and it worked fine.

From my Section “Device” in /etc/X11/xorg.conf

```
Identifier  "** ATI (generic)               [ati]"
Driver      "ati"

```

#### Audio

The built-in audio card is an VIA686 PCI Audio Controller and is supported by the snd-via82xx module in the kernel.

```
ALSA device list:
#0: VIA 82C686A/B rev20

```

#### Internal 24x CD-ROM drive

Seems to work fine, and was found by kernel. No problem with playing any movies or sound.

```
hdc: ATAPI 24X CD-ROM drive, 128kB Cache, UDMA(33)
Uniform CD-ROM driver Revision: 3.12

```

#### Modem / PCMCIA

These are the things I’ve not had the opportunity to test myself, but I’ll attempt to give some information about them below.

- The integrated modem seems to be recognized by the kernel.
- I’ve not had the chance yet to test PCMCIA, but after a few searches on google, it seems like it doesn’t take much to make it work.

Should there be anything you would like to add to this document, please post a comment.

[Back to Linux-on-Laptops.com](http://www.linux-laptop.net/pb.html)

