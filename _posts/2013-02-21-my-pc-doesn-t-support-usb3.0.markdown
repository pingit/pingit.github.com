---
layout: post
title: "my PC doesn't support USB3.0"
published: true
---

so my PC doesn't support USB3


    ping@640g-laptop:~$ sudo lsusb -v | grep -i bcdusb
      bcdUSB               2.00
      bcdUSB               2.00
      bcdUSB               2.00
      bcdUSB               2.00
      bcdUSB               2.00
      bcdUSB               2.00
      bcdUSB               2.00
      bcdUSB               2.00
      bcdUSB               2.00
      bcdUSB               2.00
      bcdUSB               2.10
      bcdUSB               1.10

    ping@640g-laptop:~$ lspci | grep -i USB
    00:1a.0 USB controller: Intel Corporation 5 Series/3400 Series Chipset USB2 Enhanced Host Controller (rev 06)
    00:1d.0 USB controller: Intel Corporation 5 Series/3400 Series Chipset USB2 Enhanced Host Controller (rev 06)
