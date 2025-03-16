---
title: Getting the HP USB-C/A Universal Dock G2 working with Fedora 41
date: 2025-03-14
authors:
    - jorge
categories:
    - Linux
tags:
    - linux
    - fedora
    - displaylink
    - docking-station
    - secureboot
    - dkms
    - hp
    - elitebook
    - usb-c
---

I have a HP Elitebook 840 G7 that I use along with a HP USB-C/A Universal Dock
G2. Now, this has been working fine in Windows, but when I changed to Fedora,
not so much. I had to plug in my HDMI cable directly to the laptop, and not the
docking station. It just wasn't working. This is what I did to fix the issue.

<!-- more -->

First, install **mokutil** and and **dkms**, then check if you have SecureBoot enabled:

```bash
sudo dnf -y install mokutil dkms
sudo mokutil --sb-state
```

If SecureBoot is enabled, it should return:

```
SecureBoot enabled
```

Now download the latest displaylink-rpm from
https://github.com/displaylink-rpm/displaylink-rpm/releases. In this case we
need to download:

https://github.com/displaylink-rpm/displaylink-rpm/releases/download/v6.1.0-3/fedora-41-displaylink-1.14.8-1.github_evdi.x86_64.rpm

Now install the package:

```bash
sudo dnf install fedora-41-displaylink-1.14.8-1.github_evdi.x86_64.rpm
```

Make sure that the following files exist. If they do not, download the RPM
again and give it another shot:

```
/var/lib/dkms/mok.key
/var/lib/dkms/mok.pub
```

If they exist, great! Now we have to set a password. Run the command:

```bash
sudo mokutil --import /var/lib/dkms/mok.pub
```

Enter a password of your choice, twice.

Now, reboot.

When you do so, you will get to the MOK Manager EFI interface. Press any key
before the system boots. If you can't make it, no worries. Just import the
mok.pub file again, and reboot. Then select:

- Enroll MOK
- Continue
- Yes to Enroll the keys
- Type in the password you provided
- Reboot

When the system is up, run:

```bash
sudo dkms autoinstall
```

..to build and sign evdi module by MOK.

Everything should then be set. Verify that everything works by running:

```bash
sudo dkms status
```

the output should be something like:

```
evdi/1.14.8 6.12.6-200.fc41.x86_64, x86_64: installed
```

Now connect your HDMI cable to the docking station, and the USB-C to your
laptop. It should work! If not, reboot, and give it another try.

Good luck!
