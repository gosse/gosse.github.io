---
author: Gary Ossewaarde
title: Dell S-Series OS10 Image Upgrade
categories: ['networking']
tags: ['dell', 'networking', 'os10']
date: 2021-07-21T21:45:28-04:00
---

I keep having to look this up, so I’m just going to write it here quick.

1\. Download firmware (finally on dell.com)  
2\. Copy to usb stick or tftp server  
3\. Do the upgrade.

```
OS10# copy run start
(if tftp) OS10# image download tftp://filename.bin
OS10# image install usb://filename.bin

OS10# show image status
Image Upgrade State: install
File Transfer State: idle
State Detail: No download information available
Task Start: 0000-00-00T00:00:00Z
Task End: 0000-00-00T00:00:00Z
Transfer Progress: 0 %
Transfer Bytes: 0 bytes
File Size: 0 bytes
Transfer Rate: 0 kbps

Installation State: install
--------------------------------------------------
State Detail: In progress: Configuring root filesystem
Task Start: 2018-11-06T21:55:30Z
Task End: 0000-00-00T00:00:00Z
OS10# show image status
Image Upgrade State: install
==================================================
File Transfer State: idle
--------------------------------------------------
State Detail: No download information available
Task Start: 0000-00-00T00:00:00Z
Task End: 0000-00-00T00:00:00Z
Transfer Progress: 0 %
Transfer Bytes: 0 bytes
File Size: 0 bytes
Transfer Rate: 0 kbps

Installation State: install
--------------------------------------------------
State Detail: In progress: Installing OS10 packages
Task Start: 2018-11-06T21:55:30Z
Task End: 0000-00-00T00:00:00Z

OS10# show boot
Current system image information:
===================================
Type Boot Type Active Standby Next-Boot
-----------------------------------------------------------------------------------
Node-id 1 Flash Boot [A] 10.4.0E(R3) [B] 10.4.1.2 [A] active 
OS10# boot system standby
OS10# show boot
Current system image information:
===================================
Type Boot Type Active Standby Next-Boot
-----------------------------------------------------------------------------------
Node-id 1 Flash Boot [A] 10.4.0E(R3) [B] 10.4.1.2 [B] standby 
OS10# copy run start
OS10# reload

Proceed to reboot the system? [confirm yes/no]:yes
```

The system reboots.

```
show ver
OS10# show version
Dell EMC Networking OS10-Enterprise
Copyright (c) 1999-2018 by Dell Inc. All Rights Reserved.
OS Version: 10.4.1.2
Build Version: 10.4.1.2.524
Build Time: 2018-09-26T17:20:01-0700
System Type: S4112F-ON
Architecture: x86_64
Up Time: 00:02:40
```