---
layout: post
title: "Windows 10 Image Series - Part 1 - Create The VM - NielsKok.Tech"
date: 2021-11-24
author: "Niels Kok"
categories: ["DevOps"]
tags: ["Azure", "DevOps", "Pipelines", "Windows10", "WindowsVirtualDesktop"]
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/06/image-42.png"
description: "Windows 10 Image series - part 1. Build a Windows 10 Image via Azure Pipeline via Infra as code without 3rd party tooling."
original_url: "https://www.nielskok.tech/devops/windows-10-image-series-part-1/"
---

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-42.png)

# Windows 10 Image Series – Part 1 – Create The VM

November 24, 2021June 8, 2021 by [Niels Kok](<https://www.nielskok.tech/author/admin/> "View all posts by Niels Kok")

In this series I am going to show you how build a Windows 10 Image via Azure Pipelines and DevOps without 3rd party tooling, welcome to part 1!   
  
Part 1 is about building the Windows 10 machine, sysprepping it and creating an image from it. 

#### Prerequisites

There are some prerequisites.  
  
Firstly, I am assuming that you have knowledge of Azure DevOps. These are the parts that already need be setup:

  * A Virtual Network in your Azure subscription.
  * Service Connection via Service Principal to ARM ([more information](<https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml>))
  * Azure Key Vault with connection to Azure DevOps ([more information](<https://zimmergren.net/using-azure-key-vault-secrets-from-azure-devops-pipeline/>))
  * GIT Repo in Azure DevOps ([more information](<https://docs.microsoft.com/en-us/azure/devops/repos/git/create-new-repo?view=azure-devops>))  


In addition, if you don’t have knowledge about Azure DevOps and still want to follow this series please let me know. I might write a blog about the preparing Azure DevOps.

#### Checkout/Skip to the other parts

0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps)

2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation)  
  
3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/windows-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
3.1 [Windows 10 Image Series – Part 3.1](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/>) (Create test VM from Shared Image Gallery)   
  
4\. [Windows 10 Image Series – Part 4 ](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>)(SessionHost Deployment from Image)  
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)  
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML) 

## Building the Windows 10 Image Pipeline

The pipeline consist of 3 parts:

  * Building a Windows 10 machine using an ARM Template
  * Sysprepping the Windows 10 machine that was just created
  * Creating an image from the just created machine


We are starting with creating the pipeline.

### Creating the pipeline

Firstly, log on to the [Azure portal](<https://portal.azure.com/>).  
  
After that, create a resource group, for example:

  * RG_WE_GoldenImage_WVD


This resourcegroup is for storing the Windows 10 images. 

![Windows 10 Image Series - Part 1 - Resource Group](https://www.nielskok.tech/wp-content/uploads/2021/06/image-2.png)

Log on to [Azure DevOps](<https://dev.azure.com/>).  
  
After that, go to your repository and add a new folder:

![Windows 10 Image Series - Part 1 - Creating the pipeline](https://www.nielskok.tech/wp-content/uploads/2021/06/image.png)

Call the folder ARM templates and put the following file in the folder:

  * Windows10Template.json


Furthermore, you can find this file in my [github](<https://github.com/Ruthhl3ss/public/tree/main/ARMTemplates>). Your repo should look like this:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-12.png)

This ARM Template build the Windows 10 Virtual Machine.  
  
Now go to pipelines:

![Windows 10 Image Series - Part 1 - Creating the pipeline add](https://www.nielskok.tech/wp-content/uploads/2021/06/image-1.png)

Create a new pipeline:

![Windows 10 Image Series - Part 1 - adding the pipeline](https://www.nielskok.tech/wp-content/uploads/2021/06/image-3-1024x314.png)

Pick “Use the classic editor”:

![Windows 10 Image Series - Part 1 - Classic git repo](https://www.nielskok.tech/wp-content/uploads/2021/06/image-4.png)

Select your repository where you have put the ARM Template folder and file:

![Windows 10 Image Series - Part 1 - Selecting a source](https://www.nielskok.tech/wp-content/uploads/2021/06/image-5.png)

Lastly, start with an “Empty Job”:

![Windows 10 Image Series - Part 1 - Selecting empty job](https://www.nielskok.tech/wp-content/uploads/2021/06/image-6.png)

Name the pipeline and select your agent pool:

![Windows 10 Image Series - Part 1 - Naming pipeline and agents](https://www.nielskok.tech/wp-content/uploads/2021/06/image-7.png)

Click on save:

![saving  Azure pipeline](https://www.nielskok.tech/wp-content/uploads/2021/06/image-8.png)

The pipeline for Windows 10 Image Series – Part 1 has been created! Let’s go on to the next chapter.

#### Variable Groups

Next, go to “Library” in the pipelines section:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-18.png)

We are now going to create a variable group where we specify our variables for our build later on. That makes it easier to use the same variables through different scripts and templates.  
  
Please click on “+ Variable Group”:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-19.png)

Name it and add the following variables:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-21.png)

ImageName –> Windows10Image_$(Build.DefinitionName)_$(Build.BuildId) – _This is name that the image is going to have_  
ImageRGName –> RG_WE_GoldenImage_WVD – _ResourceGroup where the image is going to land_  
Location –> WestEurope – _Location for all the builds_  
RGName –> RG_WE_Win10ImageBuild_Temp – _Resource Group where the VM is going to build and deleted afterwards_  
Subnet –> WVD-Build – _Subnet where the VM is going to be build in_  
VMName –> Windows10VM_$(Build.DefinitionName)_$(Build.BuildId) – _Generic Name for the VM_  
VNetResourceID –> _Put the resource id of your VNET here_  
  
After that, click on save!  
  
Next up, go to your Azure Key Vault in the Azure Portal. For example:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-23.png)

Add a secret to this key vault:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-24.png) ![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-25.png)

Name it: LocalAdminPassword and put a generated password in here:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-26.png)

Go back to Azure DevOps, pipelines, library and add a new variable group:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-27.png)

Name the variable group,  
  
Choose “Link secret from an Azure Key vault as variables”  
  
Pick your service connection and vault  
  
Lastly, add the variables:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-28.png)

Go back to your azure pipeline:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-29.png)

Edit the pipeline:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-30-1024x318.png)

Click on variables:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-31.png)

And link both variable groups to the pipeline:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-32.png)

This is what it should look like after that:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-33.png)

