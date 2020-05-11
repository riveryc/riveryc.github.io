# Windows Docker Basic Performance


As we want to use Docker’s Container technology to help us fast deployment, but how fast it is?

<!--more-->

Let’s do a quick count here:

* Create a brand new Container
* Start up the Container
* Enter into its session
* Remove the Container

Let’s see how it goes:

* Create a brand new Container, 2 seconds, not bad, how fast you can create a new VHD disk which contains a OS?

```PowerShell
$containerImage = Get-ContainerImage -Name WindowsServerCore
Measure-Command {$testContainer = New-Container -Name testContainer -ContainerImage $containerImage}
```

![screenshot](/img/post/20150913/windows-docker-basic-performance-1.png)

* Start up the Container, almost 8 seconds to get a fully boot OS, how fast does your SSD give your an OS from the moment while you press the power button?

```PowerShell
Measure-Command {Start-Container -Container $testContainer}
```

![screenshot](/img/post/20150913/windows-docker-basic-performance-2.png)

* Enter to Container’s Session, 6 seconds to get into the Container’s session, which is similar as normal speed to remote manage other computer. But, don’t forget this is a new machine that it’s not joined the domain, so it’s using local authentication…

```PowerShell
Measure-Command {Enter-PSSession -ContainerId $testContainer.ContainerId -RunAsAdministrator}
```

![screenshot](/img/post/20150913/windows-docker-basic-performance-3.png)

* Stop the Container then remove it. Total 15 seconds.

```PowerShell
Measure-Command {Stop-Container $testContainer;Remove-Container $testContainer -Confirm: $false -Force}
```

![screenshot](/img/post/20150913/windows-docker-basic-performance-4.png)

Generally, it looks pretty good. As long as we prepared the Image well, with docker build file, we will get much better deployment experience I believe. :)

Then we might want to know how it goes with Hardware consumption (Mostly it’s memory) compare to the VM.

* The current VM I’m running is using 795MB Memory.
* During the creation of a new Container, it increased to 810MB due to Container Service got started.
* Start creating the 2nd Container, the memory usage didn’t change too much, around 815MB.
* Startup one of the container, memory usage increased to 930MB, “CExecSvc.exe” started with session ID = 3
* Startup the 2nd one, memory usage increased to 1GB, about 70MB different from last state, 2nd “CExecSvc.exe” started with session ID = 4
* Stop all Containers, memory dropped back to 830MB. all “CExecSvc.exe” terminated
* Remove all Containers, memory didn’t change.

From this basic test, we can see an Container OS initialized with around 100MB memory usage, and it will only increase if it uses more resources.

In order to use Container in the real world, we need to bring network feature in to let it run some applications. :) Will introduce this in the next blog.
