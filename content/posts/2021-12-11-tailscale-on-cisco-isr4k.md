---
author: Gary Ossewaarde
title: Tailscale on Cisco ISR4k
categories: ['networking']
date: 2021-12-11T21:45:28-04:00
---

<div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">### Tailscale &amp; IOx

I absolutely love using [tailscale](https://tailscale.com/). It uses a great new protocol, [wireguard](https://www.wireguard.com/), but makes it easy to configure and use. I’ve used it as a VPN service for over a year now, but have always wanted to connected to devices that are not tailscale capable.

My home router is a Cisco ISR4321. These are routers capable of running IOx, which allows you to run containers or even full-blown virtual machines on the router itself. The easiest way to get started with this is using [guestshell](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/prog/configuration/166/b_166_programmability_cg/guest_shell.html), which comes packaged with IOS-XE. I did not try to run tailscale in guestshell, I wanted to run a separate virtual machine.

### Build Tailscale Router VM

I built my tailscale router VM on Alpine Linux. The steps were pretty easy,

- Install Alpine to disk
- Enable community repos
- Install qemu-guest-agent and tailscale, `apk add qemu-guest-agent tailscale`
- Enable IP forwarding, add `net.ipv4.ip_forward = 1` and `net.ipv6.conf.all.forwarding = 1` to /etc/sysctl.conf
- Shutdown the VM

At this point, you need the disk image to be qcow2. I was using VMware to build my VM, so I had to convert it, `qemu-img convert -c -O qcow2 /tmp/alpinetailscale100.vmdk /tmp/alpinetailscale100.qcow2`

### Create the IOx Package

You need the ioxclient tool for this part. This can be downloaded from [DevNet here](https://developer.cisco.com/docs/iox/#!iox-resource-downloads/downloads). I opted to use the SDE virtual machine, which has all the necessary tools already installed. I copied my qcow2 to the VM and created my packages descriptor file, **package.yaml**

```
<pre class="">ioxsde@ioxsde:~$ cat package.yaml
descriptor-schema-version: "2.4"

info:
  name: tailscale
  description: "Alpine Linux with tailscale installed"
  version: "1.0"
  author-link: "http://next-hop-self.com"
  author-name: "Gary Ossewaarde"

app:
  type: vm
  cpuarch: x86_64
  resources:
    profile: custom
    cpu: 200
    memory: 1024
    disk: 2

    network:
      -
        interface-name: eth0
  # Specify runtime and startup
  startup:
    disks:
      -
        target-dev: "hdc"
        file: "alpinetailscale100.qcow2"

    qemu-guest-agent: True
```

Once you have this to your liking, you can create the package file with: `ioxclient package --name tailscale101 .`. This will create a file, tailscale101.tar in the folder you run the command in. Copy this file to your router: `scp tailscale101.tar admin@192.168.10.1:bootflash:tailscale101.tar`

### Install/Active App on Router

First, you need to configure your internal networking if you haven’t yet. The IOx apps use VirtualPortGroup interfaces to connect to the IOS part of the router. My interface configuration looks like this:

```
<pre class="">interface VirtualPortGroup0
ip address 10.0.0.1 255.255.255.0
ip nat inside
```

</div></div></div></div><div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Now, we can configure the app-hosting configuration stanza. Note **guest-interface 0** maps to **eth0** on the guest. For my alpine VM, I have DHCP enabled, so I also needed a DHCP pool.

```
<pre class="">app-hosting appid tailscale
 app-vnic gateway0 virtualportgroup 0 guest-interface 0
ip dhcp pool app-hosting
 network 10.0.0.0 255.255.255.0
 default-router 10.0.0.1
 dns-server 1.1.1.1
```

</div></div></div></div><div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Now, we can install, activate, start, then connect to the new VM. Note: for the appid, you can only use A-Z, a-z, and \_. I had a dash in the name and spent way too much time troubleshooting.

```
<pre class="">isr4k#app-hosting install appid tailscale package bootflash:tailscale101.tar
isr4k#app-hosting activate appid helloworld_app
isr4k#app-hosting start appid helloworld_app
isr4k#app-hosting connect appid helloworld_app console
```

At this point, you should be in the shell of your virtual machine.

```
<pre class="">localhost:~# ip addr
link/ether 52:54:dd:47:76:e8 brd ff:ff:ff:ff:ff:ff
inet 10.0.0.3/24 scope global eth0
valid_lft forever preferred_lft forever
inet6 fe80::5054:ddff:fe47:76e8/64 scope link
valid_lft forever preferred_lft forever
```

</div></div></div></div><div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">### Bring up Tailscale and Routes

Now that the VM is running, just a few things are left. First, bring up tailscale and add your routes. In the VM, `tailscale up --advertise-routes=192.168.10.0/24`. Once you run this command, you will need to copy and paste the tailscale URL into a browser and authenticate. Once this is activate, you will need to enable the routes in the tailscale admin portal. See [these docs](https://tailscale.com/kb/1019/subnets/) for more information.

Clients on Windows, macOS, iOS, and Android will automatically pick up your new subnet routes. For Linux, I had to tell also run the command `tailscale up --accept-routes.`

</div></div></div></div><div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">Finally, a route to the tailscale hosts is needed on your router. Redistribute as necessary. `ip route 100.64.0.0 255.192.0.0 10.0.0.3 name tailscale`

</div></div></div></div><div class="boldgrid-section"><div class="container"><div class="row"><div class="col-md-12 col-xs-12 col-sm-12">With that, test reachability to your other networks from other tailscale hosts. For me, it worked right away.

### Additional Resources

I had to combine information from the following two DevNet guides to work out what I needed.

- [Tutorial: Build sample LXC type IOx app using Docker toolchain](https://developer.cisco.com/docs/iox/#!tutorial-build-sample-vm-type-iox-app/tutorial-build-sample-vm-type-iox-app)
- [Tutorial: Build sample VM type IOx app](https://developer.cisco.com/docs/iox/#!lxc-workflow/build-docker-image)

</div></div></div></div>