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


# Remove unneeded stuff

Frist it is worth checking what is eating up a lot of disk space, and removing the unnecessary.

https://askubuntu.com/questions/5980/how-do-i-free-up-disk-space


# Nullify free space

In the Linux Guest system:

- Log out from the gui
- Open shell (e.g., Host Key + F2)
- Nullify free space:

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

# If remount read-only fails

Edit fstab to have the mount point in question mounted read-only by default

```bash
vi /etc/fstab
```

Make the appropriate entry 'ro'

```
/dev/sda1  /   ext4   ro    0    1
```

Reboot and run `zerofree -v /dev/sda1` now

## To "recover" the system:

- Re-mount read-write: `mount -o remount,rw /dev/sda1`
- Edit `/etc/fstab` again to restore the original content


# Shutdown the Guest OS (Linux)

```bash
shutdown -h now
```

# Compact VDI with VBoxManage on the host

```
C:\Program Files\Oracle\VirtualBox>VBoxManage.exe modifyhd --compact "c:\path\to\linux-disk-image.vdi"
```
