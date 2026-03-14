---
title: "Enable Azure AD Device Script"
date: 2020-03-27
excerpt: "Automatically enabled Azure AD Device that you have uploaded via a CSV using a Powershell script. Put this powershell script in an automation account"
featured_image: "https://www.anoopcnair.com/wp-content/uploads/2019/09/Whiteglove-no-Net-error-1024x662.jpeg"
categories:
  - "Azure"
  - "Intune"
  - "Powershell Automation"
tags:
  - "Automation"
  - "AzureAD"
  - "INtune"
  - "MEM"
  - "Microsoft365"
---

I have been testing with Windows 10 AutoPilot White Glove and I have run in to the problem where the Azure AD Device was not able to find my organisation. 

## Problem definition

The error looked like this:

![Windows Autopilot WhiteGlove Provisioning Backend Process #4](https://www.anoopcnair.com/wp-content/uploads/2019/09/Whiteglove-no-Net-error-1024x662.jpeg)

I had uploaded the hardware hashes to Intune using a CSV. Therefore, I was very suprised to see this error. The device was visible in my tenant. Furthermore everything looked good. Then I took a closer look. I Saw that the device was not enabled:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-36.png)

After that I enabled the device. I rebooted the device and it was able to find the organization:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-37.png)

The WhiteGlove deployment worked perfectly! 🙂 Then I thought that I didn’t want to enable every device by hand. Let’s create a script! After that I thought let’s share this little piece of knowledge.

## Script + Explanation

This is the syntax of the script:
    
    
    #check if module exists and install if it does not exist
    if (Get-Module -ListAvailable -Name AzureAD) {
        Write-Host "The Module AzureAD Already exists"
    } 
    else {
        Install-Module AzureAD
        Write-Host "The module AzureAD is being installed"
    }
    
    #Connect to Azure AD
    Connect-AzureAD
    
    #Populate all devices
    $Devices =  Get-AzureADDevice
    
    #Enable Devices that are not Enabled
    Foreach ($AzureADDevice in $Devices)
    {
        if ($AzureADDevice.AccountEnabled -eq $False) {
            try {
                Set-AzureADDevice -Objectid $AzureADDevice.Objectid -AccountEnabled $true
                Write-Host Device $AzureADDevice.DisplayName enabled -ForegroundColor Green
            }
            catch {
                Write-Host Device $AzureADDevice.DisplayName could not be enabled -ForegroundColor Red
            }
    }
    }

Usage of the script:  
  
When you run the script first the script checks whether you have the AzureAD module installed. If it doesn’t exist it will be installed:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-38.png)

Then you need to log on:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-39.png)

After that the devices will be populated and the one’s that are not enabled will be enabled:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-40.png)

The devices will be displayed with their serial number (if they are not enrolled before).   
  
That way you can automatically enabled you devices. It also possible to put this an automation run book if you have frequent updates with devices.
