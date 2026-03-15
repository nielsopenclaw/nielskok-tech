---
layout: post
title: "Exchange Online Migrations Tips & Tricks - NielsKok.Tech"
date: 2021-11-24
author: "Niels Kok"
categories: ["Exchange Online"]
tags: ["Exchange", "ExchangeOnline", "Microsoft365", "Office365"]
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/06/image-6.png"
description: "Exchange Online Migrations Powershell tips & trick about moving mailboxes via Powershell to Exchange Online. Also moving mailboxes in bulk in discussed"
original_url: "https://www.nielskok.tech/exchange-online/exchange-online-migrations-tips-tricks/"
---

# Exchange Online Migrations Tips & Tricks

November 24, 2021June 19, 2020 by [Niels Kok](<https://www.nielskok.tech/author/admin/> "View all posts by Niels Kok")

I have done a lot of Exchange Online Migrations over the years and I would like to share my tips & tricks in moving the mailboxes via Powershell. I am going to discus moving a single mailbox, moving mailboxes in bulk, offboarding mailboxes back to Exchange On-Premises. Furthermore I am going to show you how to collect information in CSV’s so you can move them easier.

## Tips & Tricks

Firstly, I want to say that I prefer move requests over migration batches. If you know you way around Powershell this gives you more control and the completion of mailboxes will take less time.

#### Single mailbox to Exchange Online

Let’s start with migrating a single user to Exchange Online.
    
    
    #first specify you on-prem credentials
    $ONPREMCREDS = Get-Credential
    
    #Then connect to Exchange Online
    Connect-ExchangeOnline
    
    #Then move the mailbox to the AutoSuspended state.
    New-MoveRequest -Identity "username@domain.com" -Remote -RemoteHostName "Your MRS Proxy Endpint" -TargetDeliveryDomain domainname.mail.onmicrosoft.com -RemoteCredential $ONPREMCREDS -BadItemLimit 1000 -SuspendWhenReadytoComplete

If you get the error that the command “Connect-ExchangeOnline” does not exist please install the MSOnline module with command:
    
    
    Install-Module MSOnline

This is the command where you get status of the move request:
    
    
    Get-MoveRequest | Get-MoveRequestStatistics

For example, this is what the status of a move request looks like:

![Exchange Online Migration Move Request status.](https://www.nielskok.tech/wp-content/uploads/2020/06/image-6.png)

To complete the mailboxes that have the “StatusDetail” of “AutoSuspended”, this is the status where mailbox is synced and is ready to be completed. 

![AutoSuspended move-request](https://www.nielskok.tech/wp-content/uploads/2020/06/image-7.png)

We run the following command because we stated earlier that the move-request needs to be “AutoSuspended”
    
    
    Get-MoveRequest | Where-Object Status -EQ AutoSuspended | Resume-Moverequest

To remove completed move-request please use the following command:
    
    
    Get-MoveRequest | Where-Object Status -eq completed | Remove-MoveRequest

#### Single mailbox to On-Premises

Secondly, let’s move a mailbox back on-premises:
    
    
    $ONPREMCREDS = Get-Credential
    
    Connect-ExchangeOnline
    
    Get-Mailbox -Identity "username@domain.com" | New-MoveRequest -OutBound -RemoteTargetDatabase "Databasename" -RemoteHostName "YOUR MRS Proxy endpoint" -RemoteCredential $ONPREMCREDS -TargetDeliveryDomain "domain.com"
    

You can use the same commands to check the status of the move request and to remove the move requests.

#### Bulk mailboxes to Exchange Online

The principle is the same for the single user move-request. The only code that has been added is a CSV import and a foreach loop.  
  
This is code:
    
    
    #first specify you on-prem credentials
    $ONPREMCREDS = Get-Credential
    
    #Then connect to Exchange Online
    Connect-ExchangeOnline
    
    #Collect the CSV info
    $MAILBOXLIST = Import-CSV "C:\Userlist.csv"
    
    #Create the move requests
    foreach ($line in $MAILBOXLIST) {
        New-MoveRequest -Identity $line.alias -Remote -RemoteHostName "Your MRS Proxy Endpoint" -TargetDeliveryDomain domainname.mail.onmicrosoft.com -RemoteCredential $ONPREMCREDS -BadItemLimit 1000 -SuspendWhenReadytoComplete
    
        Write-Host The moverequest for $line.alias is being created -ForegroundColor Green
    
        }

For example, this is output when you run this command:

![](https://www.nielskok.tech/wp-content/uploads/2020/06/image-8.png)

The move request are created 1 at a time, this means you can complete them one at a time or all together if you like.

#### Bulk mailboxes to On-Premises

This principle is the same for the bulk mailboxes to Exchange Online only the code is a little bit different.
    
    
    #first specify you on-prem credentials
    $ONPREMCREDS = Get-Credential
    
    #Then connect to Exchange Online
    Connect-ExchangeOnline
    
    #Collect the CSV info
    $MAILBOXLIST = Import-CSV "C:\Userlist.csv"
    
    #Create the move requests
    foreach ($line in $MAILBOXLIST) {
        Get-Mailbox -Identity $line.alias | New-MoveRequest -OutBound -RemoteTargetDatabase "Database Name" -RemoteHostName "Your MRS Proxy EndPoint" -RemoteCredential $ONPREMCREDS -TargetDeliveryDomain "domain.com"
    
        Write-Host The moverequest for $line.alias is being created -ForegroundColor Green
    
        }

#### Collect CSV’s

You can easily collect the information needed from the Exchange Admin Center.  
  
Go the mailboxes that you want to migrate and click on the elipsys:

![Exchange Admin Center](https://www.nielskok.tech/wp-content/uploads/2020/06/image-9.png)

After that choose the option to “Export to CSV”:

![](https://www.nielskok.tech/wp-content/uploads/2020/06/image-10.png)

Next choose to only export the Emailaddress:

![](https://www.nielskok.tech/wp-content/uploads/2020/06/image-12.png)

This is the result:

![](https://www.nielskok.tech/wp-content/uploads/2020/06/image-11.png)

## References

[Exchange Online Move Commands](<https://oddytee.wordpress.com/2015/02/13/moving-mailboxes-in-bulk-to-office-365/>)  
[Check if users have their mailboxes on-premises](<https://mikefrobbins.com/2018/08/02/determine-if-a-mailbox-is-on-premises-or-in-office-365-with-powershell/>)  
  
More info about Exchange Online:  
  
[Exchange Online Error HCW8108](<https://www.nielskok.tech/exchange-online/exchange-online-hybrid-connection-error-hcw8108/>)