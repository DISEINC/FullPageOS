FullPageOS
==========

A `Raspberry Pi <http://www.raspberrypi.org/>`_ distribution to display one webpage in full screen. It includes `Chromium <https://www.chromium.org/>`_ out of the box and the scripts necessary to load it at boot.
This repository contains the source script to generate the distribution out of an existing `Raspbian <http://www.raspbian.org/>`_ distro image.

This is a fork from https://github.com/guysoft/FullPageOS, branded and adapted for the DISE Xpress Digital Signage software.

The current image used is 0.6.0, adapted post-hoc. Raw build available here:
http://docstech.net/FullPageOS/2016-05-10-fullpageos-jessie-lite-0.6.0.zip.

# Creating A Disk Image

Straight-up using Win32DiskImager to create an image will cause the whole device to be copied,
regardless of how much of it is actually allocated. This process requires linux and `dd`, as
Bash on Ubuntu on Windows can't access disk drives.

You'll need:
* A computer or VM running linux
* An SD-card with your OS (and un-expanded FS) and a reader
* A USB storage device

This guide will assume that your OS is /dev/sda, the Pi is /dev/sdb and that the storage device is /dev/sdc.
It will also assume that the storage device has a partition /dev/sdc1. Adjust accordingly if your setup differs.

1. Fire up the machine and insert the SD card and USB device.
2. Run `sudo fdisk -l` to list disks and their partitions.
3. Make note of where allocated space ends on the Pi, e.g.
```
Device    Boot    Start   End     Sectors   Size  Id  Type
/dev/sdb1         8192    137215  129024    63M   c   W95 FAT32 (LBA)
/dev/sdb2         137216  4331519 4194304   2G    83  Linux
```

In this case `fdisk -l` tells us that allocated space ends at `4331519`.

4. Mount the storage device partition with `sudo mount /dev/sdc1 /mnt`
5. Write the image with `dd if=/dev/sdb of=/mnt/$IMAGE.img count=$END`
Where $IMAGE is the name of the resulting file, and $END the number we took note of earlier.
6. Unmount the storage device with `sudo umount /dev/sdc1`


# Original documentation

Where to get it?
----------------

Official mirror is `here <http://docstech.net/FullPageOS/>`_

Nightly builds are available `here <http://docstech.net/FullPageOS/nightly/>`_ (currently built on demand)

How to use it?
--------------

1. Unzip the image and install it to an SD card  [like any other Raspberry Pi image](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)
2. Configure your WiFi by editing `fullpageos-network.txt` on the root of the flashed card when using it like a flash drive
3. Boot the Pi from the SD card
4. Log into your Pi via SSH (it is located at `fullpageos.local` [if your computer supports bonjour](https://learn.adafruit.com/bonjour-zeroconf-networking-for-windows-and-linux/overview) or the IP address assigned by your router). Default username is "pi", default password is "raspberry", change the password using the `passwd` command and expand the filesystem of the SD card through the corresponding option when running `sudo raspi-config`.

Requirements
------------
* Raspberrypi 2 and newser or device running Armbian, Older Rasperry Pis are not currently supported.  See https://github.com/guysoft/FullPageOS/issues/12 and
https://github.com/guysoft/FullPageOS/issues/43.
* 2A power supply


Features
--------

* Loads Chromium at boot in full screen
* Webpage can be changed from /boot/fullpageos.txt
* Ships with preconfigured `X11VNC <http://www.karlrunge.com/x11vnc/>`_, for remote connection (password 'raspberry')

Developing
----------

Requirements
~~~~~~~~~~~~

#. `qemu-arm-static <http://packages.debian.org/sid/qemu-user-static>`_
#. Downloaded `Raspbian <http://www.raspbian.org/>`_ image.
#. root privileges for chroot
#. Bash
#. realpath
#. sudo (the script itself calls it, running as root without sudo won't work)

Build FullPageOS From within FullPageOS / Raspbian / Debian / Ubuntu
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

FullPageOS can be built from Debian, Ubuntu, Raspbian, or even FullPageOS.
Build requires about 2.5 GB of free space available.
You can build it by issuing the following commands::

    sudo apt-get install realpath qemu-user-static

    git clone https://github.com/guysoft/FullPageOS.git
    cd FullPageOS/src/image
    curl -J -O -L  http://downloads.raspberrypi.org/raspbian_latest
    cd ..
    sudo modprobe loop
    sudo bash -x ./build

Building FullPageOS Variants
~~~~~~~~~~~~~~~~~~~~~~~~

FullPageOS supports building variants, which are builds with changes from the main release build. An example and other variants are available in the folder ``src/variants/example``.

To build a variant use::

    sudo bash -x ./build [Variant]

Building Using Vagrant
~~~~~~~~~~~~~~~~~~~~~~
There is a vagrant machine configuration to let build FullPageOS in case your build environment behaves differently. Unless you do extra configuration, vagrant must run as root to have nfs folder sync working.

To use it::

    sudo apt-get install vagrant nfs-kernel-server
    sudo vagrant plugin install vagrant-nfs_guest
    sudo modprobe nfs
    cd FullPageOS/src/vagrant
    sudo vagrant up

After provisioning the machine, its also possible to run a nightly build which updates from devel using::

    cd FullPageOS/src/vagrant
    run_vagrant_build.sh

To build a variant on the machine simply run::

    cd FullPageOS/src/vagrant
    run_vagrant_build.sh [Variant]

Usage
~~~~~

#. If needed, override existing config settings by creating a new file ``src/config.local``. You can override all settings found in ``src/config``. If you need to override the path to the Raspbian image to use for building OctoPi, override the path to be used in ``ZIP_IMG``. By default, the most recent file matching ``*-raspbian.zip`` found in ``src/image`` will be used.
#. Run ``src/build`` as root.
#. The final image will be created in ``src/workspace``

Code contribution would be appreciated!