#### Filling the pipeline with jobs

It is now time to fill the pipeline with the jobs that handle these tasks:

  * Building a Windows 10 machine using an ARM Template
  * Sysprepping the Windows 10 machine that was just created
  * Creating an image from the just created machine


##### Building a Windows 10 machine using an ARM Template

Firstly, add the ARM Template job by clicking on this icon:

![Adding pipeline jobs](https://www.nielskok.tech/wp-content/uploads/2021/06/image-9.png)

After that, search for “ARM Template deployment” and click on add:

![Windows 10 Image Series - Part 1 - Add ARM templatejob](https://www.nielskok.tech/wp-content/uploads/2021/06/image-11.png)

Rename the task and select the deployment scope:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-13-1024x327.png)

After that, select the “Azure Resource Manager Connection”, “Subscription” and “Action”:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-14-1024x256.png)

Next, enter a Resource Group name and a location. **NOTE:** The Resource Group should not exist! This will be automatically cleaned up when the machine has been converted to an image.

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-15.png)

It is now time to pick the ARM template to deploy the Windows 10 VM.  
  
Firstly, select “Linked Artifact”:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-16-1024x133.png)

At the template section select the ARM template we added earlier on in the process. The Windows10template.json file:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-17.png)

Now click on “Override Template Parameters” and make sure to add the following parameters from the variable group created earlier:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-34.png)

##### Sysprepping the Windows 10 machine that was just created

Add a second job and search for Azure CLI:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-35-1024x312.png)

