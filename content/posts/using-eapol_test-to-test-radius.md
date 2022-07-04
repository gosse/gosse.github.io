---
author: Gary Ossewaarde
title: Using eapol_test to test RADIUS
categories: ['networking']
tags: ['networking', 'radius']
date: 2021-07-21T21:45:28-04:00
---

The little program eapol\_test can come in handy when trying to test RADIUS configurations. eapol\_test is a part of [wpa\_supplicant](http://w1.fi/wpa_supplicant/).

<!--more-->

Good information on the program can be found in the manpage, however the usage of the -N flag for attribute/value pairs deserves a few more words (and an example to remind myself in the future how to use it. Here is an example that includes sending Called-Station-Id of be-ef-be-ef-be-ef:ent-secure to 10.10.10.10 with a shared key of ‘aruba123’.

```
./eapol_test -c wpasupplicantconfig.conf -N 30:s:be-ef-be-e9-be-ef:ent-secure -a 10.10.10.10 -s aruba123
```

According to the man page, -N is for ‘attr spec’:

Send arbitrary attribute specific by attr\_id:syntax:value, or attr\_id alone. attr\_id should be the numeric ID of the attribute, and syntax should be one of ‘s’ (string), ‘d’ (integer), or ‘x’ (octet string). The value is the attribute value to send. When attr\_id is given alone, NULL is used as the attribute value. Multiple attributes can be specified by using the option several times.

So, in my example, I’m sending attribute ID of 30 (IETF Called-Station-Id), a string, with the value of be-ef-be-e9-be-ef:ent-secure.
