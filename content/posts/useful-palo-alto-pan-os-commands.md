---
author: Gary Ossewaarde
title: Useful Palo Alto PAN-OS Commands
categories: ['networking']
tags: ['networking', 'palo alto', 'panos', 'security', 'strata']
date: 2022-07-13T21:45:28-04:00
---

Here are some commands I continually find myself searcing for, all in one place. 

<!--more-->

Fix terminal height/width

```
set cli terminal height 500
set cli terminal width 500
```

Update Content/Threats from CLI (update license first)


```
request license fetch 
request content upgrade check
request content upgrade download latest
request content upgrade install version latest
```

Update Anti-Virus (AV) from CLI

```
request anti-virus upgrade check 
request anti-virus upgrade download latest 
request anti-virus upgrade install version latest
```

Update PAN-OS Version from CLI

```
request system software info
request system software download version 9.0.3-h3
request system software install version 9.0.3-h3
request restart system
```

Set management port to DHCP

```
configure
set deviceconfig system type dhcp-client accept-dhcp-domain yes accept-dhcp-hostname yes send-client-id yes send-hostname yes
commit


```

Newer models – disable ZTP

```
request disable-ztp
```

Configure Panorama server and delete default config 

```
set deviceconfig system panorama-server `IP/Hostname`

delete network virtual-wire default-vwire
delete rulebase security rules rule1
delete zone trust
delete zone untrust
delete network interface ethernet ethernet1/1
delete network interface ethernet ethernet1/2
delete network interface ethernet ethernet1/3
delete network interface ethernet ethernet1/4
delete network interface ethernet ethernet1/5
delete network interface ethernet ethernet1/6
delete network interface ethernet ethernet1/7
delete network interface ethernet ethernet1/8
```
