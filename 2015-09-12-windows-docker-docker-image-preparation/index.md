# Windows Docker - Docker Image Preparation


In order to get docker container working, we need the base image for any customization or modification.

<!--more-->

Firstly, get the WIM file ready: CBaseOs_th2_release_10514.0.150808–1529_amd64fre_ServerDatacenterCore_en-us.wim

This can be downloaded from MSDN.

Open up my favorite PowerShell ISE, check current Container status:

```PowerShell
#Check Current Container
Get-Container
#Check Current Container Image
Get-ContainerImage
#Check Container Host
Get-ContainerHost
```

As we can see, we got null value from the first two command due to we have nothing to run for Container, and following is the result for our Container HOST:

```PowerShell
Name         ContainerImageRepositoryLocation                              
----         --------------------------------                              
DOCKER01-GUI C:\ProgramData\Microsoft\Windows\Hyper-V\Container Image Store
```

Let’s check all cmdlet for Container in Windows:

```PowerShell
Get-Command *Container*
```

![screenshot](/img/post/20150912/windows-docker-docker-image-preparation-1.png)

There are 2 similar commands relates to create a new Container Image:

```PowerShell
Install-ContainerOSImage
Import-ContainerImage
```

By using "Get-Help", we know that we should use "Install-ContainerOSImage" to Installs a base image from a WIM file into the shared central image store for the Windows Server and Hyper-V Containers feature.

So, we are running below:

```PowerShell
Install-ContainerOSImage -WimPath "C:\WIM\CBaseOs_th2_release_10514.0.150808-1529_amd64fre_ServerDatacenterCore_en-us.wim"
```

Then we get following:
![screenshot](/img/post/20150912/windows-docker-docker-image-preparation-2.png)
It looks good, isn’t it?

A snapshot will be taken after this, because next step will be bit mass, if you want to try different things, you may want to do the same as me. :)
