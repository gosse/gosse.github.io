---
boldgrid_hide_page_title:
- '1'
url: "/?p=76"
type: "post"
author: nexthopself_qo5fng
title: VLT Configuration (Dell OS10)
bgseo_robots_index:
- index
bgseo_robots_follow:
- follow
siteorigin_page_settings:
- a:7:{s:6:"layout";s:7:"default";s:15:"display_top_bar";b:1;s:14:"display_header";b:1;s:13:"header_margin";b:1;s:10:"page_title";b:1;s:13:"footer_margin";b:1;s:22:"display_footer_widgets";b:1;}
boldgrid_in_page_containers:
- '1'
categories: "['networking']"
guid: https://www.next-hop-self.com/?p=76
id: 76
tags: "['dell', 'networking', 'os10']"
---

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">VLT is a multi-chassis LAG technology, like VPC, VSX, MC-LAG, MLAG, etc. It is not virtual stacking, each switch maintains an independent control plane.

Get your spanning-tree house in order. This is just an example.

</div></div></div></div>```
OS10(conf)# protocol spanning-tree rstp 
OS10(conf-rstp)# bridge-priority 4096 (primary peer) 
OS10(conf-rstp)# bridge-priority 8192 (secondary peer) 
OS10(conf-rstp)# no disable
```

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Set up your VLT Peer ports and port-channel.  
\*\*Important: Do not add any VLANs to the VLT Interconnect. The VLTi interface manages VLAN tagged/untagged traffic automatically between peers. Manually adding any VLAN configuration has been shown to disrupt traffic flow.\*\*

</div></div></div></div>```
OS10(conf)# interface range fortyGigE 0/56 , fortyGigE 0/60
OS10(conf-if-range-fo-0/56,fo-0/60)# no shutdown
OS10(conf-if-range-fo-0/56,fo-0/60)# interface port-channel 100
OS10(conf-if-po-100)# channel-member fortyGigE 0/56,60
OS10(conf-if-po-100)# no shutdown
```

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Configure VLT Domain. Use a unique domain ID for each pair in your enviornment.

</div></div></div></div>```
OS10(conf)# vlt domain 1
OS10(conf-vlt-domain)# primary-priority 10 (primary peer)
OS10(conf-vlt-domain)# primary-priority 20 (secondary peer)
OS10(conf-vlt-domain)# peer-link port-channel 100
OS10(conf-vlt-domain)# back-up destination (primary peer)
OS10(conf-vlt-domain)# back-up destination (secondary peer)
```

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Configure a port-channel to uplink to another switch or switches

</div></div></div></div>```
OS10(conf)#interface Port-channel 2 
OS10(conf-if-po-2)#switchport 
OS10(conf-if-po-2)#vlt-peer-lag port-channel 2 
OS10(conf-if-po-2)#no shutdown 
OS10(conf-if)#interface tengigabitethernet 0/40 
OS10(conf-if)#port-channel 2 mode active 
OS10(conf-if)#no shutdown
```