---
title: "Deploy Internet Shortcut with custom icon via Intune using Win32App"
date: 2020-02-19
excerpt: "Deploy internet shorcut with custom icon using a powershell script which is packaged in Win32App. This is deployed via Microsoft Intune."
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/02/image-8.png"
categories:
  - "Intune"
  - "Microsoft 365"
tags:
  - "Microsoft365"
  - "Office365"
  - "powershell"
  - "SharePoint\"
  - "SharePointOnline"
---

When we as IT People deploy Windows 10 AutoPilot we often get the feedback from users that they miss their shortcuts for SharePoint and such. For that reason I have created a manual which you can use to deploy this using a Win32App. 

## Preparation

Create a folder where you store the files that you need to deploy. For example:  
  
I am deploying a shortcut for my SharePoint Online environment. To deploy this shortcut I need to following files:

![](https://www.nielskok.tech/wp-content/uploads/2020/02/image-8.png)

The install.cmd cointains the following code:
    
    
    if not exist "C:\ProgramData\AutoPilotConfig" md "C:\ProgramData\AutoPilotConfig"
    if not exist "C:\ProgramData\AutoPilotConfig\Icons" md "C:\ProgramData\AutoPilotConfig\Icons"
    xcopy "SharePointShortCut.ps1" "C:\ProgramData\AutoPilotConfig" /Y
    xcopy "SharePointicon.ico" "C:\ProgramData\AutoPilotConfig\Icons" /Y
    Powershell.exe -Executionpolicy bypass -File "C:\ProgramData\AutoPilotConfig\SharePointShortCut.ps1"

First we create 2 folders:  
– C:\ProgramData\AutoPilotConfig  
– C:\ProgramData\AutoPilotConfig\Icons  
  
I always create these folders to run and store installation/removal scripts. For example, when you have a custom application which requires a custom removal script this is where I store these scripts.  
  
After that we copy the 2 files to these folders and last but not least we run the Powershell script. This script contains this code:
    
    
    if (-not (Test-Path "C:\Users\Public\Desktop\SharePointOnline.url"))
    {
    $null = $WshShell = New-Object -comObject WScript.Shell
    $path = "C:\Users\Public\Desktop\SharePointOnline.url"
    $targetpath = "https://tenantname.sharepoint.com"
    $iconlocation = "C:\ProgramData\AutoPilotConfig\Icons\SharePointicon.ico"
    $iconfile = "IconFile=" + $iconlocation
    $Shortcut = $WshShell.CreateShortcut($path)
    $Shortcut.TargetPath = $targetpath
    $Shortcut.Save()
    
    Add-Content $path "HotKey=0"
    Add-Content $path "$iconfile"
    Add-Content $path "IconIndex=0"
    }

If you want to deploy another Internet Shortcut then SharePoint Online please edit these values:  
  
$path, is the value where the SharePoint is being stored.  
$targetpath, is the URL where you want to lead the Shortcut too.  
$iconlocation, is the value where the icon is stored.  
  
Change these values and put another iconfile in the folder that you are going to package.

## Packaging

We are going to use WinAppUtil. This application is needed to package applications in Microsoft Intune. You can download it here: [Link](<https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool>)  
  
Open Powershell, run the WinAppUtil.exe and use the following values:

![](https://www.nielskok.tech/wp-content/uploads/2020/02/image-9.png)

## Deployment

Log on to the Intune Portal at https://devicemanagement.microsoft.com/  
  
Go to Apps: 

![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-2.png)

After that go to Windows and add an App:

![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-3.png)

Add a Win32App:  


![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-4.png)

Select app package file and browse to the SharePointShortCut.intunewin file.  


![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-5.png)

Specify the package information:

![](https://www.nielskok.tech/wp-content/uploads/2020/02/image-11.png)

Specify the installation instructions:

![](https://www.nielskok.tech/wp-content/uploads/2020/02/image-12.png)

Install command: “Install.cmd”  
Uninstall command: del /f “C:\Users\Public\Desktop\SharePointOnline.url”  
Device restart behavior: “no specific action”

Specify the requirements:

![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-10.png)

Use the following detection rule:

![](https://www.nielskok.tech/wp-content/uploads/2020/02/image-13.png)

No depencies and no scope tags are assigned.  
  
Assign the groups that need the application: 

![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-12.png)

And we are done!  
  
If you have any questions don’t hesitate to ask.  
  
Regards,  
  
Niels
