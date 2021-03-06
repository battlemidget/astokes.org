---
layout: post.jade
title: x230T, realtek wifi, and my solution
date: 2013-03-22T15:00Z
tags: [ubuntu, linux]
---
<h2 id=&#34;recap&#34;>Recap</h2>
<p>I can ping all devices on the network <em>except</em> the gateway<br />
(192.168.0.1) and in turn can not access outside of the network<br />
without proxying through another device.</p>
<p>The system:</p>
<p>Lenovo x230 Tablet with a Realtek wifi adapter running on Quantal:</p>
<p>Network controller: Realtek Semiconductor Co., Ltd. RTL8188CE<br />
802.11b/g/n WiFi Adapter (rev 01)</p>
<p>The wtf:</p>
<p>I can obtain an IP from the router</p>
<pre class=&#34;prettyprint&#34;>
wlan0 Link encap:Ethernet HWaddr e0:06:e6:c2:d2:e0
inet addr:192.168.0.102 Bcast:192.168.0.255 Mask:255.255.255.0
inet6 addr: fe80::e206:e6ff:fec2:d2e0/64 Scope:Link
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
RX packets:178844 errors:0 dropped:0 overruns:0 frame:0
TX packets:101517 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:1000
RX bytes:121465876 (121.4 MB) TX bytes:10612848 (10.6 MB)
</pre>
<p>I can ping other devices:</p>
<pre class=&#34;prettyprint&#34;>
&#10140; ~ ping 192.168.0.101
PING 192.168.0.101 (192.168.0.101) 56(84) bytes of data.
64 bytes from 192.168.0.101: icmp_req=1 ttl=64 time=3.84 ms
64 bytes from 192.168.0.101: icmp_req=2 ttl=64 time=1.32 ms
</pre>
<p>I can not ping the gateway:</p>
<pre class=&#34;prettyprint&#34;>
&#10140; ~ ping 192.168.0.1
PING 192.168.0.1 (192.168.0.1) 56(84) bytes of data.
From 192.168.0.102 icmp_seq=1 Destination Host Unreachable
From 192.168.0.102 icmp_seq=2 Destination Host Unreachable
</pre>
<p>My resolv.conf is autogenerated with:</p>
<pre class=&#34;prettyprint&#34;>
&#10140; ~ cat /etc/resolv.conf
# Dynamic resolv.conf(5) file for glibc resolver(3) generated by
resolvconf(8)
# DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
nameserver 127.0.1.1
search nc.rr.com
</pre>
<p>My /etc/hosts:</p>
<pre class=&#34;prettyprint&#34;>
&#10140; ~ cat /etc/hosts
127.0.0.1 localhost
127.0.1.1 quantal
</pre>
<p>My routing table:</p>
<pre class=&#34;prettyprint&#34;>
&#10140; ~ route
Kernel IP routing table
Destination Gateway Genmask Flags Metric Ref Use Iface
default 192.168.0.1 0.0.0.0 UG 0 0 0 wlan0
link-local * 255.255.0.0 U 1000 0 0 wlan0
192.168.0.0 * 255.255.255.0 U 9 0 0 wlan0
</pre>
<p>Lastly, my module info for device:</p>
<pre class=&#34;prettyprint&#34;>
&#10140; ~ modinfo rtl8192ce
filename: /lib/modules/3.5.0-17-generic/kernel/drivers/net/wireless/rtlwifi/rtl8192ce/rtl8192ce.ko
firmware: rtlwifi/rtl8192cfw.bin
description: Realtek 8192C/8188C 802.11n PCI wireless
license: GPL
author: Larry Finger <Larry.Finger@lwfinger.net>
author: Realtek WlanFAE <wlanfae@realtek.com>
author: lizhaoming <chaoming_li@realsil.com.cn>
srcversion: DD4F3D83A75531AC98862F2
alias: pci:v000010ECd00008176sv*sd*bc*sc*i*
alias: pci:v000010ECd00008177sv*sd*bc*sc*i*
alias: pci:v000010ECd00008178sv*sd*bc*sc*i*
alias: pci:v000010ECd00008191sv*sd*bc*sc*i*
depends: rtlwifi,mac80211
vermagic: 3.5.0-17-generic SMP mod_unload modversions
parm: swlps:bool
parm: swenc:using hardware crypto (default 0 [hardware])
(bool)
parm: ips:using no link power save (default 1 is open)
(bool)
parm: fwlps:using linked fw control power save (default 1 is open)
(bool)
</pre>
<p>Things I&#39;ve attempted:</p>
<p>Turning off fwlps, ips. Attempted to compile a driver from upstream<br />
and even tried the latest daily mainline kernel for Quantal.</p>
<p>Has anyone seen this before? What really baffles me is that I can not<br />
ping the gateway. To verify I can ping the gateway from another<br />
system:</p>
<pre class=&#34;prettyprint&#34;>
~ : ping 192.168.0.1
PING 192.168.0.1 (192.168.0.1) 56(84) bytes of data.
64 bytes from 192.168.0.1: icmp_req=1 ttl=64 time=1.35 ms
64 bytes from 192.168.0.1: icmp_req=2 ttl=64 time=1.22 ms
64 bytes from 192.168.0.1: icmp_req=3 ttl=64 time=5.11 ms
</pre>
<p>This also doesn&#39;t happen outside of my network as I was able to use<br />
this laptop at UDS-R which kind of points to a router issue but<br />
anything without a Realtek adapter works :\</p>
<h2 id=&#34;solution&#34;>Solution</h2>
<p>Turns out that this particular laptop was having issues resolving dns queries because on my particular cable modem/router there was an option for &#34;Enable DNS relay&#34; that was not checked (off). Once I checked that option my laptop suddenly started working! I tried to do some more research on what could be a possible reason as to why this specific laptop/wifi combo requires dns relay to be enabled on the router in order to access anything outside my network? Another odd thing is that if I directly connect the laptop to the router everything works so I assume it is something to do with the actual wifi driver/hardware. Anyway, I&#39;m finally back to being able to use my recently purchased laptop :)</p>
<p>p.s - Im in London next week so if any kernel guys are feeling gracious and would like to look at my laptop I&#39;ll have it with me.</p>
