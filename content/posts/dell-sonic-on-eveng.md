---
author: Gary Ossewaarde
title: Dell Enterprise SONiC in Eve-NG
categories: ['networking']
tags: ['networking', 'eve-ng', 'sonic']
date: 2024-03-26T09:45:28-04:00
---

Import Dell Enterprise SONiC into Eve-NG for labbing. 

<!--more-->

1. [Download Dell Enterprise SONiC](https://www.dell.com/support/home/en-ie/product-support/product/enterprise-sonic-distribution/drivers) (requires login) and put in proper directory. Download the GNS3 image for use in Eve-NG. 
```
mkdir /opt/unetlab/addons/qemu/SONiC-4.2.0
unzip Enterprise_SONiC_OS_4.2.0_gns3.zip
mv Enterprise_SONiC_OS_4.2.0.img /opt/unetlab/addons/qemu/SONiC-4.2.0/virtioa.qcow2
```

2. Download template from [Dell Networking Github](https://github.com/Dell-Networking/PoC-DES-EVE-NG)
3. Place in proper directories:
```
cp SONiC.yml /opt/unetlab/html/templates/amd/
cp SONiC.yml /opt/unetlab/html/templates/intel/
```
4. Fix permissions! 
```
/opt/unetlab/wrappers/unl_wrapper -a fixpermissions
```

5. Build a SONiC Lab. The default login for this image is admin/YourPaSsWoRd