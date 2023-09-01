--- 
title: "Active Directory Delegation done the PowerShell way!" 
date: 2023-03-11T08:23:11+01:00 
draft: false
comments: true

---

{{< figure
  src="/pics/gcm_dsacl.png"
>}}


## Active Directory Access Granting

The following module is very important for a Active Directory operations or engineering type person. Most who have tried granting permissions outside of adding users to groups in Active Directory would probably agree that, access delegation can be a daunting task.

Your best bet is to use the Delegation wizard, but wheres the fun (PowerShell fun..) in that. And how well does it scale? Not very well.

{{< figure
  src="/pics/delg_wiz.jpg"
>}}


## The alternatives

So, the code-friendly alternatives we have are:

- [DSACLS](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc771151(v=ws.11))

The first alternative, the native command DSACLS, can be a powerful way delegating, but is very frustrating to work with (or so I think).

- [Set-ACL](https://stackoverflow.com/questions/31375506/set-acl-on-ad-computer-object)

This alternative is probably your best bet outside of the module DSACL. However creating the directory access rules and applying them can be a bit cumbersome.

Both alternatives are fine, but will require you to do some heavy lifting in terms of learning. I will not go into depth into the alternatives here, it's just a good heads-up that these exists.

If you've learnt the basic of using a cmdlet in PowerShell, luckily for you, access delegation is just a module install away.

Using [DSACL](https://github.com/SimonWahlin/DSACL).

```powershell
# Install the DSACL Module
Install-Module -Name DSACL  -Scope CurrentUser

# Build a splatting table with the desired groups
$ParamSplat = @{
    TargetDN = "CN=SalesUsers,DC=Contoso,DC=COM" # DistinguishedName of object to modify ACL on. Usually an OU.
    DelegateDN = "CN=SalesAdmins,OU=Groups,DC=Contoso,DC=COM" # DistinguishedName of group or user to give permissions to.
    DirectOnGroup = $true
}

Add-DSACLManageGroupMember @ParamSplat

```
This example creates a [splatting](https://learn.microsoft.com/powershell/module/microsoft.powershell.core/about/about_splatting) table, and defines the objects to modify ACL (access control list), and user/group to give the permission to.

```powershell
$ParamSplat = @{
    TargetDN = "OU=Users,DC=Contoso,DC=COM"
    DelegateDN = "CN=SalesAdmins,OU=Groups,DC=Contoso,DC=COM"
    AccessType = 'Allow'
    ObjectTypeName = 'User'
    NoInheritance = $True
}

Add-DSACLFullControl @ParamSplat
```
Gives the SalesAdmins group Full Control over 'User' object administrations in the Users OU. Disables Inheritance, making the ACL only apply on the given OU (Users).


The module contains some really useful cmdlets for managing AD delegation, to name a few:

- Set-DSACLOwner
- Add-DSACLJoinDomain
- Add-DSACLResetPassword

```powershell
Get-Command -Module DSACL
```
I recommend checking out the [online documentation](https://github.com/SimonWahlin/DSACL/tree/master/docs) for each cmdlet, as well as running them in lab and verifying the ACL's once executed.

If your into AD and PowerShell, it's a fun and very learning experience to fire up some hyper-v VMs using Windows Server Evaluation ISO and a local client with some left-over RAM, and promote some domain controllers to test this module out. An awesome lab module that I recommend for very fast labs, are [AutomatedLab](https://automatedlab.org/en/latest/). If you want to go a more step-by-step and less abstracted way of setting up a lab, I recommend my own Hyper-V script [Labmil](https://ehmiiz.tech/blog/ps_simplehyper-vlabmodule/).

That's it. Try it out yourself to discover how easy directory access delegation can be. The amount of AD automation is endless once you get a hang of it, combined with the standard [ActiveDirectory](https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps) module.

## Happy coding