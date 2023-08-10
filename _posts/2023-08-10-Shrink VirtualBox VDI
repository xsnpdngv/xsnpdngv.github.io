---
title: Shrink VirtualBox disk image
excerpt: Process of making dynamically growing vdi image smaller.
categories:
  - development
tags:
  - virtualbox
  - vdi
  - shrink
---

{% include toc %}

# Nullify free space

In the Linux Guest system (after logging out from the gui)

```bash
dd if=/dev/zero of=/var/tmp/bigemptyfile bs=4096k ; rm /var/tmp/bigemptyfile
```
Note that dd will use up all the free space

Or

```bash
telinit 1
mount -o remount,ro /dev/sda1
zerofree -v /dev/sda1
```

# Shutdown the Guest VM

```bash
shutdown -h now
```

# Compact VDI with VBoxManage on the host

```
C:\Program Files\Oracle\VirtualBox>VBoxManage.exe modifyhd --compact "c:\path\to\linux-disk-image.vdi"
```
