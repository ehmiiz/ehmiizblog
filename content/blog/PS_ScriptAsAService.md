--- 
title: "PowerShell Guide: Script as a Windows Service" 
date: 2024-03-26T14:58:11+01:00 
draft: false
comments: false

--- 

{{< figure
  src="/pics/red_blue.png"
>}}


## Red or blue pill

If you are in the same rabbit-hole as I was of setting up a Windows Service of any form of looping script, there's two pills you can choose from:

1. Red Pill: Create a program that abide to the law of the fearsome [Service Control Manager](https://learn.microsoft.com/en-us/windows/win32/services/service-control-manager).

2. Blue Pill: Write a PowerShell script, 8 lines of XML, and download [WinSW.exe](https://github.com/winsw/winsw)

WinSW describes itself as following:

`A wrapper executable that can run any executable as a Windows service, in a permissive license.`

Naturally as someone who enjoys coding with hand grenades, I took the Blue Pill and here's how that story went:


## The Blue Pill

1. Create a new working directory and save it to a variable

```powershell
$DirParams = @{
    ItemType    = 'Directory'
    Name        = "PowerShell_Service"
    OutVariable = 'WorkingDirectory'
}
New-Item @DirParams
```

2. Download the latest WinSW-x64.exe to the working directory
```powershell
# Get the latest WinSW 64-bit executable browser download url
$ExecutableName = 'WinSW-x64.exe'
$LatestURL = Invoke-RestMethod 'https://api.github.com/repos/winsw/winsw/releases/latest'
$LatestDownloadURL = ($LatestURL.assets | Where-Object {$_.Name -eq $ExecutableName}).browser_download_url
$FinalPath = "$($WorkingDirectory.FullName)\$ExecutableName"

# Download it to the newly created working directory
Invoke-WebRequest -Uri $LatestDownloadURL -Outfile $FinalPath -Verbose
```
3. Create the PowerShell script which the service runs

This loop checks for notepad every 5 sec and kills it if it finds it

```powershell
while ($true) {
    $notepad = Get-Process notepad -ErrorAction SilentlyContinue
    if ($notepad) {
        $notepad.Kill()
    }
    Start-Sleep -Seconds 5
}
```

4. Construct the .XML file

Just edit the `id`, `name`, `description` and `startarguments`

```xml
<service>
  <id>PowerShellService</id>
  <name>PowerShellService</name>
  <description>This service runs a custom PowerShell script.</description>
  <executable>C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe</executable>
  <startarguments>-NoLogo -file C:\Path\To\Script\Invoke-PowerShellServiceScript.ps1</startarguments>
  <log mode="roll"></log>
</service>
```

Save the .xml, in this example I saved it as `PowerShell_Service.xml`

```powershell
# if not already, step into the workingdirectory
cd $WorkingDirectory.FullName

# Install the service
.\WinSW-x64.exe install .\PowerShell_Service.xml

# Make sure powershell.exe's executionpolicy is Bypass
Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope LocalMachine

# As an administrator
Get-Service PowerShellService | Start-Service
```

## Conclusion

Running a PowerShell script as a service on any windows machine isn't that complicated thanks to WinSW. It's a great choice if you don't want to get deeper into the process of developing windows services (it's kind of a fun rabbit-hole though).

I recommend reading [docs of WinSW](https://github.com/winsw/winsw?tab=readme-ov-file#documentation).

Some things to consider:

- The service will run PowerShell 5.1 as System
- Meaning the executionpolicy must be supporting that usecase (bypass as local machine will do)
- The script in this example is just a demo of a loop, but anything you can think of that loops will do here
- Starting the Service requires elevated rights in this example
- If you get the notorious `The service did not respond to the start or control request in a timely fashion`, you have my condolences (This is a very general error msg that has no clear answer by itself it seems)

### Good luck have fun, happy coding

### /Emil