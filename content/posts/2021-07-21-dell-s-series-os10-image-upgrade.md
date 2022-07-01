---
type: "post"
author: Gary Ossewaarde
title: Dell S-Series OS10 Image Upgrade
categories: ['networking']
tags: ['dell', 'networking', 'os10']
---

I keep having to look this up, so I’m just going to write it here quick.

1\. Download firmware (finally on dell.com)  
2\. Copy to usb stick or tftp server  
3\. Do the upgrade.

```
OS10# copy run start<br></br>(if tftp) OS10# image download tftp://filename.bin<br></br>OS10# image install usb://filename.bin<br></br><br></br>OS10# show image status<br></br>Image Upgrade State: install<br></br>File Transfer State: idle<br></br>State Detail: No download information available<br></br>Task Start: 0000-00-00T00:00:00Z<br></br>Task End: 0000-00-00T00:00:00Z<br></br>Transfer Progress: 0 %<br></br>Transfer Bytes: 0 bytes<br></br>File Size: 0 bytes<br></br>Transfer Rate: 0 kbps<br></br><br></br>Installation State: install<br></br>--------------------------------------------------<br></br>State Detail: In progress: Configuring root filesystem<br></br>Task Start: 2018-11-06T21:55:30Z<br></br>Task End: 0000-00-00T00:00:00Z<br></br>OS10# show image status<br></br>Image Upgrade State: install<br></br>==================================================<br></br>File Transfer State: idle<br></br>--------------------------------------------------<br></br>State Detail: No download information available<br></br>Task Start: 0000-00-00T00:00:00Z<br></br>Task End: 0000-00-00T00:00:00Z<br></br>Transfer Progress: 0 %<br></br>Transfer Bytes: 0 bytes<br></br>File Size: 0 bytes<br></br>Transfer Rate: 0 kbps<br></br><br></br>Installation State: install<br></br>--------------------------------------------------<br></br>State Detail: In progress: Installing OS10 packages<br></br>Task Start: 2018-11-06T21:55:30Z<br></br>Task End: 0000-00-00T00:00:00Z<br></br><br></br>OS10# show boot<br></br>Current system image information:<br></br>===================================<br></br>Type Boot Type Active Standby Next-Boot<br></br>-----------------------------------------------------------------------------------<br></br>Node-id 1 Flash Boot [A] 10.4.0E(R3) [B] 10.4.1.2 [A] active <br></br>OS10# boot system standby<br></br>OS10# show boot<br></br>Current system image information:<br></br>===================================<br></br>Type Boot Type Active Standby Next-Boot<br></br>-----------------------------------------------------------------------------------<br></br>Node-id 1 Flash Boot [A] 10.4.0E(R3) [B] 10.4.1.2 [B] standby <br></br>OS10# copy run start<br></br>OS10# reload<br></br><br></br>Proceed to reboot the system? [confirm yes/no]:yes
```

The system reboots.

```
show ver<br></br>OS10# show version<br></br>Dell EMC Networking OS10-Enterprise<br></br>Copyright (c) 1999-2018 by Dell Inc. All Rights Reserved.<br></br>OS Version: 10.4.1.2<br></br>Build Version: 10.4.1.2.524<br></br>Build Time: 2018-09-26T17:20:01-0700<br></br>System Type: S4112F-ON<br></br>Architecture: x86_64<br></br>Up Time: 00:02:40
```