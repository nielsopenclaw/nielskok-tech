---
layout: post
title: "Powershell Function for Creating WVD Hostpools - NielsKok.Tech"
date: 2021-11-24
author: "Niels Kok"
categories: ["PowerShell Automation"]
tags: ["Automation", "Hostpools", "powershell", "WindowsVirtualDesktop", "WVD"]
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/03/image-32.png"
description: "I have written a Powershell Function which I use for Creating WVD Hostpools Powershell Automation NielsKok.Tech"
original_url: "https://www.nielskok.tech/powershellautomation/powershell-function-for-creating-wvd-hostpools/"
---

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-32.png)

# Powershell Function for Creating WVD Hostpools

November 24, 2021March 23, 2021 by [Niels Kok](<https://www.nielskok.tech/author/admin/> "View all posts by Niels Kok")

I have written a Powershell Function which I use for Creating WVD Hostpools. Instead of clicking through the portal you now can create multiple hostpools by running this function.

#### Prerequisites

  * An Azure subscription
  * The Azure subscription needs to be [prepared](<https://docs.microsoft.com/en-us/windows/deployment/windows-10-subscription-activation>) for WVD deployment
  * Install Powershell Modules (This is listed in the blog)


#### Powershell Function

This is the syntaxis for the script:
    
    
    Function CreateWVDHostPools {
        Param (
            [Parameter(Mandatory = $True, Position = 1, ValueFromPipeline = $False)]
            [String]$ResourceGroupName,
            [Parameter(Mandatory = $True, Position = 2, ValueFromPipeline = $False)]
            [string[]]$HostPools
        )
    
        $Location = "WestEurope"
    
        $ExistingResourceGroups = Get-AzResourceGroup
    
        if ($ExistingResourceGroups.ResourceGroupName -notcontains $ResourceGroupName) {
    
            Write-Host "ResourceGroup $($ResourceGroupName) does not exist. Creating new ResourceGroup" -ForegroundColor Green
    
            New-AzResourceGroup -Name $ResourceGroupName -Location $Location
            
        }
        else {
            Write-Host "ResourceGroup $($ResourceGroupName) already exists" -ForegroundColor Yellow
        }
    
        foreach ($HostPoolName in $HostPools){
    
        New-AzWvdWorkspace -ResourceGroupName $ResourceGroupName `
                            -Name "$($HostPoolName)-Workspace" `
                            -Location $Location `
                            -FriendlyName "$($HostPoolName)-Workspace" `
                            -ApplicationGroupReference $null `
                            -Description "$($HostPoolName)-Workspace"
    
        New-AzWvdHostPool   -Name $HostPoolName `
                            -ResourceGroupName $ResourceGroupName `
                            -Location $Location `
                            -HostPoolType Pooled `
                            -PreferredAppGroupType 'Desktop' `
                            -LoadBalancerType DepthFirst `
                            -MaxSessionLimit '12' `
        
        $HostPool = Get-AzWvdHostPool -Name $HostPoolName -ResourceGroupName $ResourceGroupName
    
        New-AzWvdApplicationGroup   -Name "$($HostPoolName)-DAG" `
                                    -ResourceGroupName $ResourceGroupName `
                                    -ApplicationGroupType 'Desktop' `
                                    -HostPoolArmPath $HostPool.id `
                                    -Location $Location
    
        $DAG = Get-AzWvdApplicationGroup -Name "$($HostPoolName)-DAG" -ResourceGroupName $ResourceGroupName
    
        Register-AzWvdApplicationGroup  -ResourceGroupName $ResourceGroupName `
                                        -WorkspaceName "$($HostPoolName)-Workspace" `
                                        -ApplicationGroupPath $DAG.id
        
        }
    }

In addition, by default the script creates hostpools which are pooled and use “DepthFirst” with a 12 users session limit. Edit the script if needed. For example: BreathFirst with a 15 users sessionlimit.

#### Example

Firstly, open up Powershell and install the modules by using the following code:
    
    
    $installedModules = Get-InstalledModule
    if ($installedModules.Name -notmatch "Az") {
        Install-Module Az -Force -AllowClobber
    }

Secondly, connect to Azure using this code:
    
    
    Connect-AzAccount

Next up, select your subscription:
    
    
    Select-AzSubscription -SubscriptionId SUBSCRIPTION ID | Set-AzContext

In addition, if you want to create multiple hostpools in different subscriptions you need to run the function multiple times in each context.  
  
Next up, load the Powershell function in your Powershell session by pasting the code in the Powershell session:

![Powershell Function for Creating WVD Hostpools](https://www.nielskok.tech/wp-content/uploads/2021/03/image-31.png)

  
After that, run the function by using this code:  
  
**NOTE:** If you create multiple hostpools, use this format: Hostpoolname1, Hostpoolname2 (the space between the names is mandatory, otherwise it does not work.) Furthermore, if the ResourceGroupName does not exist. The script creates the ResourceGroup
    
    
    CreateWVDHostPools -ResourceGroupName WVD2 -HostPools WVD-Dev, WVD-Test, WVD-Prod

After that, the script has run and has created the following output:

![Hostpools created with Powershell](https://www.nielskok.tech/wp-content/uploads/2021/03/image-32.png)

And that is how you use a Powershell Function for creating WVD Hostpools!

#### References

[Microsoft Docs about ResourceGroups](<https://docs.microsoft.com/en-us/powershell/module/az.resources/new-azresourcegroup?view=azps-5.6.0>)  
  
Other Posts at NielsKok.tech:  
[How to create Hostpools in General](<https://www.nielskok.tech/windows-virtual-desktop/wvd-create-hostpool/>)  
[WVD ARM Template update  
](<https://www.nielskok.tech/windows-virtual-desktop/wvd-intune-arm-template-update/>)