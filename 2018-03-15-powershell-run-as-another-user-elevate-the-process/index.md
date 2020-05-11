# PowerShell Run as Another User & Elevate the Process


When you have UAC enabled, most of the time, the default PowerShell ISE is opened with normal process. This is not going to work when you want to apply the DSC resources. Have to have an administrative PowerShell process running to get it done.

<!--more-->

However, my own account doesn’t have certain permission to access an encrypted vault. Thus I have to run as another account.

Here is what we should do…

Open up PowerShell Console as administrator by right clicking the icon, and select `“Run as Administrator”`.

Then put in following: (assume the user you are going use is Domain user `“domain\superuser”`)

```PowerShell
Start-Process powershell.exe -Credential “Domain\SuperUser” -ArgumentList “Start-Process powershell_ise.exe -Verb runAs”
```

The new PowerShell ISE with `“Domain\SuperUser”` credential & Administrator permission is open. :)

Enjoy…

