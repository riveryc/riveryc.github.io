# Delete Content of folder Containig Files With Open File Handles Using Powershell


When removing a folder, and it complains the files in the folder are in used, how to ignore the error and force them to be removed?

<!--more-->

```PowerShell
function KillProcessesWithHandles
{
    param([string]$path)

    $allProcesses = Get-Process
    # Start by closing all notepad processes. Someone may have left a logor config file open
    $allProcesses | where {$_.Name -eq "notepad"} | Stop-Process -Force -ErrorAction SilentlyContinue

    # Then close all processes running inside the folder we are trying to delete
    $allProcesses | where {$_.Path -like ($path + "*")} | Stop-Process -Force -ErrorAction SilentlyContinue

    # Finally close all processes with modules loaded from folder we are trying to delete
    foreach($lockedFile in Get-ChildItem -Path $path -Include * -Recurse) {
        foreach ($process in $allProcesses) {
            $process.Modules | where {$_.FileName -eq $lockedFile} | Stop-Process -Force -ErrorAction SilentlyContinue
        }
    }
}
```

* Terminate notepad process
* Terminate any relevant process that may using that file
* Terminate any process that is loading that file…

# Only use this on your own risk

Happy scripting. :)
