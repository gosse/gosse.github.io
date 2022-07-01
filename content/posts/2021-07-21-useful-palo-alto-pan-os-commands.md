---
boldgrid_hide_page_title:
- '1'
url: "/2021/07/21/useful-palo-alto-pan-os-commands/"
type: "post"
author: nexthopself_qo5fng
title: Useful Palo Alto PAN-OS Commands
bgseo_robots_index:
- index
bgseo_robots_follow:
- follow
siteorigin_page_settings:
- a:7:{s:6:"layout";s:7:"default";s:15:"display_top_bar";b:1;s:14:"display_header";b:1;s:13:"header_margin";b:1;s:10:"page_title";b:1;s:13:"footer_margin";b:1;s:22:"display_footer_widgets";b:1;}
boldgrid_in_page_containers:
- '1'
categories: "['networking']"
guid: https://www.next-hop-self.com/?p=72
id: 72
tags: "['networking', 'palo alto', 'panos', 'security', 'strata']"
---

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Fix terminal height/width

</div></div></div></div>```
set cli terminal height 500
set cli terminal width 500
```

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Update Content/Threats from CLI (update license first)

</div></div></div></div>```
<pre class="">request license fetch 
request content upgrade check
request content upgrade download latest
request content upgrade install version latest
```

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Update Anti-Virus (AV) from CLI

</div></div></div></div>```
<pre class="">request anti-virus upgrade check 
request anti-virus upgrade download latest 
request anti-virus upgrade install version latest
```

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Update PAN-OS Version from CLI

</div></div></div></div>```
<pre class="">request system software info
request system software download version 9.0.3-h3
request system software install version 9.0.3-h3
request restart system
```

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Set management port to DHCP

</div></div></div></div>```
<pre class="">configure
set deviceconfig system type dhcp-client accept-dhcp-domain yes accept-dhcp-hostname yes send-client-id yes send-hostname yes
commit


```

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Newer models – disable ZTP

</div></div></div></div>```
<pre class="">request disable-ztp
```

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Configure Panorama server and delete default config </div></div></div></div>```
<pre class="">set deviceconfig system panorama-server `IP/Hostname`

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