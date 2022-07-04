---
author: Gary Ossewaarde
title: Automating Certificate Install on ASA with Netmiko
categories: ['networking']
tags: ['asa', 'automation', 'cisco', 'networking', 'security']
date: 2021-07-21T21:45:28-04:00
---

Here is a little script I wrote to automate putting certificates onto ASAs. It also activates the cert on the inside interface (mine is a one-armed VPN concentrator). The cert is assumed to already in the correct format and named asabase64.cert.

<!--more-->

```no-highlight
#!/usr/bin/env python3

from netmiko import Netmiko
import base64
import datetime

# connection config for netmiko 
asav = {
"host": "hostname",
"username": "user",
"password": "pass",
"device_type": "cisco_asa",
"secret": "enable secret"
}

# open and read the cert 
with open("asabase64.cert", "r") as f: 
cert = f.read().splitlines()

# name the cert with today's date 
# this helps when pushing a new cert 
# to not have a namespace overlap 
today = datetime.datetime.now().strftime("%d-%m-%Y")
certname = today + "starcert"
certcmd = "crypto ca import " + certname + " pkcs12 asaexportpass"
# build the commands needed to install the cert
commands = []
commands.append("conf t")
commands.append(certcmd)
commands = commands + cert
commands.append("")
commands.append("quit")
trustpoint = "ssl trust-point " + certname + " inside"
commands.append(trustpoint)

# connect to the firewall 
net_connect = Netmiko(**asav)
print(net_connect.find_prompt())

# always a good test 
output = net_connect.send_command_timing("show int ip br")

# send the commands
for command in commands:
print(command)
net_connect.send_command_timing(command)


# clean up 
net_connect.disconnect()
```
