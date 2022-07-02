---
author: Gary Ossewaarde
title: VLT Configuration (Dell OS10)
categories: ['networking']
tags: ['dell', 'networking', 'os10']
draft: false
date: 2021-07-21T21:45:28-04:00
---

VLT is a multi-chassis LAG technology, like VPC, VSX, MC-LAG, MLAG, etc. It is not virtual stacking, each switch maintains an independent control plane.

Get your spanning-tree house in order. This is just an example.

```
OS10(conf)# protocol spanning-tree rstp 
OS10(conf-rstp)# bridge-priority 4096 (primary peer) 
OS10(conf-rstp)# bridge-priority 8192 (secondary peer) 
OS10(conf-rstp)# no disable
```

Set up your VLT Peer ports and port-channel.  
\*\*Important: Do not add any VLANs to the VLT Interconnect. The VLTi interface manages VLAN tagged/untagged traffic automatically between peers. Manually adding any VLAN configuration has been shown to disrupt traffic flow.\*\*

```
OS10(conf)# interface range fortyGigE 0/56 , fortyGigE 0/60
OS10(conf-if-range-fo-0/56,fo-0/60)# no shutdown
OS10(conf-if-range-fo-0/56,fo-0/60)# interface port-channel 100
OS10(conf-if-po-100)# channel-member fortyGigE 0/56,60
OS10(conf-if-po-100)# no shutdown
```

Configure VLT Domain. Use a unique domain ID for each pair in your enviornment.

```
OS10(conf)# vlt domain 1
OS10(conf-vlt-domain)# primary-priority 10 (primary peer)
OS10(conf-vlt-domain)# primary-priority 20 (secondary peer)
OS10(conf-vlt-domain)# peer-link port-channel 100
OS10(conf-vlt-domain)# back-up destination (primary peer)
OS10(conf-vlt-domain)# back-up destination (secondary peer)
```

Configure a port-channel to uplink to another switch or switches

```
OS10(conf)#interface Port-channel 2 
OS10(conf-if-po-2)#switchport 
OS10(conf-if-po-2)#vlt-peer-lag port-channel 2 
OS10(conf-if-po-2)#no shutdown 
OS10(conf-if)#interface tengigabitethernet 0/40 
OS10(conf-if)#port-channel 2 mode active 
OS10(conf-if)#no shutdown
```