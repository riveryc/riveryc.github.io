# Windows Docker - First Run a Container


We’ve installed the WIM for the Container base Image, then we can see it from:

```PowerShell
Get-ContainerImage
```

<!--more-->

Here, we will do following:

* Use “WindowsServerCore” base Image to create a Container
* Start the Container
* Enter into the Container
* Read some basic information
* Make changes on folder and files
* Mark around with Registry
* Windows feature install or removal
* Exit from the Container
* Stop the Container
* Start it again
* Check values
* Stop and remove

There was something stupid I did for this Lab: I deleted system files, a lot…. and trying to install Windows Feature IIS and didn’t get successful. So I have to use a new Container to do the demo, that’s why you can see the Container ID is different from previous pic. So please be careful when you are trying delete system files. :)

Hopefully this will give you an idea about what container is and why do we need it…

* We are creating a new Container by using the Container base Image:

 ```PowerShell
 #Get the Container Image
 $containerImage = Get-ContainerImage -Name WindowsServerCore
 #Create the Container
 $testContainer = New-Container -Name testContainer -ContainerImage $containerImage
 ```

* Check the Container’s state

 ```PowerShell
 Get-Container
 ```

<!--img class="margin-left-40" src="/img/post/20150912/windows-docker-first-run-a-container-1.png"-->
![screenshot](/img/post/20150912/windows-docker-first-run-a-container-1.png)

* Start the Container

 ```PowerShell
 Start-Container -Container $testContainer
 ```

* Check the Container’s state again

 ```PowerShell
 Get-Container
 ```

![screenshot](/img/post/20150912/windows-docker-first-run-a-container-2.png)

* Enter into the Container with default user

 ```PowerShell
 Enter-PSSession -ContainerId $testContainer.ContainerId
 #find out current running user
 whoami
 ```

![screenshot](/img/post/20150912/windows-docker-first-run-a-container-3.png)

* Enter as Admin

 ```PowerShell
 Enter-PSSession -ContainerId $testContainer.ContainerId -RunAsAdministrator
 #find out current running user
 whoami
 ```

![screenshot](/img/post/20150912/windows-docker-first-run-a-container-4.png)

* Get some basic information inside and outside of this Container (Do compare)

 ```PowerShell
 #Get the Container’s Computername
 $env:computername
 #Get the Container Host’s Computername <- Don’t know if this is a bug for now
 [System.Net.Dns]::GetHostName()
 #Get PS Information
 $PSVersionTable
 #Get Windows Feature status for Web Server
 Get-WindowsFeature “Web-Server”
 #Get PS Drive Providers
 Get-PSDrive
 #Get Network information
 ipconfig
 ```

   > Running inside of the container:
![screenshot](/img/post/20150912/windows-docker-first-run-a-container-5.png)
   > Running out of the container:
![screenshot](/img/post/20150912/windows-docker-first-run-a-container-6.png)

* Simply delete some random files… or make some random folders in C:

You will find the file system is totally seperated between host and container

* Delete heaps things in HKLM:\Software:

```PowerShell
rm HKLM:\SOFTWARE -Recurse -Force
```

Same as above, the registry is totally seperated as well

* Install IIS feature in Container

![screenshot](/img/post/20150912/windows-docker-first-run-a-container-7.png)

* Exit Container -> Exit

* Stop Container

```PowerShell
Stop-Container $testContainer
```

* Start Container again, Check the value

![screenshot](/img/post/20150912/windows-docker-first-run-a-container-8.png)

* Remove Container

```PowerShell
Stop-Container $testContainer
Remove-Container $testContainer -Confirm: $false -Force
Get-Container
```

This is the basic operation for Container in Windows. As you can see, it’s a application level virtualisation technology. Next time, I’ll do some basic performance test to show how quickly we can bring up an application. :)

