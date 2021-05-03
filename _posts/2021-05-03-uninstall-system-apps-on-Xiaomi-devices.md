---
title: 'How to uninstall system apps on Xiaomi devices (NO ROOT)'
layout: post
description: 'This is a small guide on how to debloat your Xiaomi devices' 
---


This is a small guide on how to debloat(uninstall system apps) your Xiaomi devices without root privilege.

 - First you need to install [ADB Drivers](https://forum.xda-developers.com/t/official-tool-windows-adb-fastboot-and-drivers-15-seconds-adb-installer-v1-4-3.2588979/) and [Java SDK](https://www.oracle.com/java/technologies/javase-jdk14-downloads.html) on your PC.
 - Open your phone settings and enter the My Device menu. Now press 7 times in the "MIUI Version" field until the message "You are a developer now" appears.
 - Return to the Settings menu and go to Advanced Settings menu. Select "Developer Options". From here you have to activate the **USB Debugging** option by confirming the requested permission. Also turn on "Install via USB" and "Debugging via USB (security settings)".
 - Connect your smartphone to the PC via a USB cable (suitable for data transfer) and open the "Command Prompt" / "Power Shell window", and type "adb devices" a pop-up window should appear on your smartphone asking you to consent to USB debugging. Typing this will show up your dedvice name, now close the cmd.
 - Now download and open [Xiaomi ADB / Fastboot Tool](https://github.com/Szaki/XiaomiADBFastbootTools/releases/download/7.0/XiaomiADBFastbootTools.jar) The software will automatically recognize your smartphone, showing you a list of applications that need to be removed. List of boatwares you can remove such as following ~
 
*Analytics, App Store, Backup, Browser, Facebook, Games, Google Duo, Google Play Movies, Google Play Music, Mi App Store, Mi Credit, Mi Drop, Mi Pay, Mi Recycle, Miui Daemon, MiWebView, MSA, PAI, PartnerBookmarks, SMS Extra, Translation Service, Uniplay Service, VsimCore.*

Once you have selected the system applications that you want to remove from your Xiaomi smartphone, simply click the “Uninstall!” Button. In a matter of minutes we you have eliminated all the pre-installed apps on our Xiaomi that you do not use.
