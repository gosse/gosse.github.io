---
author: Gary Ossewaarde
title: Decrypt ISE Backup
categories: ['networking']
tags: ['networking', 'cisco', 'ise', 'security']
date: 2022-07-18T01:45:28-04:00
---

Decrypt PGP encrypted ISE backup. 

<!--more-->

```
gpg -v --batch --yes --passphrase myEncryptionKey -d somefile.tar.gpg > somefile.tar
```

[Source](https://community.cisco.com/t5/network-access-control/decrypt-backup-file-using-gpg-keychain/td-p/3450118)
