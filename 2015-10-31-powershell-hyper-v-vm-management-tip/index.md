# PowerShell Hyper-V VM Management Tip


If there is a way that we can manage our VMs from the hyper-visor level, like invoke command from HOST directly to VM, that would be awesome!!!!

<!--more-->

However, the method is only available from Windows 10/Server 2016 called: [PowerShell Direct](http://blogs.technet.com/b/virtualization/archive/2015/05/14/powershell-direct-running-powershell-inside-a-virtual-machine-from-the-hyper-v-host.aspx)

For the current users who is running on Windows Server 2012 & Windows Server 2012R2, we can use following trick to get it work. We will following resource to get this done:

* An AD account with enough permission
* Hyper-V admin
* Run Administration PowerShell script from the Hyper-V host

Login the VM:

* Open up Schedule Task, create a new task by using the previous AD Account
* Run this task every 5 mins <- Can be any time interval you want.
* Action: run a PowerShell script “C:\Script\ScheduledPowerShellCaller.ps1” to call another script (Script content will be provided later)
* Program File: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
* Argument: -File C:\scripts\ScheduledPowerShellCaller.ps1

ScheduledPowerShellCaller.ps1:

```PowerShell
$RemoteCMDPath = "C:\scripts\RemoteCMD.ps1"
if(Test-Path $RemoteCMDPath)
{
    . $RemoteCMDPath
    Remove-Item $RemoteCMDPath
}
```

The way to call it:

* Create a new script: RemoteCMD.ps1
* New-Item -Path C:\testfolder$((Get-Date).ToString(“HHmmss”)) -ItemType Directory
* Copy the file into VM by using `Copy-VMFile`:

```PowerShell
Copy-VMFile -VM $VM -SourcePath C:\Scripts\RemoteCMD.ps1 -DestinationPath C:\Scripts\RemoteCMD.ps1 -FileSource Host
```

Check the VM’s C Drive see the change in 5 mins. New folder is created automatically, and the RemoteCMD.ps1 is gone to prevent it’s running again :)

