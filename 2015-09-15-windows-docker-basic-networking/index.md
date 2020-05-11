# Windows Docker Basic Networking


## We got the Container, then we will need to used it for our application via Network.

<!--more-->

### Creating network for our Container now:

#### No matter what, we need to create a Virtual Switch on Container HOST

```PowerShell
$VMSwitch = Get-VMSwitch
if ($VMSwitch.Count -le 0)
{
 #Creating a VMSwitch
 New-VMSwitch -Name “Virtual Switch” -SwitchType NAT -NATSubnetAddress “172.16.0.0/12”
}
```

#### Create a NAT

```PowerShell
#Creating a Net Nat 
New-NetNat -Name ContainerNAT -InternalIPInterfaceAddressPrefix “172.16.0.0/12”
```

#### Create a container with this Switch

```PowerShell
$IISContainer = New-Container -Name IISContainer -ContainerImage $containerImage -SwitchName “Virtual Switch”
```

### Start this Container and get Web-Server windows feature installed

```PowerShell
Install-WindowsFeature -Name Web-Server
```

#### Map Container HOST and Container itself with TCP port 80

```PowerShell
Add-NetNatStaticMapping -NatName ContainerNat -Protocol TCP -ExternalIPAddress 0.0.0.0 -InternalIPAddress 172.16.0.2 -InternalPort 80 -ExternalPort 80
```

#### Allow firewall on 80 port

```PowerShell
New-NetFirewallRule -Name “Web Server” -DisplayName “HTTP on TCP/80” -Protocol tcp -LocalPort 80 -Action Allow -Enabled true
```

Now, we are able to access the default website from another computer in the network. :)
