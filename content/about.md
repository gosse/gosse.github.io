---
title: "About"
date: 2022-06-30T21:42:17-04:00
draft: false
---

## About Me

I am network engineer with broad technical interests. I currently hold Cisco, Palo Alto, Aruba, and Juniper certifications. For an up-to-date list, see my LinkedIn.

## Site Title

The name next-hop-self comes from the setting in BGP. RFC 4271 defines next-hop as, “The NEXT_HOP is a well-known mandatory attribute that defines the IP address of the router that SHOULD be used as the next hop to the destinations listed in the UPDATE message.”

In iBGP, the next-hop advertised to iBGP peers is not changed (by default). If the peers do not have the next hop that is in the advertisement in their RIB, they will not install the routes into their table. One way to fix this is to have the advertising router advertise the next hop as its own interface.

