---
type: "post"
author: Gary Ossewaarde
title: Automating Certificate Install on ASA with Netmiko
categories: ['networking']
tags: ['asa', 'automation', 'cisco', 'networking', 'security']
---

Here is a little script I wrote to automate putting certificates onto ASAs. It also activates the cert on the inside interface (mine is a one-armed VPN concentrator). The cert is assumed to already in the correct format and named asabase64.cert.

```
#!/usr/bin/env python3<br></br><br></br>from netmiko import Netmiko<br></br>import base64<br></br>import datetime<br></br><br></br># connection config for netmiko <br></br>asav = {<br></br>"host": "hostname",<br></br>"username": "user",<br></br>"password": "pass",<br></br>"device_type": "cisco_asa",<br></br>"secret": "enable secret"<br></br>}<br></br><br></br># open and read the cert <br></br>with open("asabase64.cert", "r") as f: <br></br>cert = f.read().splitlines()<br></br><br></br># name the cert with today's date <br></br># this helps when pushing a new cert <br></br># to not have a namespace overlap <br></br>today = datetime.datetime.now().strftime("%d-%m-%Y")<br></br>certname = today + "starcert"<br></br>certcmd = "crypto ca import " + certname + " pkcs12 asaexportpass"<br></br># build the commands needed to install the cert<br></br>commands = []<br></br>commands.append("conf t")<br></br>commands.append(certcmd)<br></br>commands = commands + cert<br></br>commands.append("")<br></br>commands.append("quit")<br></br>trustpoint = "ssl trust-point " + certname + " inside"<br></br>commands.append(trustpoint)<br></br><br></br># connect to the firewall <br></br>net_connect = Netmiko(**asav)<br></br>print(net_connect.find_prompt())<br></br><br></br># always a good test <br></br>output = net_connect.send_command_timing("show int ip br")<br></br><br></br># send the commands<br></br>for command in commands:<br></br>print(command)<br></br>net_connect.send_command_timing(command)<br></br><br></br><br></br># clean up <br></br>net_connect.disconnect()
```