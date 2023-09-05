---
date: 2023-09-05
title: "My Modules"
---

{{< figure
  src="https://raw.githubusercontent.com/ehmiiz/linuxinfo/main/linuxinfoico.png"
>}}

## [PowerShellGallery Modules](https://www.powershellgallery.com/packages?q=elarsson)

In this section you'll find a list of my published PSGallery modules. 

My modules vary in functionality and usefulness. I believe in learning by doing, and some projects are simply created for myself, to learn git, PowerShell, c#, linux binaries, and what have you. I try to stick to a few key pointers though:

- Cross-platform

- Open-Source

- Documented

- Support -WhatIf if a command touches data

- Not pollute the shell/operating system, a `Uninstall-Module` will simply remove any trace of the module


### [linuxinfo](https://github.com/ehmiiz/linuxinfo)

Gets valuable information on a linux system using PowerShell.

```powershell
Install-Module -Name 'linuxinfo' -Scope CurrentUser
```

### [BinaryPasswordGenerator](https://github.com/ehmiiz/PasswordGenerator)

A PowerShell revamp of the popular 'PasswordGenerator' Nuget package. Use to generate Passwords, OTPs or PINs for personal use or in projects.

```powershell
Install-Module -Name 'BinaryPasswordGenerator' -Scope CurrentUser
```

### [ExplainLinuxFS](https://github.com/ehmiiz/ExplainLinuxFS)

Get assistance navigating the Linux File System using PowerShell!

```powershell
Install-Module -Name 'ExplainLinuxFS' -Scope CurrentUser
```

### [PowerShell-Docs-CommunityModule](https://github.com/ehmiiz/PowerShell-Docs-CommunityModule)

Verifies a level of quality to the PowerShell-Docs repo, making it easy for a community contributor to know what to start working on.

```powershell
Install-Module -Name 'PowerShell-Docs-CommunityModule' -Scope CurrentUser
```

### [PSHangMan](https://github.com/ehmiiz/PSHangMan)

A Hang-man game in PowerShell with tech companies as guessing words and bad/good comments about them depending on the games outcome.

```powershell
Install-Module -Name 'PSHangMan' -Scope CurrentUser
```

### [FilesToFolders](https://github.com/ehmiiz/FilesToFolders)

The PowerShell cmdlet `Move-FileToFolder` will move files to newly created folders based on day, month or year naming format.

```powershell
Install-Module -Name 'FilesToFolders' -Scope CurrentUser
```