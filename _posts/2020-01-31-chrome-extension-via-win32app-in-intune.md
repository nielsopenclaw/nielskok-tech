---
title: "Chrome Extension via Win32App in Intune"
date: 2020-01-31
excerpt: "Chrome Extension via Win32App packaging a Powershell. Use a custom detection script to check the deployment. NielsKok.Tech"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/01/image.png"
categories:
  - "Intune"
  - "Microsoft 365"
  - "Powershell Automation"
tags:
  - "ChromeExtension"
  - "INtune"
  - "MEM"
  - "Microsoft365"
  - "Office365"
---

If you use Azure AD as identity provider for your webbased third party applications, you might want to consider use the Google Chrome extension called “Windows 10 Accounts”. This extension provide the complete single sign on experience for applications that use Azure Activery Directory based identities.   
  
I will provide an instruction on how to deploy this extension via Intune for Windows 10 devices.   
  
For example, the Windows 10 accounts extension looks like this: 

![](https://www.nielskok.tech/wp-content/uploads/2020/01/image.png)

## Prerequisites 

  1. WinAppUtil, this application is needed to package applications in Microsoft Intune. You can download it here: [Link](<https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool>)
  2. Appropriate licenses to deploy a Windows 10 device via Intune.
  3. Windows 10 Intune enrolled device.
  4. Google Chrome installed


## Preparation

Let’s start with the deployment. First of all start by creating a folder. For example: “C:\temp\ChromeAddOnWindows10Accounts”  
  
Create 2 files in this directory: 

![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-7.png)

The first file (ChromeAddOnWindows10Accounts.ps1) contains a powershell script, this powershell creates a registry key with forces Google Chrome to install the extension. [Link to original creator of the script](<https://www.petervanderwoude.nl/post/simply-installing-the-windows-10-accounts-extension-for-google-chrome-by-using-powershell/>)
    
    
    #Set variables as input for the script
    $KeyPath = "HKLM:\Software\Policies\Google\Chrome\ExtensionInstallForcelist"
    $KeyName = "1"
    $KeyType = "String"
    $KeyValue = "ppnbnpeolgkicgegkbkbjmhlideopiji;https://clients2.google.com/service/update2/crx"
    
    #Verify if the registry path already exists
    if(!(Test-Path $KeyPath)) {
        try {
            #Create registry path
            New-Item -Path $KeyPath -ItemType RegistryKey -Force -ErrorAction Stop
        }
        catch {
            Write-Output "FAILED to create the registry path"
        }
    }
    
    #Verify if the registry key already exists
    if(!((Get-ItemProperty $KeyPath).$KeyName)) {
        try {
            #Create registry key 
            New-ItemProperty -Path $KeyPath -Name $KeyName -PropertyType $KeyType -Value $KeyValue
        }
        catch {
            Write-Output "FAILED to create the registry key"
        }
    }

You can use this script to implement every Chrome extension. Replace the $KeyValue with the value needed for your extension. and replace the $KeyName with a number other than 1. Otherwise you will overwrite this value.  
  
The second file (install.cmd) calls the powershell script, you will use this in the Intune configuration. The file contains the following code: 
    
    
    Powershell.exe -Executionpolicy bypass -File ChromeAddOnWindows10Accounts.ps1

## Packaging

Now it’s time to create the package. Start Powershell and run the WinAppUtil, for me the location is “C:\temp\Intune\IntuneWinAppUtil.exe”.   
  
Use the paths in the screen below accordingly and hit enter: 

![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-1.png)

A ChromeAddOnWindows10Accounts.intunewin is created in the folder “C:\temp\Output”. This file will used in the deployment via Intune.

## Deployment

Log on to the Intune Portal at https://devicemanagement.microsoft.com/  
  
Go to Apps: 

![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-2.png)

After that go to Windows and add an App:  


![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-3.png)

Add a Win32App:  


![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-4.png)

Select app package file and browse to the ChromeAddOnWindows10Accounts.intunewin file.  


![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-5.png)

Specify the package information:  


![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-6.png)

Specify the installation instructions:  


Install command: “Install.cmd”  
Uninstall command: “reg delete HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\ExtensionInstallForcelist /v “1” /f “  
Device restart behavior: “no specific action”

![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-8.png)

Specify the requirements:  


![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-10.png)

I have created a custom detection script, these are the settings in Intune:  


![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-11.png)

This is the code of the detection script:  

    
    
    #ChromeAddOnDetectionScript
    $KeyPath = "HKLM:\Software\Policies\Google\Chrome\ExtensionInstallForcelist"
    $KeyName = "1"
    $KeyType = "String"
    $KeyValue = "ppnbnpeolgkicgegkbkbjmhlideopiji;https://clients2.google.com/service/update2/crx"
    
    (Get-ItemProperty -Path $KeyPath -Name $KeyName).1 -eq $KeyValue

No depencies and no scope tags are assigned.  
  
Assign the groups that need the application: 

![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-12.png)

The detection rules show installed:  


![](https://www.nielskok.tech/wp-content/uploads/2020/01/image-13.png)

And it’s a wrap! If you have any questions feel free to contact me.   
  
Regards,   
  
Niels

Niels
