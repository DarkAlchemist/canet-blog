---
title: NIC Death
date: '2014-02-01 21:01:12'
---

In the recent week, I've run across the annoying issue of trying to debug why my network, specifically my home server access was slow and sporadic. Given my router had been acting up since a power outage at Christmas, I believed that might be the cause but it just didn't make sense. Having done some tests with my housemate's hardware, I narrowed it down to my home server.

My initial issues manifested whilst trying to play videos on my PS3 via streaming, something that usually isn't a problem. Having upgraded PS3 Media Server recently and using a video I wasn't sure about, I gave up trying to fix it at the time. What worried me is that when I was trying to access files via a Samba share on the same server, it would start to play briefly then die, even on videos that used to work fine!

So, I take to the shell to try and diagnose the issues!

* Nothing in the Samba logs to indicate what's going on.
* `ifconfig` decides to tell me that there's no packet loss on the Gigabit interface at all

All I get is this slightly obscure message in `syslog` from the kernel


	Jan 27 00:07:56 rinoa kernel: [  584.495023] r8169 0000:01:06.0 eth1: link down
	Jan 27 00:07:59 rinoa kernel: [  586.852168] r8169 0000:01:06.0 eth1: link up
	Jan	27 00:08:22 rinoa kernel: [  609.994358] r8169 0000:01:06.0 eth1: link down
	Jan 27 00:08:24 rinoa kernel: [  612.328398] r8169 0000:01:06.0 eth1: link up
	Jan 27 00:08:44 rinoa kernel: [  632.046656] r8169 0000:01:06.0 eth1: link down
	Jan 27 00:08:46 rinoa kernel: [  634.587853] r8169 0000:01:06.0 eth1: link up
    
Why is the interface going up and down like this? Google didn't really have a good answer for me. Investigation led me to replacing the ethernet cable, which just seemed to make the situation worse. In vain effort, I also tried updating my Linux kernel to the latest stable to see if that resolved the issue. I settled in the end for reverting to the 100mbit internal NIC to see if that fared any better. Despite the downgrade in speed, it's been 100% reliable. 

So, if you see this and you've tested your switch and ethernet cable, your NIC is pretty much FUBAR. Just wish this info was available more easily!