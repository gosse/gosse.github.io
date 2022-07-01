---
author: Gary Ossewaarde
title: Useful Palo Alto PAN-OS Commands
categories: ['networking']
tags: ['networking', 'palo alto', 'panos', 'security', 'strata']
date: 2021-07-21T21:45:28-04:00
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