---
title: "Set Calendar Permissions based on folder type"
date: 2020-01-16
excerpt: "Set calendar permissions based on folder type in Exchange Online and use an Azure Runbook to automate the whole thing. NielsKok.Tech"
categories:
  - "Microsoft 365"
  - "Powershell Automation"
tags:
  - "Automation"
  - "Calendars"
  - "ExchangeOnline"
  - "powershell"
---

If you have ever migrated an Exchange Server or have been an administrator of a large Exchange environment this may soundsfamiliar. When you want to change the default permissions of the calendar of a roommailbox this folder is often in multiple languages. This depends on multiple factors, examples are: “Calendar, Agenda & Kalender”. This often resulted in running a script multiples times to set the calendar permissions. In other words, for Dutch I needed to run the script for “Agenda”.   
  
The script on this page set the permissions for a calendar based on folder type. In this case it doesn’t matter in which language the folder was created. See the example below: 
    
    
    $AccessRights = Reviewer # Use Accordingly
    $USERS = Get-Mailbox -Resultsize Unlimited -RecipientTypeDetails roommailbox # Use Accordingly, if you want to only Room mailboxes swap "Usermailbox" for "Roommailbox"
    
    $USERS | ForEach-Object {
    
    $Calendar = $_.Alias + ":\" + (Get-MailboxFolderStatistics -Identity $_.Alias -FolderScope calendar | where-object {$_.FolderType -eq "Calendar"}).Name
    Write-host -ForegroundColor green "Set Calendar Permissions for $Calendar to $AccessRights"
    Set-MailboxFolderPermission -Identity $Calendar -User Default -AccessRights $AccessRights 
    
    }

Credits to my colleague Edwin Ligthart.  
  
Regards,  
  
Niels
