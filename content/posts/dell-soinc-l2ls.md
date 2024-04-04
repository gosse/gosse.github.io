---
author: Gary Ossewaarde
title: Dell Enterprise SONiC L2 Spine-Leaf Deployment
categories: ['networking']
tags: ['networking', 'eve-ng', 'sonic']
date: 2024-03-26T10:45:28-04:00
draft: false
---

### Introduction 

"Layer 2 Spine-Leaf" is a great architecture for most small data centers. If the data center only has a few racks of equipment, it's often unnecessary to build a more complex design. In its most basic deployment, all gateways are SVIs (VLAN interfaces) on the spines. Multi-tenancy can be achieved by using VRF-lite and putting a firewall in to stitch the VRFs together. 

This is part of a series of building data center fabrics with different network operating systems. 

- Part 1: Layer 2 Spine-Leaf
- Part 2: Layer 3 Spine-Leaf with VXLAN
- Part 3: Layer 3 Spine-Leaf with EVPN-VXLAN
- Part 3: Layer 3 Spine-Leaf with Dual Data Center

### Topology

![Layer 2 Spine Leaf](/images/L2-Spine-Leaf.drawio.png)

### Configuration 

#### Disable ZTP
```
sonic login: admin
Password: 
Last login: Wed Nov 29 17:34:36 UTC 2023 on ttyS0
Linux sonic 5.10.0-21-amd64 #1 SMP Debian 5.10.162-1 (2023-01-21) x86_64
You are on
  ____   ___  _   _ _  ____
 / ___| / _ \| \ | (_)/ ___|
 \___ \| | | |  \| | | |
  ___) | |_| | |\  | | |___
 |____/ \___/|_| \_|_|\____|

-- Software for Open Networking in the Cloud --

Unauthorized access and/or use are prohibited.
All access and/or use are subject to monitoring.

Help:    http://azure.github.io/SONiC/

admin@sonic:~$ sonic-cli                                                                           
Zero Touch Provisioning discovery in progress. Please disable ZTP or logout.
                                                                               
sonic# configure terminal 
sonic(config)# no ztp enable

Mar 26 13:49:39.660718+00:00 2024 sonic WARNING sonic-ztp[3150]: Received terminate signal. Shutting down.
Zero Touch Provisioning discovery in progress. Please disable ZTP or logout.                                                                               
Mar 26 13:49:40.665128+00:00 2024 sonic INFO sonic-ztp[3119]: Process pid 17315 returned with status 15.
```

At this point, the sonic systems restart and it takes a minute. Then, you'll need to get back into sonic-cli. 

#### Basic admin config
```
admin@sonic:~$ sonic-cli                                                                           
sonic# configure terminal 
sonic(config)# hostname SONiCx
Broadcast message: Hostname has been changed from 'sonic' to 'SONiC1'. Users running 'sonic-cli' are suggested to restart your session.
sonic(config)# lldp enable
```

#### Spine Configuration

Configure the MC-LAG and downstream port. 
```
!! Configure MCLAG Peer-Link and Keepalive
interface PortChannel1 mode active
 description "MCLAG PEER LINK"
 no shutdown
! 
interface Ethernet0
 description "MCLAG PEER LINK A"
 channel-group 1
 no shutdown
!
interface Ethernet1
 description "MCLAG PEER LINK B"
 channel-group 1
 no shutdown
!
interface Ethernet4
 description "MCLAG KEEPALIVE"
 no shutdown
 ip address 169.254.1.1/30
!
!! Configure MCLAG Domain
mclag domain 1
 source-ip 169.254.1.1
 peer-ip 169.254.1.2
 peer-link PortChannel1
 keepalive-interval 1
 session-timeout 30
 delay-restore 300
!! Configure link to Leaf
interface PortChannel3 mode active
 switchport trunk allowed Vlan 1-4094
 no shutdown
 mclag 1
!
interface Ethernet7
 description "LINK TO LEAF-03"
 channel-group 3
 no shutdown
```

Verify:
```
# show mclag brief
 
Domain ID            : 1
Role                 : standby
Session Status       : up
Peer Link Status     : up
Source Address       : 169.254.1.2
Peer Address         : 169.254.1.1
Session Vrf          : default
Peer Link            : PortChannel1
Keepalive Interval   : 1 secs
Session Timeout      : 30 secs
Delay Restore        : 300 secs
System Mac           : 50:00:00:01:00:10
Mclag System Mac     : 
```



#### Leaf Configuration

```
interface PortChannel1
 description "Uplink to Spine"
 switchport trunk allowed Vlan 1-4094
 no shutdown
!
interface Ethernet0
 channel-group 1
 no shutdown
!
interface Ethernet1
 channel-group 1
 no shutdown
!
interface Vlan 10
!
!! access ports for test devices
interface Ethernet2
 switchport access vlan 10
!
```


### Conclusion 
Building a basic MC-LAG layer 2 leaf-spine on SONiC is quite easy and a great solution for small DCs. 
