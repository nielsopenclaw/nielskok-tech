---
title: "AutoPilot info auto upload script"
date: 2020-04-10
excerpt: "Place this script on a USB Drive to automatically upload Windows 10 Autopilot device information to your Microsoft Intune tenant."
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/04/image-22.png"
categories:
  - "Intune"
  - "Microsoft 365"
  - "Powershell Automation"
tags:
  - "Autopilotinfo"
  - "INtune"
  - "MEM"
  - "MIcrosoft Office"
  - "Microsoft365"
---

I have created a script which auto uploads your Windows 10 AutoPilot info to your tenant. I didn’t do the hard work, that is someone called [Nickolaj Andersen](<https://www.scconfigmgr.com/author/najtmaregmail-com/>). He wrote the script that you can download from the Powershell Galery. This is his original post:  
  
<https://www.scconfigmgr.com/2019/06/04/import-windows-autopilot-device-identity-using-powershell/>

## The Script

It is a simple script, it is just a couple of lines. This is the syntax of the script:
    
    
    # Check if AutoPilotScript is installed
    $InstalledScripts = Get-InstalledScript
    If ($InstalledScripts.name -notcontains "Upload-WindowsAutopilotDeviceInfo") {
        Install-Script -Name Upload-WindowsAutopilotDeviceInfo -force
    }
    
    # collect Windows Autopilot info and Upload it to Azure
    Upload-WindowsAutopilotDeviceInfo.ps1 -TenantName "tenantname.onmicrosoft.com" -Verbose

Firstly, the script will check if the script that uploads the data is installed. If is not it will be installed. That is done by this part:
    
    
    # Check if AutoPilotScript is installed
    $InstalledScripts = Get-InstalledScript
    If ($InstalledScripts.name -notcontains "Upload-WindowsAutopilotDeviceInfo") {
        Install-Script -Name Upload-WindowsAutopilotDeviceInfo -force
    }

After that the script that has just been installed will be called upon by this line:
    
    
    Upload-WindowsAutopilotDeviceInfo.ps1 -TenantName "tenantname.onmicrosoft.com" -Verbose

## Example

I will show you an example for the script. I created a simple Windows 10 Hyper V machine for the script to run on.  
  
I copy the script to the machine itself but in a real life situation you should put it on an USB drive and run it from there. Is this is a machine will hasn’t been enrolled and is still in the OOBE state you should use the key combination: “Shift” + “F10”. This opens a command prompt, after that run the Powershell script from there.  
  
In addition, all actions above require you to have an internet connection. 🙂  
  
I have place the script in my temp folder:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-22.png)

Run CMD as Admin, otherwise you are not able to install scripts onto your system. Run the following command (Change the File parameter accordingly) :
    
    
    powershell -ExecutionPolicy Bypass -File C:\Temp\AutoPilotupload.ps1

NuGet needs to be installed, enter Y:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-23.png)

The script now installs a couple of modules to be able to connect to Azure AD. After that it prompts for the credentials to connect to Azure AD:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-24.png)

The data is being uploaded:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-25.png)

And now the Windows 10 device is uploaded in to your tenant:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-26.png)
