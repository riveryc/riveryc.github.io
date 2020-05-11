# NetBIOS Impacts Cluster Service


As NetBIOS is a legacy technology and should be replaced by DNS in LAN, we are deciding to disable all NetBIOS on all NICs in our environment.

<!--more-->

However, we saw the issue straightaway: All AG SQL Cluster services are stopped…

![screenshot](/img/post/20151209/netbios-impacts-cluster-service-1.jpg)

Error message normally like:

```Event
Event ID: 1205: The Cluster service failed to bring clustered service or application ‘AGName’ completely online or offline. One or more resources may be in a failed state. This may impact the availability of the clustered service or application.

Event ID: 1069: Cluster resource ‘AG Network Resource Name’ of type ‘Network Name’ in clustered role ‘AGName’ failed.
Based on the failure policies for the resource and role, the cluster service may try to bring the resource online on this node or move the group to another node of the cluster and then restart it. Check the resource and group state using Failover Cluster Manager or the Get-ClusterResource Windows PowerShell cmdlet.
```

We are able to bring the IP online, but not the resource itself (same pic shows above).

It shows that’s the Network Resource issue, and it’s complaining about the Name cannot be registered with DNS.

By checking the properties of NIC, I found the NIC has NetBIOS disabled, which is what I want, but SQL AG Network IP Address has it enabled…

The way to fix it:

Clear the checkbox of `Enable NetBIOS for this address` in `IP Address` Property:

![screenshot](/img/post/20151209/netbios-impacts-cluster-service-2.jpg)

Problem fixed! :)

