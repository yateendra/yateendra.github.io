---
title: 'Scrcpy How to Mirror Your Android Device Screen to PC'
layout: post
description: 'scrcpy is the best application that allows you to mirror your phone screen to PC via USB for free.'
---

[Scrcpy](https://github.com/Genymobile/scrcpy) is the best application that allows you to mirror your phone screen to PC via USB for free. It works on all desktop operating systems including Windows, macOS and Linux. You don't need to download or install any app on your android phone.
![SCRPY ](https://i.ytimg.com/vi/HAJaNuFIvnU/hqdefault.jpg)
Here's what you need to get started:

- Download [scrcpy](https://github.com/Genymobile/scrcpy) for the operating system you are using. Follow the installation instructions in the ReadMe file at the bottom of this page.
- USB cable for connecting your phone to a computer.
- An Android smartphone or tablet with USB debugging enabled.

### How to Enable USB Debugging Mode on Android
-   Go to **Settings -> System -> About phone** ( **Settings -> About phone** in older versions of Android).
-   Scroll down and tap **Build Number** seven times until you see a pop-up window telling you that you are in developer mode.
-   Refer to **Settings -> System** and enter the list **Developer Options** New.
-   Scroll down and select **Enable USB Debugging** .
-   Confirm the action when prompted.

On custom versions of Android, the first step may be slightly different. But in general, you need to find the page with the current build information and tap on the number a few times to enable developer options.
### How to output Android screen to PC or Mac via USB
Now that the USB debugging mode is enabled, the rest is simple:
1.  Download **the scrcpy** .zip file for Windows 10 64-bit.
[**https://github.com/Genymobile/scrcpy/releases**](https://github.com/Genymobile/scrcpy/releases)
2.  **Extract all** contents of the zip file.
3.  **Connect your Android device** to your Windows PC via USB (or via TCP/IP).
4.  Install  [ADB Drivers](https://forum.xda-developers.com/t/official-tool-windows-adb-fastboot-and-drivers-15-seconds-adb-installer-v1-4-3.2588979/) on your PC.
5.  Run **scrcpy.exe** .
6.  At this point, you can view the display and operate the device.


### How to Mirror Android Screen to PC Wirelessly
 - Connect the mobile and PC to same Wi-Fi.
 - Find the mobile IP address at **Settings  -> About phone-> Status**.
 - Open CMD in current SCRCPY folder.
 - type `adb tcpip 5555` 
 - and `scrcpy --tcpip = DEVICE_IP:5555` (replace DEVICE_IP with your android IP address).
 - You can view the display at this point, now unplug the device.

Enjoy SCRCPY!
