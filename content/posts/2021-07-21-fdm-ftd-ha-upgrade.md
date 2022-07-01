---
author: Gary Ossewaarde
title: FDM FTD HA Upgrade
categories: ['networking']
tags: ['cisco', 'fdm', 'ftd', 'networking', 'security']
date: 2021-07-21T21:45:28-04:00
---

To upgrade an HA pair of Firepower firewalls managed by FDM, use the following procedure:

1\. Deploy any pending changes  
2\. Log into standby unit, upload the upgrade and install   
3\. After the reboot and copmleted upgrade, on teh standby unit, go High Availability and **Switch Mode**. This will force failover.   
4\. Log into new standby unit, upgrade that unit.   
5\. Resume HA (if it does not automatically resume)   
6\. Deploy policy if needed