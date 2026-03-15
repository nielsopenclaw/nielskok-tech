---
layout: post
title: "WVD Create HostPool - NielsKok.Tech Azure Virtual Desktop"
date: 2021-11-24
author: "Niels Kok"
categories: ["Azure Virtual Desktop"]
tags: ["Automation", "Azure", "Hostpool", "MicrosoftAzure", "powershell"]
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/03/image-30.png"
description: "This is blogpost about how to create an WVD Hostpool. The GUI method and the powershell method are shown. Azure Virtual Desktop"
original_url: "https://www.nielskok.tech/azure-virtual-desktop/wvd-create-hostpool/"
---

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-30.png)

# WVD Create HostPool

November 24, 2021March 19, 2021 by [Niels Kok](<https://www.nielskok.tech/author/admin/> "View all posts by Niels Kok")

This is a blogpost about how to create a WVD HostPool. I demonstrate two methods. The GUI based method and the Powershell method. In addition, you can also create the Hostpool via an ARM template but that’s a topic for another time.

#### The GUI based method

Firstly, log on to [Microsoft Azure](<https://portal.azure.com/>).   
  
After that, go to Windows Virtual Desktop:

![Microsoft Azure WVD](https://www.nielskok.tech/wp-content/uploads/2021/03/image-21.png)

Click on Host pools:

![Create WVD Hostpool portal add twice](https://www.nielskok.tech/wp-content/uploads/2021/03/image-22.png)

After that, click on add:

![Create WVD Hostpool portal add](https://www.nielskok.tech/wp-content/uploads/2021/03/image-23.png)

A new blade is shown. Firstly, you need to fill in the basics:  
  
Choose a subscription, resource group, hostpool name and location for your metadata. (West Europe is now available)  
  
A validation environment lets you test services that are made available by the WVD team at Microsoft. Apply this only to your development or test hostpools!  
  
Next up is the Hostpool type. Personal is the VDI option (1 user per machine). Pooled is the multi-user option. We are going with Pooled.  
  
The load balancing algorithm is very important. Breadth-first is the method which spreads out the users on the available VM’s as much as possible. Depth-first fills up a WVD host first and if that host reaches the Max session limit the next machine will be filled up. For auto-scaling purposes it is more easy and economical to use Depth-first.

![Create WVD Hostpool](https://www.nielskok.tech/wp-content/uploads/2021/03/image-24.png)

Next up is the option to create Virtual Machine. I don’t use this option because I deploy my VM’s via Azure DevOps.

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-25.png)

At the workspace tab you can register an desktop app group. I always choose to do so. Furthermore, I also register a workspace per hostpool:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-26.png)

Apply tags if you want to and after that click on review + create:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-28.png)

Finally, click on create to create the hostpool:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-29.png)

That’s how you create a WVD hostpool via the GUI for Microsoft Azure.

#### The Powershell Method

Obviously, this is my favorite method to create a WVD Hostpool.  
  
Firstly, log on to Microsoft Azure using the following code:
    
    
    Connect-AzAccount

Next up, set the correct subscription:
    
    
    Select-AzSubscription -SubscriptionId SUBSCRIPTION_ID | Set-AzContext

Now, we create 3 resources. The WVD Workspace, Hostpool and Desktop Application Group.  
  
Firstly, we use this code to create the workspace (fill in the parameters accordingly).
    
    
    New-AzWvdWorkspace -ResourceGroupName 'WVD' `
                       -Name 'WVD-Test' `
                       -Location 'westeurope' `
                       -FriendlyName 'WVD Test' `
                       -ApplicationGroupReference $null `
                       -Description 'WVD Test'

Next up is the WVD hostpool: (fill in the parameters accordingly)
    
    
    New-AzWvdHostPool   -Name 'WVD-Testing' `
                        -ResourceGroupName 'WVD' `
                        -Location 'WestEurope' `
                        -HostPoolType Pooled `
                        -PreferredAppGroupType 'Desktop' `
                        -LoadBalancerType DepthFirst `
                        -MaxSessionLimit '12'

We are going to create the Desktop Application Group. We first need to grab the resource id from the hostpool that we just created:
    
    
    $HostPool = Get-AzWvdHostPool -Name 'WVD-Testing' -ResourceGroupName 'WVD'

After that we can create the Desktop Application Group: (fill in the parameters accordingly)
    
    
    New-AzWvdApplicationGroup   -Name 'WVD-Testing-DAG' `
                                -ResourceGroupName 'WVD' `
                                -ApplicationGroupType 'Desktop' `
                                -HostPoolArmPath $HostPool.id `
                                -Location 'WestEurope'

Lastly, we are registering the Desktop Application Group to the workspace.  
  
First, grab the resource id for the Desktop Application Group:
    
    
    $DAG = Get-AzWvdApplicationGroup -Name 'WVD-Testing-DAG' -ResourceGroupName 'WVD'

Then we register the Desktop Application Group to the Workspace:
    
    
    Register-AzWvdApplicationGroup  -ResourceGroupName 'WVD' `
                                    -WorkspaceName 'WVD-Test' `
                                    -ApplicationGroupPath $DAG.id

The result is these resources have been created:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-30.png)

#### References

[Microsoft Docs about hostpools](<https://docs.microsoft.com/nl-nl/azure/virtual-desktop/create-host-pools-powershell>)  
  
Other Posts:  
[WVD ARM Template Update](<https://www.nielskok.tech/windows-virtual-desktop/wvd-intune-arm-template-update/>)  
[WVD HostPool registration key](<https://www.nielskok.tech/windows-virtual-desktop/wvd-hostpool-registration-key/>)