Name the task, choos a resource manager connection, script type and a script location:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-36.png)

After that, put the following script in the “Inline Script section”:
    
    
    $FileUri = "https://raw.githubusercontent.com/Ruthhl3ss/public/main/AzureVMExtensions/SysPrepScript.ps1"
    
    Set-AzVMCustomScriptExtension -ResourceGroupName $(RGName) -VMName $(VMName) -Location $(Location) -FileUri $FileUri -Run 'SysPrepScript.ps1' -Name SysPrep
    
    Get-AzVMExtension -ResourceGroupName $(RGName) -VMName $(VMName) | where {$_.ExtensionType -eq "CustomScriptExtension"} | Remove-AzVMExtension -Force

This script downloads a sysprep script from my github repository and applies it to the VM. If you are not comfortable using this public script you can also put the sysprep script in a storage account ([more information](<https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/custom-script-windows>)).  
  
Afterwards the ‘CustomScriptExtension’ is removed so you can apply another one later on if you want to.  
  
After this step the VM is sysprepped and ready to be captured.

##### Creating an image from the just created machine

Add another Azure CLI Job to the pipeline:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-37-1024x336.png)

Name the task, choos a resource manager connection, script type and a script location:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-38.png)

After that, put the following script in the “Inline Script section”:
    
    
    #  Capture Image
    
    $vmName = '$(VMName)'
    $rgName = '$(RGName)'
    $location = '$(Location)'
    $imageName = '$(ImageName)'
    $ImagergName = '$(ImageRGName)'
    
    
    $VMStatus = Get-AzVM -ResourceGroupName $rgName -Name $vmName -Status
    
    if ($VMStatus.Statuses[1].DisplayStatus -eq 'VM Running') {
        
        Write-Host 'VM is Running and will be turned off'
    
        Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force    
    
        Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    
        $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    
        $ImageConfiguration = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id
    
        New-AzImage -Image $ImageConfiguration -ImageName $imageName -ResourceGroupName $ImagergName
    
    }
    else {
        Write-Host 'VM is not running, capturing image will start immediatly'
    
        Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    
        $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    
        $ImageConfiguration = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id
    
        New-AzImage -Image $ImageConfiguration -ImageName $imageName -ResourceGroupName $ImagergName
    }
    
    ## Removing VM  Resource Group
    $ExistingImages = Get-AzResource -ResourceGroupName $ImagergName | Where-Object ResourceType -eq 'Microsoft.Compute/images'
    
    if ($ExistingImages.Name -contains $imageName) {
        
        Remove-AzResourceGroup -Name $rgName -Force
    
    }

This script turns the VM off if it is running and creates a managed image from it. After that, the resource group where the VM was build is removed by this script.

#### Run the Pipeline

When you are finished creating the pipeline you can click on queue:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-39.png)

The pipeline will start creating the job:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-42-1024x534.png)

This is the temporary resource group:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-41.png)

These are the temporary resources:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-44.png)

And last but not least there is the image being created:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-45.png)

And the resource group has been removed!  
  
This was Windows 10 Image Series – Part 1, check out the other parts:  
  
0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps)   
  
2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation)   
  
3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/windows-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
3.1 [Windows 10 Image Series – Part 3.1](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/>) (Create test VM from Shared Image Gallery)   
  
4\. [Windows 10 Image Series – Part 4](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>) (SessionHost Deployment from Image)  
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)

6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML)   
  
Windows 10 Image Series

#### References

Other posts:  
  
[Autopilot Device info menu script](<https://www.nielskok.tech/intune/autopilot-devices-script-menu/>)  
[Powershell function for creating Hostpools](<https://www.nielskok.tech/powershellautomation/powershell-function-for-creating-wvd-hostpools/>)  
  
References  
[Microsoft Docs about AZExtensions](<https://docs.microsoft.com/en-us/powershell/module/az.compute/remove-azvmcustomscriptextension?view=azps-6.0.0>)