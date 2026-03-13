---
title: "Windows 10 Image Series – Part 4 - Deploy SessionHost Powershell"
date: 2021-08-02
layout: post
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/08/image-2.png"
categories:
  - "Azure Virtual Desktop"
  - "Azure"
  - "DevOps"
tags:
  - "Azure"
  - "Azure Virtual Desktop"
  - "DevOps"
  - "Infrastructure as code"
  - "AVD"
  - "AzureDevOps"
  - "Hostpools"
  - "Pipelines"
source_url: "https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/"
---

In this series I am going to show you how build a Windows 10 Image via Azure Pipelines and DevOps without 3rd party tooling, welcome to part 4!  
  
We built the image in the past parts. It’s now time to actually built hostpools from our image. So, part 4 is about building hostpools!

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
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)  
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML) 

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

After that run the following code:
[code] 
    CreateWVDHostPools -ResourceGroupName DemoPart4 -HostPools NielskokdemoPart4
[/code]

As a result, a new resourcegroup is created:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-27-1024x413.png)

And the AVD Hostpool, Workspace and Applicationgroup are created:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-28-1024x426.png)

#### Creating ResourceGroup for Virtual Machines

Secondly, we need a ResourceGroup for our Virtual Machines to reside in. I have created a Powershell Function to create ResourceGroups.  
  
Please copy the following code:
[code] 
    function CreateResourceGroups {
        param (
            [Parameter(Mandatory = $True, Position = 1, ValueFromPipeline = $False)]
            [string[]]$ResourceGroups,
            [Parameter(Mandatory = $False, Position = 2, ValueFromPipeline = $False)]
            [string]$Location = 'WestEurope'
    
        )
        
        $ExistingResourceGroups = Get-AzResourceGroup
    
        foreach ($ResourceGroup in $ResourceGroups){
            if ($ExistingResourceGroups.ResourceGroupName -notcontains $ResourceGroup) {
                Write-Host "ResourceGroup $($ResourceGroup) does not exist. Creating new ResourceGroup" -ForegroundColor Green
    
                New-AzResourceGroup -Name $ResourceGroup -Location $Location
            }
            else {
                Write-Host "ResourceGroup $($ResourceGroup) already Exists" -ForegroundColor Yellow
            }
    
            
        }
        
    }
[/code]

After that, run this code: (If you want another region please add the -location parameter)
[code] 
    CreateResourceGroups -ResourceGroups DemoPart4VirtualMachines
[/code]

As a result, the resource group is created:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-29-1024x411.png)

#### Creating the hostpool pipeline

We are using build pipelines for our hostpool creation stage. We are not going to use release pipelines. In addition, YAML can’t be used in release pipelines and since YAML holds all the features in Azure Pipelines, we need to use build pipelines.

Firstly, log to [Azure DevOps](<https://dev.azure.com/>).  
  
After that, go to Pipelines:

![Windows 10 Image Series - Part 4 - Azure Pipelines](https://www.nielskok.tech/wp-content/uploads/2021/07/image-21-1024x559.png)

And click on “New pipeline”:

![Windows 10 Image Series - Part 4 - Creating new pipeline](https://www.nielskok.tech/wp-content/uploads/2021/07/image-22-1024x421.png)

For now, we create a classic pipeline. In Part 5 we will convert everything to YAML:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-23.png)

After that, select your project, repository and branch:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-24-1024x411.png)

Lastly, start with an empty job:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-25-1024x498.png)

Next, name your pipeline and select the agent pool:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-26-1024x280.png)

After that, add a Azure CLI job:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-1024x296.png)

Fill in the name, select the service connection and select Inline Powershell:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-1-1024x598.png)

