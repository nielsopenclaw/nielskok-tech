---
layout: post
title: "Connect to Exchange Online script with modern authentication"
date: 2021-11-24
author: "Niels Kok"
categories: ["Exchange Online"]
tags: ["Encryption", "Exchange", "ExchangeOnline", "Microsoft365", "Office365"]
description: "Use this script to connect to Exchange Online via the new commandlets for modern authentication. You will need the new module called MSOnline"
original_url: "https://www.nielskok.tech/exchange-online/connect-to-exchange-online-script-with-modern-authentication/"
---

November 24, 2021June 25, 2020 by [Niels Kok](<https://www.nielskok.tech/author/admin/> "View all posts by Niels Kok")

In october this year basic authentication for Exchange Online will no longer be available. That is why I’ve written a blog which explain how to use a script with encrypted credentials to connect to Exchange Online via modern authentication.

## Prerequisites

You need to have the module “ExchangeOnlineManagement” installed. You can do so by running the following code in an elevated Powershell window:
    
    
    Install-Module –Name ExchangeOnlineManagement

## Exchange Online Script syntax

This is the syntaxt of the script to automatically connect to Exchange Online and export all the roommailboxes to CSV:
    
    
    ##### Exchange Connection
    $Username = "Username@domain.com"
    $PasswordString = "01000000d-EXAMPLE STRING - 7a00c04fc297eb01000000dd82bb8d17c57e41b861885d18c6e3c9000000000222e31845bde6e99c2d8a3bf1c50000000004800000a000000010000000e2d12fa62412fe47aa5dd44a38c8b6df180000007c43b18a97e45bacbd1554afed19ab5b8656c807dbc56a57140000005e84843d1adc78af2a9731981de9ef972dace6f9"
    $SecPasswd = ConvertTo-SecureString $PasswordString;
    $Credentials = New-Object System.Management.Automation.PSCredential ($Username, $SecPasswd)
    
    Connect-ExchangeOnline -Credential $Credentials
    
    #Collect and Export Roommailboxes
    
    Get-EXOMailbox -RecipientTypeDetails RoomMailbox | Select-Object "name","PrimarySmtpAddress","RecipientTypeDetails" | ConvertTo-Csv -NoTypeInformation -Delimiter ";"| Out-File "C:\Filepath\RoommailboxExport.csv"

If you don’t know how to create an encrypted password you can follow this link: [Create Encrypted Passwords](<https://www.altaro.com/msp-dojo/encrypt-password-powershell/>)

## References

[Microsoft Doc about the new command](<https://docs.microsoft.com/en-us/powershell/module/exchange/connect-exchangeonline?view=exchange-ps>)  
  
[Read about my Exchange Online powershell migration tips](<https://www.nielskok.tech/exchange-online/exchange-online-migrations-tips-tricks/>)