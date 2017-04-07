FullPageOS
==========

A `Raspberry Pi <http://www.raspberrypi.org/>`_ distribution to display one webpage in full screen. It includes `Chromium <https://www.chromium.org/>`_ out of the box and the scripts necessary to load it at boot.
This repository contains the source script to generate the distribution out of an existing `Raspbian <http://www.raspbian.org/>`_ distro image.

This is a fork from https://github.com/guysoft/FullPageOS, branded and adapted for the DISE Xpress Digital Signage software.

The current image used is 0.7.0RC1 Nightly, adapted post-hoc. Raw build available here: http://docstech.net/FullPageOS/nightly/2016-12-27_2016-11-25-fullpageos-jessie-lite-0.7.0_RC1.zip.
The current image used needs to be adapted for each screen, as it uses a static ethernet configuration set in /boot/fullpageos-network.

In order to run DISE Xpress until or unless a URL-launcher is added, the start-up should be changed to launch
`go.dise.tv` with the request parameter `serial=$SERIAL` and `platform=raspbian`. For reference, see the previous
script used, located at `xpress/install-xpress`.

Dependencies are:
- Node.js
- Unclutter
- Scrot

In addition, the OS needs to be updated (`apt-get update && apt-get upgrade`), and `raspi-config` needs to be updated as well.

In order to install nodejs, see the `install-xpress`-script for reference.

Note that all the commands used there must
be executed with admin/root privileges. There might be issues with piping the curl result to bash - In that case,
change it to `curl -sL https://..... | sh`.

Where to get it?
----------------

Official mirror is `here <http://docstech.net/FullPageOS/>`_

Nightly builds are available `here <http://docstech.net/FullPageOS/nightly/>`_ (currently built on demand)

How to use it?
--------------

#. Unzip the image and install it to an SD card `like any other Raspberry Pi image <https://www.raspberrypi.org/documentation/installation/installing-images/README.md>`_
#. Configure your WiFi by editing ``fullpageos-network.txt`` on the root of the flashed card when using it like a flash drive
#. Boot the Pi from the SD card
#. Log into your Pi via SSH (it is located at ``fullpageos.local`` `if your computer supports bonjour <https://learn.adafruit.com/bonjour-zeroconf-networking-for-windows-and-linux/overview>`_ or the IP address assigned by your router), default username is "pi", default password is "raspberry", change the password using the ``passwd`` command and expand the filesystem of the SD card through the corresponding option when running ``sudo raspi-config``.

Requirements
------------
* Raspberrypi 2 and newser or device running Armbian, Older Rasperry Pis are not currently supported.  See `Raspberry Pi <https://github.com/guysoft/FullPageOS/issues/12>`_ and `Raspberry Pi <https://github.com/guysoft/FullPageOS/issues/43>`_.
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
