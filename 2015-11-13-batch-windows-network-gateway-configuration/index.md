# Batch - Windows Network Gateway Configuration


When you got 2 NIC on the same VM, one is Internal NIC, another is External NIC:

* Internal: 172.23.0.0/24
* External: 172.20.0.0/16

<!--more-->

When you are trying to talk to out side of any of these 2 subnet, for example: 172.21.0.0, 10.52.0.0, etc…

The correct NIC the traffic should go through is via External. But somehow, you might be see this when you do route print:

![screenshot](/img/post/20151113/batch-windows-network-gateway-configuration-1.png)

We can see there are Double 0.0.0.0 entries with same metric value as default gateway.

We need to do 2 things:

```Dos
route –f –p -4 add 0.0.0.0 mask 0.0.0.0 172.20.0.1 metric 5
```

This will clear all existing gateway information but only the one we added

```Dos
shutdown /r /t 10
```

restart the computer in 10 seconds, enough time for the script to be renamed, this will help to rebuild the route table

Make sure run this as a batch cmd, because you will lost connectivity if you are in the RDP session…

Enjoy scripting…

