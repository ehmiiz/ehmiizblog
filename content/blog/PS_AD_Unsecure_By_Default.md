--- 
title: "PowerShell for Security: Continuous post of AD Weaknesses" 
date: 2022-10-23T08:23:11+01:00 
draft: false
comments: true

--- 
## Idea behind this post

As an Active Directory professional, I have gained insights into its unsecure features and outdated legacy “ideas,” as well as the growing list of vulnerabilities in the ADDS, ADCS & ADFS suite.

In this post, I will share my knowledge and experience in defending Active Directory with other AD admins. Each vulnerability section will be divided into three parts: Problem, Solution, and Script.

Please note that this post is personal and subject to change. Its sole purpose is to help others. Always exercise caution when running code from an untrusted source - read it carefully and test it in a lab environment before implementing it in production.

## 1. Clear-Text Passwords In Sysvol (KB2962486)

### Problem:
Group policies are (partly) stored in the domain wide share named Sysvol.
Sysvol is a share that every domain user has read access to. A feature of group policy preferences (GPP), is the ability to store credentials in a policy, thus making use of the permissions of said account in an effective way.

The only problem is that the credentials are encrypted using a AES key, that's publically avalible [here.](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-gppref/2c15cbf0-f086-4c74-8b70-1f2fa45dd4be?redirectedfrom=MSDN)
### Solution:
Patch your Domain Controllers so that admins cannot store credentials in sysvol: [MS14-025: Vulnerability in Group Policy Preferences could allow elevation of privilege](https://support.microsoft.com/en-us/topic/ms14-025-vulnerability-in-group-policy-preferences-could-allow-elevation-of-privilege-may-13-2014-60734e15-af79-26ca-ea53-8cd617073c30)

### Script:
This is a simple script that will match the cpassword row of the xml file, telling you what policy you need to fix:
```powershell
# Get domain
$DomainName = Get-ADDomain | Select-Object -ExpandProperty DNSRoot

# Build path
$DomainSYSVOLShareScan = "\\$domainname\SYSVOL\$domainname\Policies\"

# Check path recursivly for match
Get-ChildItem $DomainSYSVOLShareScan -Filter *.xml -Recurse | % {
    if (Select-String -Path $_.FullName -Pattern "Cpassword") {
        $_.FullName
    }
}
```

## 2. Authenticated Users Can Join Up to 10 Computers to the Domain ([KrbRelayUp](https://www.microsoft.com/security/blog/2022/05/25/detecting-and-preventing-privilege-escalation-attacks-leveraging-kerberos-relaying-krbrelayup/))

### Problem:

Active Directory creates an attribute by default in it's schema named: `ms-DS-MachineAccountQuota`. The value of this attribute determines how many computers a user in the Authenticated Users group can join to the domain.

However, this “trust by default” approach can pose a security risk, an attacker can leverage this attribute for privilege escalation attacks by adding new devices to the domain.

### Solution:

Find and identify the value of ms-DS-MachineAccountQuota.

[As Microsoft puts it](https://www.microsoft.com/en-us/security/blog/2022/05/25/detecting-and-preventing-privilege-escalation-attacks-leveraging-kerberos-relaying-krbrelayup/):

Organizations should also consider setting the ms-DS-MachineAccountQuota attribute to 0 to make it more difficult for an attacker to leverage the attribute for attacks. Setting the attribute to 0 stops non-admin users from adding new devices to the domain, blocking the most effective method to carry out the attack’s first step and forcing attackers to choose more complex methods to acquire a suitable resource.

### Script:

```powershell
$DomainDN = (Get-ADDomain).DistinguishedName

Get-ADObject -Identity $DomainDN -Properties ms-DS-MachineAccountQuota

Set-ADDomain -Identity $DomainDN -Replace @{"ms-DS-MachineAccountQuota"="0"}
```

I recommend running the script line-by-line, and try it out in a lab-environment first.

The script:
- Gets the DN of the domain
- Gets the ms-DS-MachineAccountQuota attribute
- Sets it to 0, making non-privileged users unable to domain join computers.

Talk this decision through with your security department, test plan execute.



### Happy hunting
