---
title: "Windows 10 Image Series – Part 6 - YAML/Bicep SessionHost Deployment"
date: 2021-08-18
layout: post
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/08/image-34.png"
categories:
  - "Devops"
  - "Azure"
  - "Azure Virtual Desktop"
tags:
  - "Azure"
  - "Azure Virtual Desktop"
  - "DevOps"
  - "Infrastructure as code"
  - "Automation"
  - "AVD"
  - "AzureVirtualDesktop"
  - "BICEP"
source_url: "https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/"
---

In this series I am going to show you how build a Windows 10 Image via Azure Pipelines and DevOps without 3rd party tooling, welcome to part 6!

We built the image, but I would like to show you another sessionhost deployment type. The type where YAML and Bicep are used to create the sessionhost.

#### Prerequisites 

Part 1, 2 & 3 of this series needs to be completed. These are the links:  
  
1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline) 

2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation) 

3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  


Therefore, the prerequisites from part 1 are also required for this part. So:

Firstly, I am assuming that you have knowledge of Azure DevOps. These are the parts that already need be setup:

In addition, if you don’t have knowledge about Azure DevOps and still want to follow this series please let me know. I might write a blog about the preparing Azure DevOps.

#### Checkout/Skip to other parts: 

0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps) 

1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline) 

2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation) 

3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
3.1 [Windows 10 Image Series – Part 3.1](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/>) (Create test VM from Shared Image Gallery)   
  
4\. [Windows 10 Image Series – Part 4](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>) (SessionHost Deployment via Powershell and Classic Pipelines)  
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Converted the Image build pipeline to YAML)

#### Creating the Azure Virtual Desktop Hostpool

Firstly, we need te create an Azure Virtual Desktop Hostpool to deploy the virtual machine to. We are using a Powershell function I created in an earlier [blogpost](<https://www.nielskok.tech/powershellautomation/powershell-function-for-creating-wvd-hostpools/>).  
  
Please copy this Powershell function: 
[code] 
    Function CreateWVDHostPools {
        Param (
            [Parameter(Mandatory = $True, Position = 1, ValueFromPipeline = $False)]
            [String]$ResourceGroupName,
            [Parameter(Mandatory = $True, Position = 2, ValueFromPipeline = $False)]
            [string[]]$HostPools
        )
     
        $Location = "WestEurope"
     
        $ExistingResourceGroups = Get-AzResourceGroup
     
        if ($ExistingResourceGroups.ResourceGroupName -notcontains $ResourceGroupName) {
     
            Write-Host "ResourceGroup $($ResourceGroupName) does not exist. Creating new ResourceGroup" -ForegroundColor Green
     
            New-AzResourceGroup -Name $ResourceGroupName -Location $Location
            
        }
        else {
            Write-Host "ResourceGroup $($ResourceGroupName) already exists" -ForegroundColor Yellow
        }
     
        foreach ($HostPoolName in $HostPools){
     
        New-AzWvdWorkspace -ResourceGroupName $ResourceGroupName `
                            -Name "$($HostPoolName)-Workspace" `
                            -Location $Location `
                            -FriendlyName "$($HostPoolName)-Workspace" `
                            -ApplicationGroupReference $null `
                            -Description "$($HostPoolName)-Workspace"
     
        New-AzWvdHostPool   -Name $HostPoolName `
                            -ResourceGroupName $ResourceGroupName `
                            -Location $Location `
                            -HostPoolType Pooled `
                            -PreferredAppGroupType 'Desktop' `
                            -LoadBalancerType DepthFirst `
                            -MaxSessionLimit '12' `
        
        $HostPool = Get-AzWvdHostPool -Name $HostPoolName -ResourceGroupName $ResourceGroupName
     
        New-AzWvdApplicationGroup   -Name "$($HostPoolName)-DAG" `
                                    -ResourceGroupName $ResourceGroupName `
                                    -ApplicationGroupType 'Desktop' `
                                    -HostPoolArmPath $HostPool.id `
                                    -Location $Location
     
        $DAG = Get-AzWvdApplicationGroup -Name "$($HostPoolName)-DAG" -ResourceGroupName $ResourceGroupName
     
        Register-AzWvdApplicationGroup  -ResourceGroupName $ResourceGroupName `
                                        -WorkspaceName "$($HostPoolName)-Workspace" `
                                        -ApplicationGroupPath $DAG.id
        
        }
    }
[/code]

After that, run the following code:
[code] 
    CreateWVDHostPools -ResourceGroupName DemoPart4 -HostPools NielskokdemoPart4
[/code]

As a result, a new resourcegroup is created:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-27-1024x413.png)

And the AVD Hostpool, Workspace and Applicationgroup are created:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-28-1024x426.png)

After that, we are creating the YAML Pipeline for the sessionhost deployment.

#### Creating the YAML Pipeline

Firstly, log on to [Azure DevOps](<https://dev.azure.com/>).  
  
After that, go to repositories and create these files:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-21-1024x891.png)

In addition, you can find the files in my public Github repository:  
  
[JSON File](<https://github.com/Ruthhl3ss/public/blob/main/ARMTemplates/EmptyResourceGroup.json>)  
  
[Bicep File](<https://github.com/Ruthhl3ss/public/blob/main/Bicep/NewSessionHost.bicep>)  
  
[YAML File](<https://github.com/Ruthhl3ss/public/blob/main/YAMLTemplates/NewSessionHosts.yml>)  
  
  
Next, add some variables to the variable group “Windows10ImageVariables”. Go to Pipelines and after that go to library and select the variable group:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-22-1024x633.png)

  
Add these variables:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-23-1024x514.png)

Fill the variables accordingly:  
  
ARM_Subscription_ID –> Subscription ID of the subscription you want to deploy your resources in.  
  
az_tenant_id –> Tenant ID for your Azure Active Directory tenant.  
  
ServiceConnectionName –> Name of the Service Connection you use to connect Azure DevOps to your Azure tenant. ([more information](<https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml>))  
  
After that, go to Pipelines and create a new Pipeline:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-24-1024x518.png)

Select Azure Repos Git:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-25.png)

After that, select your repository:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-26.png)

Select Existing YAML file:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-27.png)

After that, select the YAML file created earlier:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-28.png)

Please change to following parameters and variables in the YAML file:

  * <<Domain Name>>
  * <<OU NAME>>
  * <<VNET Resource Group>>
  * <<VNet Name>>
  * <<Subnet Name>>
  * <<Service Principal Secret>>
  * <<Service Principal APP ID>>
  * <<Resource Group for Hostpool>>
  * <<Hostpool Name>>


After that, make sure that these file path are in line with your repository:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-29.png)

And:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-30.png)

Save the pipeline!  
  
Now it is time to run the pipeline!

#### Running the Pipeline

Go to the pipeline and click on run:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-31-1024x505.png)

After that, these parameters appear:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-32-1024x780.png)

Edit everything to your liking and click on run.  
  
Furthermore, you can set these defaults for each parameters in the YAML file by editting these values:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-33.png)

After 8 – 10 minutes the deployment has completed and this the result:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-34-1024x642.png)

The VMs are added to the hostpool and available:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-35-1024x346.png)

This was Windows 10 Image Series – Part 6, check out the other parts:   
  
0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps) 

1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline) 

2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation) 

3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
3.1 [Windows 10 Image Series – Part 3.1](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/>) (Create test VM from Shared Image Gallery)   
  
4\. [Windows 10 Image Series – Part 4](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>) (SessionHost Deployment via Powershell and Classic Pipelines)  
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Converted the Image build pipeline to YAML)
