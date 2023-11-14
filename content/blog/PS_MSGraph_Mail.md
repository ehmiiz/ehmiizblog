--- 
title: "PowerShell Solution: Use Send-MgUserMail in Azure Automation"
date: 2023-11-06T08:23:11+01:00 
draft: false
comments: true

---

{{< figure
  src="/pics/NiceMail.png"
>}}

## Send-MgUserMail

The following solution example is covering how to set-up and use the `Send-MgUserMail` cmdlet in `Azure Automation` to send an email with a subject, message body and an attached zip file.


## Pre-Requirements

- [M365 licensed `noreply` address as a shared mailbox](https://learn.microsoft.com/microsoft-365/admin/email/create-a-shared-mailbox?view=o365-worldwide#create-a-shared-mailbox-and-add-members)
- Azure Automation account with the modules `Microsoft.Graph.Users.Actions` and `Microsoft.Graph.Authentication` installed
- App Registration with `Send.Mail` application API permission exposed


## Authentication & Access

This solution will use an `Client Secret` and a `encrypted automation variable`. 

The alternative to using an Client Secret would be to [use a certificate](https://learn.microsoft.com/entra/identity-platform/howto-create-self-signed-certificate) and I would recommend doing so since it's a more secure solution in general.

Using a `Client Secret` is fine if you have good control over who has access to your App Registration and your automation account. 

This step-by-step guide will set up the app registration and the secret, and finally add the secret to the automation accounts shared resources as a variable.

---
**NOTE**

If you're looking to be more fine-grained in your access delegation, and want to skip the whole secret management aspect, be sure to look into [Managed Identities](https://learn.microsoft.com/entra/identity/managed-identities-azure-resources/how-manage-user-assigned-managed-identities?pivots=identity-mi-methods-azp), specifically User-Assigned. Thanks [Dennis](https://www.linkedin.com/in/denkaj/)!

---

1. In the Azure Portal -> `App registrations`
2. `New Registration` -> Name the app to something descriptive like Runbook name or similar
3. `Register`
4. `API permissions` -> `Add permissions` -> `Microsoft Graph` -> `Application permission`
5. Search for `Mail.Send`, check it, `Add permissions`, `Grant admin consent for ORG`
6. Navigate to `Certificates & Secrets` -> `Client secrets` -> `new client secret`
7. Fill in `description` and `Expires` after your needs
8. Navigate to your automation account in Azure -> `Variables` -> `Add variable` -> Copy-paste your secret into this variable, select `Encrypted`, `Create`


The authentication will be done in the azure automation runbook, and finally the code will look similar to this:

```powershell
# Connects to graph as your new app using encrypted secret

# Look in your App Registration -> Application (client) ID
$ClientId = "o2jvskg2-[notreal]-1246-820s-2621786s35e5" 

# Look in Azure -> Microsoft Entra ID -> Overview -> Tenant ID
$TenantId = "626226122-[notreal]-62ww-5053-56e32ss89sa5"

# Variable Name from step 8 (Authentication)
$ClientSecretCredential = (Get-AutomationVariable -Name 'From Step 8')

$Body = @{
    Grant_Type    = "client_credentials"
    Scope         = "https://graph.microsoft.com/.default"
    Client_Id     = $ClientId
    Client_Secret = $ClientSecretCredential
}

$RestMethodParams = @{
    Uri = "https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token"
    Method = "POST"
    Body = $Body
}

$Connection = Invoke-RestMethod @RestMethodParams
$Token = $Connection.access_token

Connect-MgGraph -AccessToken $Token
```
**Note** that `Get-AutomationVariable` is a cmdlet which is only available for the az automation sandbox environment. It's also the only way of getting the encrypted variable.

`Get-AutomationVariable` is an internal cmdlet from the module `Orchestrator.AssetManagement.Cmdlets` which is a part of Azure Automation, so running this outside of a runbook will fail.



## Sending the mail

Now that we have authentication and access out of the way, we can start developing a function that we will use in the runbook to send an email.
My example below has a requirement of an attachment. I'm using this for gathering data, compressing it and attaching the .zip file in the mail function.

Customize the function to your specific needs.

```powershell
function Send-AutomatedEmail {
    param(
        [Parameter (Mandatory = $false)]
        [string]$From,
        [Parameter (Mandatory = $true)]
        [string]$Subject,
        [Parameter (Mandatory = $true)]
        $To,
        [Parameter (Mandatory = $true)]
        [string]$Body,
        [Parameter (Mandatory = $true)]
        [string]$AttachmentPath
    )

    if ([string]::IsNullOrEmpty( $From )) {
        $From = "noreply@contoso.com"
    }

    # I'm defining the parameters in a hashtable 
    $ParamTable = @{
        Subject = $Subject
        From    = $From
        To      = $To
        Type    = "html"
        Body    = $body
    }

    # ArrayList instead of adding to an array with += for increased performance
    $ToRecipients = [System.Collections.ArrayList]::new()
    
    $ParamTable.To | ForEach-Object {
        [void]$ToRecipients.Add(@{
                emailAddress = @{
                    address = $_
                }
            })
    }

    try {
        $MessageAttachment = [Convert]::ToBase64String([IO.File]::ReadAllBytes($AttachmentPath))
        $MessageAttachmentName = $AttachmentPath.Split("\") | Select-Object -Last 1
    }
    catch {
        Write-Error $Error[0] -ErrorAction Stop
    }

    $params = @{
        Message         = @{
            Subject      = $ParamTable.Subject
            Body         = @{
                ContentType = $ParamTable.Type
                Content     = $ParamTable.Body
            }
            ToRecipients = $ToRecipients
            Attachments  = @(
                @{
                    "@odata.type" = "#microsoft.graph.fileAttachment"
                    Name          = $MessageAttachmentName
                    ContentBytes  = $MessageAttachment
                }
            )

        }
        SaveToSentItems = "false"
    }

    try {
        Send-MgUserMail -UserId $ParamTable.From -BodyParameter $params -ErrorAction Stop
        Write-Output "Email sent to:"
        $ParamTable.To
    }
    catch {
        Write-Error $Error[0]
    }
}
```

Finally, we construct a new [splatting table](https://learn.microsoft.com/powershell/module/microsoft.powershell.core/about/about_splatting?view=powershell-7.3) and send the email. A note, for this to run authentication must have happened earlier in the runbook.

```powershell
# Generate some data and compress it
$Date = Get-Date -Format yyyy-MM-dd
$CSVPath = "$env:temp\$($Date)-BigReport.csv"
$ZIPPath = "$env:temp\$($Date)-BigReport.zip"

$BigReport | Sort-Object | Export-Csv -Path $CSVPath -NoTypeInformation -Encoding UTF8

Compress-Archive -Path $CSVPath -DestinationPath $ZipPath


# Build the email parameters
$SendMailSplat = @{
    Subject        = "Automated Email via MGGraph"
    Body           = "This is an automated email sent from Azure Automation using MGGraph."
    To             = "user1@mail.com", "user2@mail.com","user3@mail.com"
    AttachmentPath = $ZIPPath
}

# Send the email
Send-AutomatedEmail @SendMailSplat
```

And that's all there is to it! Congrats on sending an email using the Microsoft Graph.

## Key Takeaways

While building this solution, I noticed that there's a lack of content and documentation on some things, one of those things are how to send an email to more than one recipient. If your migration from [Send-MailMessage](https://learn.microsoft.com/powershell/module/microsoft.powershell.utility/send-mailmessage?view=powershell-7.3), it isn't so straightforward, since `Send-MgUserMail` is based on either JSON or MIME format.

Meaning in a nutshell we can't just pass an array of email accounts and call it a day, instead we need to build an object that looks like something along the lines of:
Message -> ToRecipients -> emailAddress -> adress : adress.company.com

### Alternative 1 (fast)

```powershell
$ToRecipients = [System.Collections.ArrayList]::new()

$ParamTable.To | ForEach-Object {
    [void]$ToRecipients.Add(@{
            emailAddress = @{
                address = $_
            }
        })
}
```

### Alternative 2 (slow)

```powershell
$ToRecipients = @()
$ParamTable.To | ForEach-Object { $ToRecipients += @{
        emailAddress = @{
            address = $_ 
        }
    }
}
```
Use whatever fits your needs best.

Hope this was valuable to someone wanting to move away from `Send-MailMessage` to `Send-MgUserMail`!

### Happy coding
### /Emil