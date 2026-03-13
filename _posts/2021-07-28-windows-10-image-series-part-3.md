---
title: "Windows 10 Image Series – Part 3 - Shared Image Gallery"
date: 2021-07-28
layout: post
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/07/image-17.png"
categories:
  - "Azure Virtual Desktop"
  - "Azure"
  - "DevOps"
tags:
  - "Azure"
  - "Azure Virtual Desktop"
  - "DevOps"
  - "Infrastructure as code"
  - "Powershell Automation"
  - "Automation"
  - "AVD"
  - "Image"
source_url: "https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3/"
---

In this series I am going to show you how build a Windows 10 Image via Azure Pipelines and DevOps without 3rd party tooling, welcome to part 3!  
  
Part 3 is about the Shared Image Gallery. This is a service within Microsoft Azure to publish images and we are publishing our managed image in the gallery for distribution.

####  Prerequisites 

Part 1 & 2 of this series needs to be completed. These are the links:

[Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline) 

[Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation)   


Therefore, the prerequisites from part 1 are also required for this part. So:

Firstly, I am assuming that you have knowledge of Azure DevOps. These are the parts that already need be setup:

In addition, if you don’t have knowledge about Azure DevOps and still want to follow this series please let me know. I might write a blog about the preparing Azure DevOps.

#### Checkout/Skip to other parts:

0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps)   
  
1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline) 

2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation)  
  
3.1 [Windows 10 Image Series – Part 3.1](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/>) (Create test VM from Shared Image Gallery)   
  
4\. [Windows 10 Image Series – Part 4](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>) (SessionHost Deployment from Image)  
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)  
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML)  


#### Creating the Shared Image Gallery

There are 2 options to create the shared image gallery. Powershell and GUI based. I am showing the Powershell option.  
  
Firstly, log on to Azure via Powershell with this command:
[code] 
    Connect-AzAccount
[/code]

After that, make sure you select the correct subscription:
[code] 
    Select-AzSubscription "SubscriptionID"
[/code]

Now it is time to create the Shared Image Gallery. In the example we create a new resource group for the Shared Image Gallery
[code] 
    $resourceGroup = New-AzResourceGroup `
       -Name 'RG_WE_SharedImageGallery' `
       -Location 'West Europe'	
[/code]

As a result, this is the resource group:

![Windows 10 Image Series – Part 3 - Shared Image Gallery Resource Group creation.](https://www.nielskok.tech/wp-content/uploads/2021/07/image-11.png)

After that, run the following code to create the Shared Image Gallery:
[code] 
    $gallery = New-AzGallery `
       -GalleryName 'WVDImageGallery' `
       -ResourceGroupName $resourceGroup.ResourceGroupName `
       -Location $resourceGroup.Location `
       -Description 'Shared Image Gallery for nielskok.tech'
[/code]

It takes a minut or so but this is the Shared Image Gallery:

![Windows 10 Image Series – Part 3 - Shared Image Gallery creation.](https://www.nielskok.tech/wp-content/uploads/2021/07/image-12.png)

#### Creating Shared Image Gallery Image Definition

We continue in our same Powershell session to create the Shared Image Gallery Definition. In addition, the image definition is needed for us to upload our image in the Shared Image Gallery.  
  
Use this code to create the image definition:
[code] 
    $imageDefinition = New-AzGalleryImageDefinition `
       -GalleryName $gallery.Name `
       -ResourceGroupName $gallery.ResourceGroupName `
       -Location $gallery.Location `
       -Name 'Windows10WVDImage' `
       -OsState generalized `
       -OsType Windows `
       -Publisher 'NielsKok.tech' `
       -Offer 'Windows10' `
       -Sku '21h1'
[/code]

As a result, this image definition is now available in the Shared Image Gallery:

![Windows 10 Image Series – Part 3 - Image Definition](https://www.nielskok.tech/wp-content/uploads/2021/07/image-13-1024x310.png)

#### Add the Shared Image Gallery to the Azure DevOps Pipeline

Firstly, we add a new job to the existing pipeline:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-14-1024x279.png)

Name the job and fill in the parameters:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-16-1024x494.png)

Furthermore, use this script in the Inline Script section:
[code] 
    ## Variables
    
    $location = '$(Location)'
    $imageName = '$(ImageName)'
    $ImagergName = '$(ImageRGName)'
    $SharedImageGalleryName = 'WVDImageGallery'
    $SharedImageGalleryRG = 'RG_WE_SharedImageGallery'
    $SharedImageGalleryDefinitionName = 'Windows10WVDImage'
    $GalleryImageVersionName = '1.0.$(Build.BuildId)'
    
    
    ## Get Managed Image Info
    
    $managedImage = Get-AzImage `
       -ImageName $imageName `
       -ResourceGroupName $ImagergName
    
    ## Get Image Definition Info
    
    $ImageDefinition = Get-AzGalleryImageDefinition `
        -ResourceGroupName $SharedImageGalleryRG `
        -GalleryName $SharedImageGalleryName `
        -GalleryImageDefinitionName $SharedImageGalleryDefinitionName
    
    ## Create Upload Job
    
    $region1 = @{Name='West Europe';ReplicaCount=2}
    $targetRegions = @($region1)
    $job = $imageVersion = New-AzGalleryImageVersion `
          -GalleryImageDefinitionName $imageDefinition.Name `
          -GalleryImageVersionName $GalleryImageVersionName `
          -GalleryName $SharedImageGalleryName `
          -ResourceGroupName $imageDefinition.ResourceGroupName `
          -Location $imageDefinition.Location `
          -TargetRegion $targetRegions  `
          -SourceImageId $managedImage.Id.ToString() `
          -PublishingProfileEndOfLifeDate '2021-12-31' `
          -asJob
    
    ## Wait for upload to complete
    $Count = 1
    
    do {
        #Starting Count
        $Count
        $Count++
    
        Write-Host "Shared Image Gallery Upload not yet completed, Starting Sleep for 60 Seconds"
        Start-Sleep 60
    
        if ($Count -ge 75) { 
            Write-Host "Shared Image Gallery Upload FAILED"
            Break
        }
    } while ($job.State -eq "Running")
    
    if ($job.State -eq "Completed") {
        Write-Host "Shared Image Gallery Upload completed"
    }
[/code]

After that, run the pipeline.  
  
When the Azure CLI task for the Shared Image Gallery upload runs the task will report every minute about the upload status:  
  
(The task runs for about 12 – 15 minutes)

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-17-1024x687.png)

Furthermore, you can see a new version in the Shared Image Gallery:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-19-1024x427.png)

After a while (12 – 15 minutes) the job has finished:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-20-1024x418.png)

This was Windows 10 Image Series – Part 3, check out the other parts:  
  
0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps)   
  
1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline) 

2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation)  
  
3.1 [Windows 10 Image Series – Part 3.1](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/>) (Create test VM from Shared Image Gallery)   
  
4\. [Windows 10 Image Series – Part 4](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>) (SessionHost Deployment from Image)  
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)   
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML) 

#### References

[Microsoft Doc about Shared Image Gallery](<https://docs.microsoft.com/en-us/azure/virtual-machines/shared-images-powershell>)
