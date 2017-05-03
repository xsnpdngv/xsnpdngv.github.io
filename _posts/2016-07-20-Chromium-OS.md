---
title: Installing Chromium OS
categories:
  - development
tags:
  - dev
  - chrome
  - linux
  - ubuntu
---

[NeverWare](http://www.neverware.com) has built a Chromium OS based image to be
easily installed, named CloudReady. Its home edition is free. It is available
for both 32 and 64 bit. The image will work on most machines.

{% include toc %}

# Considerables

- Boot options: Dual boot with Windows (>XP), or Standalone.

- Users: The user added at first is going to be the owner of the install, so it
  cannot be removed. Another users can be added too but those can be easily
  removed too.

- Local storage: Everything under `Downloads` is locally stored. The whole
  content of the local storage is encrypted, so one does not simply put out the
  HDD and access its content.

- Media: Adobe flash and MP3 codecs can be easily added in the settings. After
  doing so music can be played with built in player.

- Photos: Image viewer also works at a glance, even editing is available.
  However the Gallery application works quite slow on limited hardware, while
  e.g., browsing is surprisingly fast.

- Development: In Chromium OS developer mode (which is the default in case of
  CloudReady), e.g., Ubuntu can be easily installed and `chroot`-ed by using
  [crouton](https://github.com/dnschneid/crouton), even with the whole Unity
  Graphical Interface.


# Download

Download NeverWare's CloudReady [image](https://www.neverware.com/freedownload)
and Unzip the file to have `chromiumos_image.bin`.


# Prepare

Plug a >=8GB USB flash disk and determine which device it became:
`sudo fdisk -l`

For Windows and Mac there is a Chrome extension: [Chromebook Recovery
Utility](https://chrome.google.com/webstore/detail/chromebook-recovery-utili/jndclpdbaamdhonoechobihbbiimdgai?hl=en)
with which the image can be written to flash drive.

For Linux `dd` is there to do it so. Write the file directly to device (change
`/dev/sdX` to the identified device id)

`dd if=chromiumos_image.bin of=/dev/sdX bs=4M`

Unmount and remove the flash drive.


# Install

Boot from the flash drive on the target machine, and try as Guest to see how
it's doing. Install if it seems fine.

Two boot options are available:

- Standalone: the whole disk will be erased and repartitioned.

- Dual boot with Windows: keeps the windows install and occupies the residual
  space.
