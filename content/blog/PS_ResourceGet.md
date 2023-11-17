--- 
title: "PowerShell 7.4: Install-Module is evolving."
date: 2023-11-16T08:23:11+01:00 
draft: false
comments: true

---

{{< figure
  src="/pics/evolvepokemon.png"
>}}

## Where does Install-Module come from?

`Install-Module` has evolved.

Have you ever asked yourself, what module imports the `Install-Module` cmdlet?
It's kind of a meta question, check for yourself! Spoiler a bit down for anyone reading on mobile.

```powershell
Get-Command -Name Install-Module
```

<details>
  <summary>Spoiler</summary>

```
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Function        Install-Module                                     2.2.5      PowerShellGet

```
</details>

## New sheriff in town 🤠

With the [GA release of PowerShell 7.4](https://devblogs.microsoft.com/powershell/powershell-7-4-general-availability/) a rewrite of PowerShellGet is included, (hint hint, renamed to [PSResourceGet](https://devblogs.microsoft.com/powershell/psresourceget-is-generally-available/)), and boy is it fast.

I installed PowerShell 7.4 on two different Ubuntu 20.04 WSL distros, and I installed a few modules to benchmark the old trusty `Install-Module` and the new sheriff in town: `Install-PSResource`.

The results speak for themselves. PSResourceGet is much faster then PowerShellGet V2.

Speaking about PowerShellGet V2, there's still a future for this module, but instead of new APIs and features, V3 (currently in pre-release) was converted to a compatibility layer over to the new and faster PSResourceGet.

{{< figure
  src="/pics/PSResourceGetModules.png"
>}}

```powershell
Install-Module -Name PowerShellGet -AllowPrerelease -Force
```
{{< figure
  src="/pics/notperfectcompat.png"
>}}

There parameters of the new PSResourceGet is not supported from calling the older cmdlets, and there's no official documentation out for PowerShellGet V3 yet, so to me this seems purely for pipeline scenarios where you have code in place that can just use the new functionality. It has less to do with interactive use it seems. [Here's some further reading](https://devblogs.microsoft.com/powershell/powershellget-3-0-22-beta22-is-now-available/) on the subject.

## Let's take it for a spin

PSResourceGet seem to me an awesome new module based on it's speed, so better get used to it's new syntax because this will be my new main driver for sure.

```powershell
Get-Command -Module Microsoft.PowerShell.PSResourceGet | sort Name
```

```Output
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Find-PSResource                                    1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Get-InstalledPSResource                            1.0.1      Microsoft.PowerShell.PSResourceGet
Alias           Get-PSResource                                     1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Get-PSResourceRepository                           1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Get-PSScriptFileInfo                               1.0.1      Microsoft.PowerShell.PSResourceGet
Function        Import-PSGetRepository                             1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Install-PSResource                                 1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          New-PSScriptFileInfo                               1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Publish-PSResource                                 1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Register-PSResourceRepository                      1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Save-PSResource                                    1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Set-PSResourceRepository                           1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Test-PSScriptFileInfo                              1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Uninstall-PSResource                               1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Unregister-PSResourceRepository                    1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Update-PSModuleManifest                            1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Update-PSResource                                  1.0.1      Microsoft.PowerShell.PSResourceGet
Cmdlet          Update-PSScriptFileInfo                            1.0.1      Microsoft.PowerShell.PSResourceGet
```

It's not only installing modules that's faster:

## What's installed?

Getting installed modules can be very time-consuming on shared systems, especially where you have the az modules installed, so this is a great performance win overall.


{{< figure
  src="/pics/gettingmodules.png"
>}}

## Find new stuff

Finding new modules and scripts is also a crucial part of PowerShell, especially for the community members. I would argue with PSResourceGet going GA, PowerShell 7.4 is probably one of the most significant performance boosters of PowerShell (in it's open source life).

{{< figure
  src="/pics/find-module.png"
>}}

As you can see, finding modules seems way faster, and there's not many matches on NTFS, so this scales well.

## What about publishing?

Let's try to use the new `Publish-PSResource`. I have a minor bug-fix to do on my project [linuxinfo](https://www.ehmiiz.se/blog/ps_linuxinfo_release/) and will edit my publishing script so that github action will publish it for me using Publish-PSResource.

I start with editing my very simple publishing script. Since I don't know if the github-hosted runner will have PSResourceGet installed yet, I need to validate that the cmdlet is present before calling it. If it's not, I'm simply installing it using PowerShellGet v2.

This should do it!

{{< figure
  src="/pics/publish-psresource_0.png"
>}}

{{< figure
  src="/pics/publish-psresource_1.1.png"
>}}


Hmm, seems like I messed something up. The Github-hosted runner can't find Publish-PSResource so, it's trying to install PSResourceGet using `Install-Module`. However I miss-spelled the module name if you look closely at line 7. It should be `Microsoft.PowerShell.PSResourceGet`, let's fix that and re-run my workflow.

{{< figure
  src="/pics/publish-psresource_1.png"
>}}

Looks way better now!

{{< figure
  src="/pics/publish-psresource_3.png"
>}}

And there's a new version of `linuxinfo` with a minor bugfix. And the Publish-PSResource migration was very straightforward.

## Conclusion

In this post, we learned about the origin of `Install-Module`, being PowerShellGet v2, and it's predecessor `Install-PSResource`, being PSResourceGet. We took some cmdlets for a spin and realized that, the new version is easily twice as fast, in some cases even 3 times faster.

We covered PowerShellGet V3 being a compatibility layer and some caveats with it.

We looked at migrating a simple publishing script from `Publish-Module` to `Publish-PSResource`.

I recommend to poke around with the new PSResourceGet cmdlets and [read it's official documentation](https://learn.microsoft.com/powershell/module/microsoft.powershell.psresourceget/?view=powershellget-3.x), and for interactive use not rely on any compatibility layer, save that for the edge-cases.

Thanks for reading this far, hope you found it helpful. PM me on twitter for any feedback.

### Happy coding
### /Emil