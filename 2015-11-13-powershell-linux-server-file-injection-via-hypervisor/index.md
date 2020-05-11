# PowerShell Linux Server File Injection via Hypervisor


Previous [Post](/post/2015-11-13-batch-windows-network-gateway-configuration/) indicates that we are able to do one way copy files from HOST level to VM itself in Windows.

<!--more-->

We are able to do the same thing in Linux too. :)

I don’t have Ubuntu, so I didn’t got chance to test, but for Red Hat or CentOS:

* On Linux Guest VM:

```Bash
yum -y install hyperv-daemons
```

* On Hyper-V Host or PowerShell remote session:

```PowerShell
Enable-VMIntegrationService -ComputerName $VM.VMHost -VMName $VM.Name -Name “Guest Service Interface”
```

* On Linux Guest VM:

```Bash
reboot
```

* On Hyper-V Host or PowerShell Remote Session:

```PowerShell
Get-VMIntegrationService -ComputerName $VM.VMHost -VMName $VM.Name -Name “Guest Service Interface”
```

* We should be able to see that the `Guest Service Interface` is `enable` and the `status` is `OK` to communicate with the HOST, if not, check the service called `hypervfcopyd` is running or not on Linux Guest VM

* Finally, we are able to use following command to do the file copy:

```PowerShell
Copy-VMFile -ComputerName $VM.VMHost -VMName $VM.Name -SourcePath “D:\Test.txt” -DestinationPath “/etc/folder” -CreateFullPath -FileSource Host
```

**But note here, Linux command is bit different from Windows, In windows we use `file name` as the `Source` and `Destination` in the same way. But this doesn’t work in Linux.**

In Linux, we use `file name` as the `source` and we use the **folder** that should contains the file as the `destination` Path.

Enjoy your magic script without Network Connection!

