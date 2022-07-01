---
url: "/2021/07/21/administrative-distance/"
type: "post"
author: nexthopself_qo5fng
title: Administrative Distance
categories: "['networking']"
guid: https://www.next-hop-self.com/?p=50
id: 50
tags: "['networking']"
---

How do we choose which route to install into forwarding table from the routing table? If longest prefix matches are equal, Cisco routers use the Administrative Distance (AD) of a route to determine which one is “best”.

In general, a lower AD means the route can be more trusted. The default distances are listed below, but it’s easy to change the AD of a particular routing protocol on a router.

```
+-------------------------------+------+<br></br>| Route Source Default Distance |      |<br></br>+-------------------------------+------+<br></br>| Connected interface           |    0 |<br></br>| Static route                  |    1 |<br></br>| EIGRP summary route           |    5 |<br></br>| BGP                           |   20 |<br></br>| Internal EIGRP                |   90 |<br></br>| IGRP                          |  100 |<br></br>| OSPF                          |  110 |<br></br>| IS-IS                         |  115 |<br></br>| RIP                           |  120 |<br></br>| EGP                           |  140 |<br></br>| ODR                           |  160 |<br></br>| External EIGRP                |  170 |<br></br>| Internal BGP                  |  200 |<br></br>| Unknown                       |  255 |<br></br>+-------------------------------+------+
```

To change the AD of a protcol, use the distance command under most routing protocols. You can see distances in “show ip protocols”.

**EIGRP**

Change AD in EIGRP, first number is internal, second external.

```
router eigrp 10<br></br> distance eigrp 15 190 
```

If you need to change the AD of a EIGRP summary route,

```
int gi1<br></br> ip summary-address eigrp 10 192.168.0.0 255.255.255.0 95 <br></br> ! 10 = EIGRP AS <br></br> ! 192.168.0.0 255.255.255.0 = subnet <br></br> ! 95 = administrative distance 
```

**OSPF**

```
router ospf 1<br></br> distance 77
```

If you need to change the distance of a redistributed protocol, such as EIGRP:

```
router ospf 1<br></br> redistribute eigrp 1 subnets<br></br> distance ospf external 10
```

BGP

The BGP distance command uses three distances, the first external, second internal, third local:

```
router bgp 65101<br></br> distance bgp 25 26 27
```

Using administrative distance is a great way to execute a routing protocol changeover. You can set the new protocol to a higher AD, enable it on your links and check the topology tables, LSDB, and RIB as applicable and be confident your new protocol converges. Then, flip the AD and the new protocol will takeover.