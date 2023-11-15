---
date: "2023-09-01"
draft: false
lastmod: "2023-09-01"
publishdate: "2023-08-25"
tags:
- powershell
- security
- passwords
- automation
title: PowerShell KeePass and saving time
toc: true
---

{{< figure
  src="/pics/ffs_modules.png"
>}}

Glad to be back from a 7-month dad leave. Let's dive into some timesaving PowerShell!

### The Problem

Password managers are very useful for anyone having more than one set of credentials, and most of us do.

They reduce the chance of credential leakage to unauthorized people and are vastly superior both post-it papers and notepad files.

However, I found myself using the graphical user interface (GUI) of my password manager daily to simply search copy and paste secret. The problem with navigating a GUI every day is that it's time consuming, and there's room for improvement, especially if you enjoy delving into some PowerShell and/or always have a terminal open.

Summary: Password managers GUIs are slow and tedious to work with. Let's explore an alternative that is much faster!


### My Solution

The solution to this problem that I went with was to create a custom script, which will install and configure my PowerShell session to easily access my password manager after typing in its master password. Together with a couple of functions to easily retrieve and copy-paste my password to the clipboard.

Setting something to your clipboard, especially a password, is a risk since other applications also can access the clipboard, thus the clipboard needs to be cleared by setting a sleep timer and overwriting the secret.

As a start, I will need to create a couple of parameters so the input becomes dynamic, so that I can use the script regardless of what filepath or database name I have on the computer.

```powershell
param (
    [Parameter(Mandatory)]
    [string]$KeePassFilePath,
    [Parameter(Mandatory)]
    [string]$KeePassDataBaseName
)
```

The modules I will be using in my script is:

```powershell
$Modules = "SecretManagement.KeePass", "Microsoft.PowerShell.SecretManagement"
```

Since I use [KeePass, naturally this module](https://github.com/JustinGrote/SecretManagement.KeePass) comes in handy.

It's an awesome module that I highly recommend for any KeePass & PowerShell user.
I will use [SecretManagement](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.secretmanagement/?view=ps-modules) to enable the KeePass module and use it's vault capabilities.

This will save me tons of time and I trust the sources that the modules originate from, to deliver secure and tested code. Much more then I trust myself to think of all security aspects of a something that would replace the modules already offered. Another great benefit of having PowerShell as a gateway to your password manager is that you don't need to install the vendors application at all, this is a big plus if you're (like I am) a fan of minimalism.

Next part of the code would be to install the modules:
I set a condition to check if both modules are already present. If not, I try to install them.
Since the `Install-Module` cmdlets `Name` parameter accepts a string array (look for `String []` in the help files), I wont have to foreach loop through the modules.

```powershell
$ExistingModules = Get-Module -Name $Modules -ListAvailable | Select-Object -ExpandProperty Name -Unique

if ($ExistingModules.count -ne 2) {
    Install-Module $Modules -Repository PSGallery -Verbose
}
```

I then have a condition to check of the vault name, if it's not present already, I register the new KeePass vault, and sets it as the DefaultVault.

```powershell
if ( -not (Get-SecretVault -Name $KeePassDataBaseName -ErrorAction SilentlyContinue)) {
    Register-SecretVault -Name $KeePassDataBaseName -Verbose -ModuleName 'SecretManagement.KeePass' -DefaultVault -VaultParameters @{
        Path = $KeePassFilePath
        UseMasterPassword = $true
    }
    Write-Verbose "$KeePassDataBaseName successfully installed." -Verbose
}
else {
    Write-Verbose "$KeePassDataBaseName was already configured." -Verbose
}
```

### Function(s)

To speed things up even further, we want to create some smaller functions to wrap all the long cmdlets that we'd otherwise have to write, to get our secrets to the clipboard.

I say functions, because here's where you can enable the work we've done even further, to work with PSCredentialObjects or start a process, wait, and send the password directly too it, thus generating a sort of custom single sign-on solution. However, sticking to the subject, my function will:

1. Have a parameter that will be the secret that we're looking for in our password manager
2. Look for the secret, use `Get-SecretInfo` if the name is unknown
3. Call the GetNetworkCredential method, and accessing the 'Password' property of the NetworkCredential object, essentially converting the SecureString to a String, and setting the value to clipboard
4. Start a job with a ScriptBlock, which will replace the secret with a 'Cleared!' string.

```powershell
function Find-FSecret {
    param (
        [parameter(mandatory)]
        [string]$Secret
    )
    $SecretLookup = Get-Secret -Name $Secret
    if ($SecretLookup) {
        Set-Clipboard -Value $SecretLookup.GetNetworkCredential().Password
        Write-Verbose "Secret found and set to clipboard. Will auto clear in 20 seconds." -Verbose
        $null = Start-Job -ScriptBlock {
            Start-Sleep -Seconds 20
            Set-Clipboard -Value 'Cleared!' -Verbose
        }
    }
}
```

I then add this function to my [profile](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.3), which will load it on my users sessions, together with an alias declaration:
```powershell
if ( -not (Get-Alias ffs -ErrorAction SilentlyContinue)) {
  New-Alias -Name 'ffs' -Value 'Find-FSecret'
}
```
Every time I get somewhat annoyed by yet another "SIGN IN" page, I simply tab over to PowerShell and [vent out some frustration](https://www.dictionary.com/browse/ffs) using my function:
```powershell
ffs github
VERBOSE: Secret found and set to clipboard. Will auto clear in 20 seconds.
```

## Discussion

In my example, I'm using KeePass, however this is very applicable to other password managers, in fact the PowerShell Gallery has tons of SecretManagement modules can be just as simple to use as in my examples.

Some examples:

- BitWarden
- LastPass
- Keeper
- CyberArk
- Devolutions

Look yourself:
```powershell
Find-Module *SecretManagement*
```

Another mention is, you want to make sure you're not leaking your clipboard history. There's 3rd party applications & settings built into Windows that might do so.

There's also the possibility for a PowerShell Transcript to catch the output of your console, so make sure you never actually paste the credentials outside of the actual logon screen. You wouldn't want to screen-share, or share a server with someone who could look into your command-line history and find a password in clear text.

Speaking of which, you can regularly look for passwords in clear text super easily using [PSSecretScanner](https://github.com/bjompen/PSSecretScanner). I would recommend to look into it after completing a project like this.

Happy coding,

[Emil](https://github.com/ehmiiz)