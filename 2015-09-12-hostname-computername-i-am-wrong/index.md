# HostName = ComputerName? I Am Wrong!


Is there any different between the result of "Hostname" & "$env:ComputerName" ?

<!--more-->

Before Windows Server 2016, I was thinking that they are not the same is because of following:

* hostname is a exe wrapper from MS, it returns full computer name, even it’s more than 15 characters.
* $env:computername is a var in PowerShell, it returns NetBIOSComputername which is limited in 15 characters

But today, I’m so wrong….

Environment:

* Container Host: Docker01-Gui
* Container name: < Random >

By using following command:

```PowerShell
Write-Host '$env:computername:' $env:computername
$hostname = hostname
Write-Host 'hostname.exe:' $hostname
$w32compsys = Get-WMIObject Win32_ComputerSystem | Select-Object -ExpandProperty name
Write-Host 'Get-WMIObject Win32_ComputerSystem | Select-Object -ExpandProperty name:' $w32compsys
Write-Host '(Get-CIMInstance CIM_ComputerSystem).Name:' (Get-CIMInstance CIM_ComputerSystem).Name
$machinename = [system.environment]::MachineName
Write-Host '[system.environment]::MachineName:' $machinename
$dnsname = [System.Net.Dns]::GetHostName()
Write-Host '[System.Net.Dns]::GetHostName():' $dnsname
```

We got result in Container Host:

```PowerShell
$env:computername: DOCKER01-GUI
hostname.exe: Docker01-Gui
Get-WMIObject Win32_ComputerSystem | Select-Object -ExpandProperty name: DOCKER01-GUI
(Get-CIMInstance CIM_ComputerSystem).Name: DOCKER01-GUI
[system.environment]::MachineName: DOCKER01-GUI
[System.Net.Dns]::GetHostName(): Docker01-Gui
```

We got result in Container itself:

```PowerShell
$env:computername: WIN-E2ABUUD2V1V
hostname.exe: Docker01-Gui
Get-WMIObject Win32_ComputerSystem | Select-Object -ExpandProperty name: WIN-E2ABUUD2V1V
(Get-CIMInstance CIM_ComputerSystem).Name: WIN-E2ABUUD2V1V
[system.environment]::MachineName: WIN-E2ABUUD2V1V
[System.Net.Dns]::GetHostName(): Docker01-Gui
```

As we can see above, “hostname.exe” and “[System.Net.Dns]::GetHostName()” are returning Container Host’name which is not the real value I think it should be…

I’m not sure if this is the expected value or a bug, but anyway, I’m going to use $env:computername to retrieve the local logic physic host in the future.

How about you?