I have used Sander Rozemuller’s method to deploy via Powershell. Please check out his [blog](<https://rozemuller.com/avd-automation-cocktail-avd-automated-with-powershell/>) as well.   
  
Furthermore, put this script in the Inline Script section: (Please note that you need to fill in the variables)
[code] 
    #### Set Variables
    #ShareImageGallery
    $SharedImageGalleryName = 'WVDImageGallery'
    $SharedImageGalleryRG = 'RG_WE_SharedImageGallery'
    $SharedImageGalleryDefinitionName = 'Windows10WVDImage'
    #Hostpool - SessionhostProperties
    $HostpoolRG = 'DemoPart4'
    $HostPoolName = 'NielskokdemoPart4'
    $VMResourceGroupName = 'DemoPart4VirtualMachines'
    $SessionHostCount = 1
    $InitialNumber = 1
    $AVDPrefix = 'DEMO-'
    $VMSize = 'Standard_D2s_v3'
    $DiskSizeGB = 128
    $Domain = 'DOMAINNAME' 
    $ouPath = 'OUTPATH'
    $moduleLocation = 'https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts/Configuration.zip'
    $VMLocalAdminUser = 'VMLocalAdminUser'
    $virtualNetworkname = 'VIRTUALNETWORKNAME'
    $SubnetName = 'SUBNETNAME'
    $Location = 'WestEurope'
    
    ## Get Image Version from Shared Image Gallery
    $imageVersion = Get-AzGalleryImageVersion -ResourceGroupName $SharedImageGalleryRG `
         -GalleryName $SharedImageGalleryName `
         -GalleryImageDefinitionName $SharedImageGalleryDefinitionName | Select-Object -Last 1
    
    ## Get HostPool Properties
    $HostpoolProperties = Get-AzWvdHostPool -ResourceGroupName $HostpoolRG `
        -Name $HostPoolName
    
    ## Create Hostpool RegistrationKey 
    $HostpoolRegKey = New-AzWvdRegistrationInfo -ResourceGroupName $HostpoolRG `
        -HostPoolName $HostPoolName `
        -ExpirationTime (Get-Date).AddDays(14)
    
    ## Get Virtual Network Properties
    
    $virtualNetwork = Get-AzVirtualNetwork -Name $virtualNetworkname
    
    ## Get KeyVault Secrets
    $DomainJoinPassword = Get-AzKeyVaultSecret -VaultName 'KEYVAULTNAME' -Name DomainJoinPassword -AsPlainText
    $DomainJoinUsername = Get-AzKeyVaultSecret -VaultName 'KEYVAULTNAME' -Name DomainJoinUsername -AsPlainText
    $LocalAdminPassword = ConvertTo-SecureString (Get-AzKeyVaultSecret -VaultName 'WEDevOps' -Name LocalAdminPassword) -AsPlainText -Force
    
    ## Create Virtual Machines
    Do {
        Write-Host ''
        
        $VMName = $avdPrefix+"$initialNumber"
        $ComputerName = $VMName
        $nicName = "nic-$vmName"
        $NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $VMResourceGroupName -Location $location -SubnetId ($virtualNetwork.Subnets | Where { $_.Name -eq $SubnetName }).Id
        $Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $LocalAdminPassword);
    
        $VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
        $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
        $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
        $VirtualMachine = Set-AzVMOSDisk -Windows -VM $VirtualMachine -CreateOption FromImage -DiskSizeInGB $DiskSizeGB
        $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -Id $imageVersion.id
    
        $sessionHost = New-AzVM -ResourceGroupName $VMResourceGroupName -Location $Location -VM $VirtualMachine
    
        $initialNumber++
        $sessionHostCount--
        Write-Output "$VMName deployed"
    }
    while ($sessionHostCount -ne 0) {
        Write-Verbose "Session hosts are created"
    }
    
    ## Install modules for Domain Join and Join Sessionhost to Hostpool
    
    $domainJoinSettings = @{
        Name                   = "joindomain"
        Type                   = "JsonADDomainExtension" 
        Publisher              = "Microsoft.Compute"
        typeHandlerVersion     = "1.3"
        SettingString          = '{
            "name": "'+ $($domain) + '",
            "ouPath": "'+ $($ouPath) + '",
            "user": "'+ $($DomainJoinUsername) + '",
            "restart": "'+ $true + '",
            "options": 3
        }'
        ProtectedSettingString = '{
            "password":"' + $($DomainJoinPassword) + '"}'
        VMName                 = $VMName
        ResourceGroupName      = $VMResourceGroupName
        location               = $Location
    }
    Set-AzVMExtension @domainJoinSettings
    
    $avdDscSettings = @{
        Name               = "Microsoft.PowerShell.DSC"
        Type               = "DSC" 
        Publisher          = "Microsoft.Powershell"
        typeHandlerVersion = "2.73"
        SettingString      = "{
            ""modulesUrl"":'$moduleLocation',
            ""ConfigurationFunction"":""Configuration.ps1\\AddSessionHost"",
            ""Properties"": {
                ""hostPoolName"": ""$($HostpoolProperties.Name)"",
                ""registrationInfoToken"": ""$($HostpoolRegKey.token)""
            }
        }"
        VMName             = $VMName
        ResourceGroupName  = $VMResourceGroupName
        location           = $Location
    }
    Set-AzVMExtension @avdDscSettings
    
[/code]

After that. run the pipeline:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-2-1024x558.png)

The VMs created:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-3-1024x561.png)

And added to the Hostpool:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-4-1024x264.png)

This was Windows 10 Image Series – Part 4, check out the other parts:  
  
0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps)   
  
1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline) 

2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation) 

3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)  
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML) 

#### References

[Sander Rozemuller’s Powershell Sessionhost deployment](<https://rozemuller.com/avd-automation-cocktail-avd-automated-with-powershell/>)
