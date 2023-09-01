--- 
title: "Analyze your Linux system using PowerShell"
date: 2023-04-08T08:23:11+01:00 
draft: false
comments: true

---

{{< figure
  src="/pics/linuxinfogallery.png"
>}}


## Install-Module [linuxinfo](https://github.com/ehmiiz/linuxinfo)

I am pleased to share that I have been working on a fun hobby project! A PowerShell module designed to facilitate Linux system analysis for PowerShell users. With its standardized noun-verb commands and object-based output, this module leverages the benefits of PowerShell to streamline analysis and information gathering on a Linux system.

Install it from the PowerShellGallery:

```powershell
Install-Module linuxinfo -Verbose
```

View it's functions:

```powershell
Get-Command -Module linuxinfo
```

```text
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        Get-BatteryInfo                                    0.0.1      linuxinfo
Function        Get-ComputerInfo                                   0.0.1      linuxinfo
Function        Get-DisplayInfo                                    0.0.1      linuxinfo
Function        Get-FileSystemHelp                                 0.0.1      linuxinfo
Function        Get-NetworkInfo                                    0.0.1      linuxinfo
Function        Get-OSInfo                                         0.0.1      linuxinfo
Function        Get-SystemUptime                                   0.0.1      linuxinfo
Function        Get-USBInfo                                        0.0.1      linuxinfo
```

Get computer information:

```powershell
Get-ComputerInfo
```

```text
BiosDate        : 06/17/2022
BiosVendor      : INSYDE Corp.
BiosVerson      : 03.09
CPU             : 11th Gen Intel(R) Core(TM) i5-1135G7 @ 2.40GHz
CPUArchitecture : x86_64
CPUThreads      : 8
CPUCores        : 4
CPUSockets      : 1
DistName        : Fedora Linux
DistSupportURL  : https://fedoraproject.org/
DiskSizeGb      : {930, 16}
DiskFreeGb      : {848, 16}
DiskUsedGb      : 82
GPU             : Intel Corporation TigerLake-LP GT2 [Iris Xe Graphics] (rev 01)
DistVersion     : 37 (KDE Plasma)
KernelRelease   : 6.2.9-200.fc37.x86_64
OS              : GNU/Linux
RAM             : 31.9G
```


Getting the Operating system information:

```powershell
Get-OSInfo
```

```
DistName      : Fedora Linux
DistVersion   : 37 (KDE Plasma)
SupportURL    : https://fedoraproject.org/
OS            : GNU/Linux
KernelRelease : 6.2.9-200.fc37.x86_64
OSInstallDate : 2023-03-25
```

## Beyond Hardware Info

There's more functions similar to the ones described above, where linuxinfo is parsing useful system information and displaying the output as a PSCustomObject. However, taking a look at a different kind of info:

```powershell
Get-FileSystemHelp -All
```

```
Name                           Value
----                           -----
root                           root users home directory
etc                            system-global configuration files
mnt                            temporary mount points
dev                            device files for hardware access
bin                            essential user binaries
run                            stores runtime information
opt                            optional application software packages
media                          mount point for external / removable devices
lost+found                     stores corrupted filesystem files
usr                            user utilities and applications
tmp                            temporary files
var                            variable files
lib                            system libraries and kernel modules
boot                           boot loader files
proc                           procfs - process and kernel information
sys                            sysfs - devices and kernel information
srv                            services data directories
sbin                           essential system binaries
home                           users home directories
```

Get basic information about the linux filesystem using PowerShell. Can be very handy if you're coming from a Windows background.

The function supports quick navigation using the `-Go` parameter, and displaying a richer help message with the `-Full` parameter.


## Testing & Disclaimer

Currently the module has been tested on Ubuntu and Fedora, so I'm fairly confident that it works good on Debian & RHEL distributions.

However I've done no testing on arch linux, therefore I'm not sure how the experience is there. It's also in an early stage (version 0.0.1), with improvement plans and new functionality. Be sure to hop on the GitHub[ repo](https://github.com/ehmiiz/linuxinfo) to learn more.

## Reason

I understand that the use-case for something like linuxinfo is a bit limited since Linux already has great tools for doing similar tasks. However this project is more of a personal journey into learning:

- [Parsing techniques](https://devblogs.microsoft.com/powershell-community/a-closer-look-at-the-parsing-code-of-a-crescendo-output-handler/)
- [What Linux binaries to use, when](https://ubuntu.com/blog/if-youre-still-using-ifconfig-youre-living-in-the-past)
- [Learning standard Linux binaries](https://diego.assencio.com/?index=1363692dafeabeff8e3f975077f92dfe)

But most importantly, having some fun with PowerShell and extending the usefulness and value of PowerShell.

I'd be happy if you'd like to try it, and star the github repo if you feel it's worth it.

## Happy coding