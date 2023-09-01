--- 
title: "PowerShell PSCustomObject Variations"
date: 2023-04-01T08:23:11+01:00 
draft: true
comments: true

---

{{< figure
  src="/pics/PSCustomObjectTrick.png"
>}}


## PSCustomObject, valuable skill

PSCustomObject is a feature in PowerShell that allows you to create structured data in a simple way.

There's a ton to cover on the topic, but if your unfamiliar with it, first of all it's probably one of the most important thing to spend time on understanding in PowerShell, secondly the [PowerShell docs cover it very well](https://learn.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-pscustomobject?view=powershell-7.3).

In this blog post, I will cover a trick that I frequently use when generating structured data in form of objects that can later be piped to [Export-CSV](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/export-csv?view=powershell-7.3), or even better, [Export-Excel](https://adamtheautomator.com/powershell-excel/)

## Looping & Conditions

This trick involves when you want to create a boolean (true or false) value in your PSCustomObject variable. 
Here's an example of what I mean:

```powershell
# Create an array object in a variable
$PSCustomObject = @()

# Get some data
$Process = Get-Process | Select-Object Name, Description -Unique

# Loop through data
foreach($p in $Process) {
    # Check if condition exists
    if ($p.Description) {
      # If it does, create the "true" version of the PSCustomObject
        $PSCustomObject += [PSCustomObject]@{
            Name = $p.Name
            ProcessHasDescription = $true
            Description = $p.Description
        }
    }
    else {
      # If it does not, create the "false" version
        $PSCustomObject += [PSCustomObject]@{
            Name = $p.Name
            ProcessHasDescription = $false
            Description = $null
        }
    }
}

# Show results
$PSCustomObject | Select-Object -First 10
```
Output:

```text
Name             ProcessHasDescription Description
----             --------------------- -----------
audiodg                          False
Code                              True Visual Studio Code
CompPkgSrv                        True Component Package Support Server
concentr                          True Citrix Connection Center        
conhost                          False
conhost                           True Console Window Host
crashpad_handler                 False
csrss                            False
ctfmon                           False
dllhost                           True COM Surrogate
```

In this example, the `Get-Process` command is used to generate a list of system processes. The code then checks if a description is attached to each process. This technique can be applied to generate objects for all kinds of purposes. I've found it particularly useful for creating reports on Active Directory, computer hardware, access reports, or any other subject that requires a report with boolean values.

Some examples:

- User changed password the last 30 days?
- Computer disk less then 10gb left?
- User has `Full Control` rights on network share?
- Server answers on ping request?

## Steps

1. Generate an array of data
2. Loop it and construct a condition
3. If condition is met, create a PSCustomObject "true" block
4. Else, create a PSCustomObject "false" block
5. Export data

## Closing thoughts

In this post, I aim to keep things short and concise by letting the example do the talking. The code is commented for easy understanding. This technique can be incredibly useful for generating reports or structured data that can inform decision-making in larger processes. I hope you find it helpful and please let me know if you do.

Wishing you a great day and, as always:

## Happy coding