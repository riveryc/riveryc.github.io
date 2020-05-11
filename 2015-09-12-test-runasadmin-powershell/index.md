# Test RunAsAdmin PowerShell


Got several times script running failed due to the ISE or PowerShell didn’t run as Administrator. Uhmm, trying to show it as log or debug warning for this...

<!--more-->

In order to make sure we are running the script as Administrator, we can use following script to test whether we are going to run this or not:

```PowerShell
function Test-RunAsAdmin()
{
    # Get the ID and security principal of the current user account
    $myWindowsID=[System.Security.Principal.WindowsIdentity]::GetCurrent()
    $myWindowsPrincipal=new-object System.Security.Principal.WindowsPrincipal($myWindowsID)
  
    # Get the security principal for the Administrator role
    $adminRole=[System.Security.Principal.WindowsBuiltInRole]::Administrator
  
    # Check to see if we are currently running "as Administrator"
    if ($myWindowsPrincipal.IsInRole($adminRole))
    {
        $global:AdminPriviledges = $true
        return $true
    }
    else
    {
        #
        # We are not running "as Administrator"
        # Exit from the current, unelevated, process
        #
        throw "You must run this script as administrator"
        return $false  
    }
}
```

Enjoy Scripting~~
