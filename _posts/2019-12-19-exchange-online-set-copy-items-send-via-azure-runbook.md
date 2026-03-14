---
title: "Exchange Online Set Copy Items Send via Azure Runbook"
date: 2019-12-19
excerpt: "Use a Powershell scropt to set Copy Send Items for Exchange Online via an Azure Runbook. Powershell Automation NielsKok.Tech"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2019/12/CopySentItems.png"
categories:
  - "Microsoft 365"
  - "Powershell Automation"
tags:
  - "Automation"
  - "Azure"
  - "Microsoft365"
  - "Office365"
  - "powershell"
---

This blog is about automating setting “Copy items sent” and “Copy items sent on behalf ” for Shared Mailboxes in Exchange Online. If Shared Mailboxes are created manually, It could be that these are settings are forgotten. If you forgot to set these settings it could have a high impact on users.  
  
I am talking about these 2 settings: 

![](https://www.nielskok.tech/wp-content/uploads/2019/12/CopySentItems.png)

The first thing you need to is create an Azure Automation Account. Go to “https://portal.azure.com/”, log in and search for Automation Account:

![](https://www.nielskok.tech/wp-content/uploads/2019/12/image.png)

Click on Add, create a new resource group and choose the correct Geo location.   
  
Then add a credential to the Automation Account. This must be an Exchange Online Administrator.

![](https://www.nielskok.tech/wp-content/uploads/2019/12/image-1.png)

It’s time to add the Powershell script as a Runbook. Go to Runbooks and click on Create a runbook:

![](https://www.nielskok.tech/wp-content/uploads/2019/12/image-2.png)

Pick a Name and choose powershell as Runbooktype:

![](https://www.nielskok.tech/wp-content/uploads/2019/12/image-3.png)

After that you can add the script to the runbook. This is the code:
    
    
    #Gather Credentials From Azure Automation Account
    $Credentials = Get-AutomationPSCredential -Name 'Niels WVD'
    $Session = New-PSSession –ConfigurationName Microsoft.Exchange -ConnectionUri https://outlook.office365.com/powershell-liveid -Credential $Credentials -Authentication Basic -AllowRedirection
     
    Import-PSSession -Session $Session -DisableNameChecking:$true -AllowClobber:$true | Out-Null
    
    #Populate Shared Mailboxes to be editted
    $users = Get-Mailbox -RecipientTypeDetails Shared
    
    foreach ($user in $users) {
    
    set-mailbox $user.alias -MessageCopyForSentAsEnabled $True
    set-mailbox $user.alias -MessageCopyForSendOnBehalfEnabled $True
    
    }

In addition, please fill in your automation credentials name you setup earlier.  
  
After that you can go to schedules to plan the script every night. 

![](https://www.nielskok.tech/wp-content/uploads/2019/12/image-4.png)

If you have any questions about the runbook or want to schedule another script feel free to contact me.  
  
Regards,  
  
Niels
