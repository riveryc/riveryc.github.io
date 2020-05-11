# Powershell Working Behind a Proxy With Authentication


PowerShell cmdlet with proxy? - Well, this is for Windows User...

<!--more-->

Sometimes you will need to work in a company that has a Proxy Server configured for security reason… I know it’s bit annoying.

Especially when I’m trying to connect my Azure Account:

```PowerShell
Add-AzureAccount -Credential (Get-Credential)
```

Then I got:

```Dos
PS C:\Users\ryang> Add-AzureAccount -Credential (Get-Credential)
cmdlet Get-Credential at command pipeline position 1
Supply values for the following parameters:
Add-AzureAccount : user_realm_discovery_failed: User realm discovery failed: The remote server returned an error: (407) Proxy Authentication Required.
At line:1 char:1
+ Add-AzureAccount -Credential (Get-Credential)
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 + CategoryInfo : CloseError: (:) [Add-AzureAccount], AadAuthenticationFailedException
 + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.Profile.AddAzureAccount
```

Proxy Authentication… ah…

By checking the winHTTP settings:

```Dos
PS C:\Users\ryang> netsh winhttp show proxy
Current WinHTTP proxy settings:
Direct access (no proxy server).
```

All we need to do is:

* Tell PowerShell using the same Proxy Setting as IE
* Pass my current Credential to Proxy

Firstly...

```Dos
netsh winhttp import proxy source=ie
```

Then with the Credential...

```PowerShell
$webclient=New-Object System.Net.WebClient
$webclient.Proxy.Credentials= Get-Credential
```

Try it again....

