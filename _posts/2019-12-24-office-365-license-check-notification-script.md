---
title: "Office 365 License check + notification script"
date: 2019-12-24
excerpt: "Use a Powershell script to check your Office 365 licensea and get a notification when you run out of licenses. Powershell Automation"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2019/12/PowershellGetAccountSku.png"
categories:
  - "Microsoft 365"
  - "Powershell Automation"
tags:
  - "Automation"
  - "EMSE3"
  - "Licences"
  - "Microsoft365"
  - "Notification"
---

I think we all have had a moment where we wanted to create a new user account but the licenses for Office 365 had run out. Our Sysadmins asked me to write a script which would send an e-mail if the licenses almost would run out.  
  
I would run the script from a management server which is able to send e-mail via an Exchange server or similar relay server. Furthermore the user that runs the script doesn’t need admin permissions in Office 365. You can just create a regular user account to run the script. To check whether your user account has the right permissions do the following.   
  
Log on to Microsoft 365 Powershell via the following command: 
    
    
    Connect-MsolService

Then run the following command:
    
    
    Get-MsolAccountSku

You will see something like:

![](https://www.nielskok.tech/wp-content/uploads/2019/12/PowershellGetAccountSku.png)

You will probably see more output because you have more licenses activated. Everything after “:”, in the example “SPE_E5”, is the official microsoft licensename. For Office 365 E3 it’s tenantname:ENTERPRISEPACK.  
  
In the following example the licenses for Office 365 E3 and EMS E3 are checked and if there are less then 3 available an e-mail will be send. Check it out: 
    
    
    $User = "Username" 
    $PWord = "EncryptedPassword" #How To create EncryptedPassword "EnterPassword" | ConvertTo-SecureString -AsPlainText -Force | ConvertFrom-SecureString
    $secPword = ConvertTo-SecureString $Pword 
    $Credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecPWord
    
    Connect-MsolService -Credential $Credential
    
    $Office365E3Licenses = Get-MsolAccountSku | Where-Object {$_.AccountSkuId -eq "tenantname:ENTERPRISEPACK"}
    $Office365E3LicensesAvailable = $Office365E3Licenses.ActiveUnits - $Office365E3Licenses.ConsumedUnits
    
    $EMSE3Licenses = Get-MsolAccountSku | Where-Object {$_.AccountSkuId -eq "tenantname:EMS"}
    $EMSE3LicensesAvailable = $EMSE3Licenses.ActiveUnits - $EMSE3Licenses.ConsumedUnits
    
    if ($Office365E3LicensesAvailable -lt 3) {
       Send-MailMessage -SmtpServer "SMTPServer" -To "E-mailaddress" -From "E-mailaddress" -Subject "Office 365 E3 Licenses almost run out!" -Body "There are $Office365E3LicensesAvailable Office 365 E3 licenses available" -BodyAsHtml 
    }
    
    if ($EMSE3LicensesAvailable -lt 3) {
        Send-MailMessage -SmtpServer "SMTPServer" -To "E-mailaddress" -From "E-mailaddress" -Subject "EMS E3 Licenses almost run out!" -Body "There are $EMSE3LicensesAvailable EMS E3 licenses available" -BodyAsHtml 
    }
    

If you have any questions don’t hesitate to ask.  
  
Regards  
  
Niels
