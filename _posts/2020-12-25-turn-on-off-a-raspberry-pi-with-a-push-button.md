---
title: Turn on/off a Raspberry Pi with a push button using gpio-shutdown
date: 2020-12-25 11:34:43 +0700
categories: [Electronics]
tags: [electronics, raspberry pi]
---

I have a Raspberry Pi 3 B+ running OctoPrint to control my 3D Printer and recently got a Raspberry Pi 4 Model B for my media center and while they work great, I hate the fact that I have to unplug/plug in the power supply (no power switch) every time I want to turn it on (usually I turn it off using the shutdown option in the OS).

After a little research I found that they support `gpio-shutdown` which requires you to short the physical pin 5 (BCM 3) to the ground next to it. This works well to power it on, but if you also want to shut down, you have to add `dtoverlay=gpio-shutdown` to `/boot/config.txt` and that's all it requires.

So I installed a [momentary push button](https://www.amazon.com/gp/product/B07Q81FXCQ/) to my 3D printer case for the Pi4.

![raspberry-pi-button](/assets/img/raspberry/RPI4-button.jpg)
![raspberry-pi-button-top](/assets/img/raspberry/RPI4-button-top.jpg)
![raspberry-pi-case](/assets/img/raspberry/RPI4-case.jpg)
