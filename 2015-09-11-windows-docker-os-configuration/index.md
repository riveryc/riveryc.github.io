# Windows Docker Os Configuration


We finished the OS installation earlier, now...

<!--more-->

Let’s start Configuring OS:

* Change Computer Name
* Add Feature to enable Container
* Network Settings: Static IP

I’ll try to make all above from PowerShell, so we only need to maintain the script rather than a document. :)

```PowerShell
Get-WindowsFeature -Name Containers | Install-WindowsFeature
$IPAddress = "192.168.100.150"
$LocalConnection = Get-NetIPAddress -InterfaceAlias "Ethernet" -AddressFamily IPv4
New-NetIPAddress -IPAddress $IPAddress -InterfaceIndex $LocalConnection.InterfaceIndex -AddressFamily IPv4 -PrefixLength 24 -DefaultGateway "192.168.100.254"
Set-DnsClientServerAddress -InterfaceIndex $LocalConnection.InterfaceIndex -ServerAddresses "192.168.100.254","8.8.8.8"
$NewComputerName = "Docker01-Gui"
Rename-Computer -NewName $NewComputerName -Restart
```

Windows Server 2016 will restarted with:

* New Feature “Containers” installed
* New Computer name as “Docker01-Gui”
* New IP: 192.168.100.150 with DNS 192.168.100.254

Let's check if all Configuration correct or wrong:

![screenshot](/img/post/20150911/windows-docker-os-configuration-1.png)

We can start next Configuration for Docker. Before doing that, I'd like to take snapshot first... :)
