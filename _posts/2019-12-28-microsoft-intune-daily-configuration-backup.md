---
title: "Microsoft Intune Daily Configuration Backup"
date: 2019-12-28
excerpt: "Create a daily backup from your intune configuration using a Powershell script and a scheduled task. Microsoft 365 NielsKok.Tech"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2019/12/FirstTimeConnectGraph-1.png"
categories:
  - "Intune"
  - "Microsoft 365"
  - "Powershell Automation"
tags:
  - "Backup"
  - "Graph"
  - "GraphAPI"
  - "INtune"
  - "Microsoft365"
---

This blog is about creating a backup for your Intune Configuration with a 90 day retention. This way you could go back in time if you have a lot of configuration changes. Sometimes there are a lot of Sysadmins making changes in the configuration without proper documentation. This way you can just look at the configuration of the day before.  
  
Let start with some preparations. You need to following Powershell modules: 
    
    
    Install-Module -Name MSGraphFunctions
    Install-Module -Name IntuneBackupAndRestore
    Install-Module -Name AzureAD

If you never have connected to Graph in Powershell you need to grant consent. You can do so by Connecting to Graph via this command:
    
    
    Connect-Graph

Fill in your credentials and you will get the following prompt:

![](https://www.nielskok.tech/wp-content/uploads/2019/12/FirstTimeConnectGraph-1.png)

Please click on Accept.  
  
Now you are ready to implement the script that will backup your Microsoft Intune Configuration. I would schedule this on a management server. This is the script: 
    
    
    #Populate Variables
    $FilePath = "C:\BackupFilePath" #Use Accordingly
    $User = "UserName" #Use Accordingly
    $PWord = "EncryptedPassword" #How To create EncryptedPassword "EnterPassword" | ConvertTo-SecureString -AsPlainText -Force | ConvertFrom-SecureString
    $secPword = ConvertTo-SecureString $Pword   
    $Credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecPWord
    
    #Create New Folder According to date
    $BackupFolder = New-Item -ItemType Directory -Path "C:\BackupFilePath\$((Get-Date).ToString('yyyy-MM-dd'))"
    
    #Connect to Graph API to Backup Intune
    Connect-Graph -credential $Credential
    
    #Start Intune Backup
    Start-IntuneBackup -Path $BackupFolder
    
    #Clean Up Old Intune Backups
    get-childitem -Path $FilePath | where-object {$_.LastWriteTime -lt (get-date).AddDays(-90)} |Remove-Item -Force

The backup folder will look like this:

![](https://www.nielskok.tech/wp-content/uploads/2019/12/BackupFilePath.png)

The total storage needed for 1 day is about 3 – 4 MB. So the 90 day retention will only take about 270 – 360 MB in total.  
  
If you want to compare some backups from a couple of days backup you can use the CMDlet: 
    
    
    Compare-IntuneBackupFile -ReferenceFilePath "Folder for Reference, for example(2019-12-27)" -DifferenceFilePath "Folder to Differentiate(2019-12-28)"

And if you then want to restore the Intune Backup you can use this CMDlet:
    
    
    Start-IntuneRestoreConfig -Path "For Example(2019-12-25)"

I will update this sometime to run it from an Azure Automation account to backup this to Azure Storage Blob. Maybe someone could help me with that.  
  
Thanks and kind regards,   
  
Niels
