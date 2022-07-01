---
url: "/2021/07/21/restart-fdm-webui/"
type: "post"
author: nexthopself_qo5fng
title: Restart FDM WebUI
categories: "['networking']"
guid: https://www.next-hop-self.com/?p=60
id: 60
tags: "['cisco', 'fdm', 'ftd', 'networking', 'security']"
---

Sometimes, the FDM Web UI stops responding. I’ve found this especially on 6.5 code. To restart tomcat, go into expert mode and disable/enable:

```
> expert<br></br>admin@fp:~$ sudo su -<br></br>Password: <br></br>root@fp:~# cd /ngfw/var/cisco/ngfwWebUi/<br></br>## delete this file if it exists<br></br>root@fp:ngfwWebUi# rm .bootstrap-failed <br></br>root@fp:ngfwWebUi# pmtool disablebyid tomcat<br></br>root@fp:ngfwWebUi# pmtool enablebyid tomcat
```

The web service takes a while (10+ minutes) to come back online.

h/t to Pieter-Jan Nefkens at [https://www.nefkens.net/fdm-application-fails-after-upgrade/ ](https://www.nefkens.net/fdm-application-fails-after-upgrade/)