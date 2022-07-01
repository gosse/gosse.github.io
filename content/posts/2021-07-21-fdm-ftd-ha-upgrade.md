---
url: "/2021/07/21/fdm-ftd-ha-upgrade/"
type: "post"
author: nexthopself_qo5fng
title: FDM FTD HA Upgrade
categories: "['networking']"
guid: https://www.next-hop-self.com/?p=62
id: 62
tags: "['cisco', 'fdm', 'ftd', 'networking', 'security']"
---

To upgrade an HA pair of Firepower firewalls managed by FDM, use the following procedure:

1\. Deploy any pending changes  
2\. Log into standby unit, upload the upgrade and install   
3\. After the reboot and copmleted upgrade, on teh standby unit, go High Availability and **Switch Mode**. This will force failover.   
4\. Log into new standby unit, upgrade that unit.   
5\. Resume HA (if it does not automatically resume)   
6\. Deploy policy if needed