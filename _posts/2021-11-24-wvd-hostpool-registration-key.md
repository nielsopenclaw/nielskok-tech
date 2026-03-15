---
layout: post
title: "WVD HostPool Registration Key - NielsKok.Tech Azure Virtual Desktop"
date: 2021-11-24
author: "Niels Kok"
categories: ["Azure Virtual Desktop"]
tags: ["Hostpool", "Registration", "RegistrationKey", "Windows10", "WVD"]
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/02/image-2.png"
description: "This blogpost is about getting a Windows Virtual Desktop (WVD) hostpool registration key. Azure Virtual Desktop NielsKok.Tech"
original_url: "https://www.nielskok.tech/azure-virtual-desktop/wvd-hostpool-registration-key/"
---

![](https://www.nielskok.tech/wp-content/uploads/2021/02/image-2.png)

# WVD HostPool Registration Key

November 24, 2021February 10, 2021 by [Niels Kok](<https://www.nielskok.tech/author/admin/> "View all posts by Niels Kok")

This blogpost is about getting a Windows Virtual Desktop (WVD) hostpool registration key. This registration key is needed to register new WVD Session Hosts to your hostpool.

I am using 2 methods to get the key. The portal and via Powershell. The portal is the easy way to get the key. For example, you can use this for testing purposes.

#### Prerequisites

You already have a Windows Virtual Desktop Hostpool.

#### Get Registration key out of the portal

Firstly, log on the [Azure Portal](<https://portal.azure.com/>)  
  
Next, go to your WVD hostpool in the portal. For example:

![Windows Virtual Desktop Hostpool registration key](https://www.nielskok.tech/wp-content/uploads/2021/02/image.png)

Lastly, when you clicked on the hostpool, you can find the key here:

![Registration key button to generate a key](https://www.nielskok.tech/wp-content/uploads/2021/02/image-1.png)

After you click the button, the generate option appears:

![](https://www.nielskok.tech/wp-content/uploads/2021/02/image-2.png)

#### Get Registration key via Powershell

And ofcourse, this is my favorite. It is now time to generate the key via Powershell. You can automate this process.  
  
Firstly, install these modules:
    
    
    $installedPackageProvider = Get-PackageProvider
    if ($installedPackageProvider.Name -notmatch "NuGet") {
        Install-PackageProvider -Name NuGet -force
         Write-Host("Install powershell module NuGet")
    }
    $installedModules = Get-InstalledModule
    if ($installedModules.Name -notmatch "Az.Accounts") {
        Install-Module Az.Accounts -Force -AllowClobber
         Write-Host("Install powershell module Az Accounts")
    }
    if ($installedModules.Name -notmatch "Az.Resources") {
        Install-Module Az.Resources -Force -AllowClobber
         Write-Host("Install powershell module Az Resources")
    }
    if ($installedModules.Name -notmatch "Az.DesktopVirtualization") {
        Install-Module Az.DesktopVirtualization -Force -AllowClobber
         Write-Host("Install powershell module Az DesktopVirtualization")
    }

After that, log on to Microsoft Azure:
    
    
    Connect-AzAccount

In addition, select the proper subscription if you have multiple:
    
    
    Select-AzSubscription -SubscriptionId "SUBSCRIPTION ID" | Set-AzContext

Create the token via the following code. This creates a token which is valid for the next 14 days. The maximum life time of a token is 30 days. Use this accordingly
    
    
    $resourceGroupName = "RESOURCEGROUP NAME OF HOSTPOOL"
    $Hostpool = "HOSTPOOL NAME"
    $SubsciptionID = "SUBSCRIPTION ID"
        
    $GetToken = New-AzWvdRegistrationInfo -SubscriptionId $SubsciptionID -ResourceGroupName $resourceGroupName -HostPoolName $Hostpool -ExpirationTime (Get-Date).AddDays(14) -ErrorAction SilentlyContinue

A new token is created, this is the output $GetToken:

![](https://www.nielskok.tech/wp-content/uploads/2021/02/image-3.png)

If you are forwarding this variable to another piece of code make sure that you only forward the token. Use this to forward only the token:
    
    
    $GetToken.Token

That way you can register new sessionhosts to your hostpool using Powershell:

![](https://www.nielskok.tech/wp-content/uploads/2021/02/image-4.png)

#### References

[Microsoft Doc about registration key](<https://docs.microsoft.com/en-us/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo?view=azps-5.5.0>)  
  
Other Posts:  
[Snapshot and upload a managed disk](<https://www.nielskok.tech/azure/snapshot-managed-disk-to-storage-account/>)  
[RDP ShortPath for WVD](<https://www.nielskok.tech/windows-virtual-desktop/rdp-shortpath-for-wvd/>)