---
layout: post
title: "How to install Solaris 11 from a USB drive on a SPARC server"
---

Requirements:

- USB flash drive
- 9-pin serial to RJ45 null modem cable
- Linux computer capable of running VirtualBox

The steps:

1.  Connect the server and the Linux computer together using the null
    modem cable.

2.  On the Linux computer, install a terminal emulation program like
    `minicom`.

3.  Set serial port parameters to communicate using 9600 baud, 8 bit,
    no parity, 1 stop bit. For `minicom`, this can be done by creating
    `/etc/minicom/minirc.dfl` with the following contents:

        pu port             /dev/ttyS0
        pu baudrate         9600
        pu bits             8
        pu parity           N
        pu stopbits         1

4.  Add the user that will be running `minicom` to the `dialout` group.
    The command to do that on Ubuntu is `sudo adduser $USER dialout`.

5.  Use the Linux host to download the latest x86 version of Solaris and
    install it on a new VirtualBox VM.

6.  Download the `SPARC USB Text Installer` version of Solaris
    (`sol-11_3-text-sparc.usb`) and save it to a local file on that
    running x86 Solaris VM.

7.  Insert the USB flash drive and connect it to the VM.

8.  From under the root user on the Solaris VM, run
    `usbcopy sol-11_3-text-sparc.usb` and select the USB drive as the
    destination. Wait until the image is written, eject the drive and
    insert it into a front USB port of the SPARC server.

9.  Launch `minicom`.

10. Power up the SPARC machine. Wait for the ILOM boot sequence to
    complete. Log in to ILOM. The default ILOM login/password are
    `root`/`changeme`.

11. Run `start /SYS` to turn the server on and then `start /SP/console`
    to start the serial console. Wait for the initialization sequence
    and tests to complete.

12. At the OpenBoot prompt (`ok`), enter `show-disks`. Choose the disk
    that corresponds to the USB drive (the one that looks something like
    `/pci@400/pci@0/pci@1/pci@0/usb@0,2/hub@4/storage@1/disk`).

13. Boot from the USB by executing `boot USBDISK` (e.g.
    `boot /pci@400/pci@0/pci@1/pci@0/usb@0,2/hub@4/storage@1/disk`).

14. When the Oracle Solaris installation menu appears on the screen,
    switch terminal type to VT100 - `minicom` supports that.

15. Proceed with installation: follow the menus to make configuration
    choices and wait for the system to be installed.

This completes the guide. Peace.

P.S. If you're using DHCP, one of the first things to do on a new system is to
install avahi so that the host becomes discoverable by its name:

    # pkg install avahi
    # svcadm enable multicast
    # svcadm enable avahi-bridge-dsd
    # svcadm restart multicast
    # svcadm restart avahi-bridge-dsd
