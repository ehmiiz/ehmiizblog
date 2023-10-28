--- 
title: "PowerShell: Super simple Hyper-V VM creation" 
date: 2023-10-25T14:58:11+01:00 
draft: false
comments: false

--- 

## Once again, meet [Labmil](https://github.com/ehmiiz/labmil).

2021, I [wrote about my script to generate hyper-v VMs](https://www.ehmiiz.se/blog/ps_simplehyper-vlabmodule/). I still use this way of creating my labs, and I think the simple nature of it is valuable.

The only requirements to using it is PowerShell, git and an iso file. Since it's specifically a hyper-v script, naturally it will require Windows.


```powershell
# Clone labmil
git clone https://github.com/ehmiiz/labmil.git

# Set iso path to desired VM OS
$IsoPath = "C:\Temp\WS2022.iso"

# Set working directory to the cloned repo
Set-Location labmil

# Create the VM with desired name, IsoPath is only needed once
if ( -not (Test-Path $IsoPath) ) {
    Write-Error "Path not found!" -ErrorAction Stop
}
.\New-LabmilVM.ps1 -Name "DC01" -IsoPath $IsoPath -Verbose
.\New-LabmilVM.ps1 -Name "DC02" -Verbose
```

{{< figure
  src="/pics/2023-10-28_labmil.png"
>}}

The above script can be used to get going. But I would recommend just writing down the git clone part, or remember it (the repo name).

After this, interactive use is very simple.

The idea behind the script is to demonstrate in what order you start using labmil.

1. Install it using git
2. Be aware of where your ISO is
3. Call the New-LabmilVM function, give the VM a name and, first time setup, provide the iso
4. Create how many VMs you want using a different name

## Features 2024 and forward

- No breaking changes! I like the simple nature of the lambil script and want to support the way it's working. It promotes learning but reduces repetitiveness.

- Optional parameters: 
    - Role: AD DS, AD CS: configures the OS, lightweight lab
    - NetworkSetup: Should automate internal switch and nic config

## AD Labbing

The reason labmil exists is because of my interest in labbing with Windows Server [but specifically AD DS](https://www.ehmiiz.se/blog/ps_ad_unsecure_by_default/).

I will be creating (even thought I know several other tools exist) a tool to populate a simple AD domain, with built in ACLs, OUs, Users, Computers, Group nesting, and security vulnerabilities. So I can automate setting up a new AD lab for myself but also for others.

Stay tuned!

## Happy labbing!