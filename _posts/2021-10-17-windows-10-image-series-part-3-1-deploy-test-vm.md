---
title: "Windows 10 Image Series – Part 3.1 – Deploy test VM"
date: 2021-10-17
layout: post
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/10/image-15.png"
categories:
  - "Azure Virtual Desktop"
  - "Azure"
  - "Infrastructure as code"
tags:
  - "Azure"
  - "Azure Virtual Desktop"
  - "Infrastructure as code"
  - "Automation"
  - "AVD"
  - "BICEP"
  - "MEM"
  - "Microsoft365"
source_url: "https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/"
---

In this series I am going to show you how build a Windows 10 Image via Azure Pipelines and DevOps without 3rd party tooling, welcome to part 3.1!   
  
This is a blog which describes how you can deploy a VM from your newly created Shared Image Gallery Image via Bicep.  


#### Prerequisites 

Part 1, 2 & 3 of this series needs to be completed. These are the links:  
  
1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline) 

2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation) 

3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)

Therefore, the prerequisites from part 1 are also required for this part. So:

Firstly, I am assuming that you have knowledge of Azure DevOps. These are the parts that already need be setup:

In addition, you need the Azure CLI module to run the Bicep template as the example. You can download it [here](<https://docs.microsoft.com/nl-nl/cli/azure/install-azure-cli-windows?tabs=azure-powershell>).  
  
Furthermore, I would advise you to use Powershell 7, [link](<https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.1>).   
  
Lastly, you will need the bicep module, [link](<https://github.com/Azure/bicep/blob/main/docs/installing.md>).

#### Checkout/Skip to other parts: 

0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps)   
  
1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline) 

2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation) 

3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
4\. [Windows 10 Image Series – Part 4](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>) (SessionHost Deployment from Image)   
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)  
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML) 

#### Creating a test VM from the Shared Image Gallery

Firstly, you need to save this code as a .Bicep file:
[code] 
    //General Parameters
    param location string = 'Westeurope'
    param virtualMachineName string = 'Windows-TestVM'
    param osDiskType string = 'Premium_LRS'
    param virtualMachineSize string = 'Standard_B4ms'
    param adminUsername string = 'TestVMAdmin'
    //Gallery Parameters
    param galleryResourceGroup string = 'RG_WE_SharedImageGallery'
    param galleryName string = 'WVDImageGallery'
    param galleryImageDefinitionName string = 'Windows10WVDImage'
    //Virtual Network Parameters
    param vnetResourceGroup string = ''
    param vnetName string = ''
    param subnetName string = ''
    
    @secure()
    param adminPassword string
    param patchMode string = 'manual'
    param enableHotpatching bool = false
    
    var imageReference = resourceId(galleryResourceGroup,'Microsoft.Compute/galleries/images/versions', '${galleryName}', '${galleryImageDefinitionName}','latest')
    var subnetId = resourceId(vnetResourceGroup,'Microsoft.Network/virtualNetworks/subnets','${vnetName}','${subnetName}')
    var virtualMachineComputerName = virtualMachineName
    var NicName = '${virtualMachineName}-NIC'
    
    
    resource NIC_Resource 'Microsoft.Network/networkInterfaces@2018-10-01' = {
      name: NicName
      location: location
      properties: {
        ipConfigurations: [
          {
            name: 'ipconfig1'
            properties: {
              subnet: {
                id: subnetId
              }
              privateIPAllocationMethod: 'Dynamic'
            }
          }
        ]
      }
      dependsOn: []
    }
    
    resource VM_Resource 'Microsoft.Compute/virtualMachines@2021-03-01' = {
      name: virtualMachineName
      location: location
      properties: {
        hardwareProfile: {
          vmSize: virtualMachineSize
        }
        storageProfile: {
          osDisk: {
            createOption: 'FromImage'
            managedDisk: {
              storageAccountType: osDiskType
            }
          }
          imageReference: {
            id: imageReference
          }
        }
        networkProfile: {
          networkInterfaces: [
            {
              id: NIC_Resource.id
            }
          ]
        }
        osProfile: {
          computerName: virtualMachineComputerName
          adminUsername: adminUsername
          adminPassword: adminPassword
          windowsConfiguration: {
            enableAutomaticUpdates: false
            provisionVMAgent: true
            patchSettings: {
              enableHotpatching: enableHotpatching
              patchMode: patchMode
            }
          }
        }
        licenseType: 'Windows_Client'
      }
      dependsOn: [
        NIC_Resource
      ]
    }
    
[/code]

After that, you have the option to add the following parameters as default values:

![Windows 10 Image Series – Part 3.1 - Networking Default values bicep](https://www.nielskok.tech/wp-content/uploads/2021/10/image-11.png)

I set default values for my testing environment. You can also add these parameters at deployment. I will show this in the example.  
  
After that, you need to log to Azure CLI. Use this command:
[code] 
    az login
[/code]

**Optional:** If you have multiple subscriptions please select your subscription in Azure CLI with the following command:
[code] 
    az account set --subscription subscription-id
[/code]

Create a resource group to deploy your test VM in:
[code] 
    az group create --location location --name resourcegroupname
[/code]

Lastly, deploy the bicep template to create the VM.
[code] 
    az deployment group create `
        --resource-group resource-group-name `
        --template-file path-to-template `
        --parameters parameters
[/code]

#### Example deployment

I have logged and select my appropriate subscription and I now show you the process of creating the resource group and deploying the bicep template.  
  
Firstly, create the resource group:

![Windows 10 Image Series – Part 3.1 - Deploy Resource Group](https://www.nielskok.tech/wp-content/uploads/2021/10/image-13.png)

After that, deploy the bicep template:

![](https://www.nielskok.tech/wp-content/uploads/2021/10/image-14.png)

As a result, the Azure Portal states that the bicep template is being deployed:

![](https://www.nielskok.tech/wp-content/uploads/2021/10/image-15-1024x453.png)

**Optional:** If you want to set the network parameters when running the bicep template. Please use the following example:
[code] 
    az deployment group create `
        --resource-group resource-group-name `
        --template-file path-to-template `
        --parameters adminPassword='Password' vnetResourceGroup='ResourceGroupName' vnetName='VNETNAME' subnetName='SUBNETNAME'
[/code]

And that concludes Windows 10 Image Series – Part 3.1. Please check out the other parts also:

0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps)   
  
1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline) 

2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation) 

3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
4\. [Windows 10 Image Series – Part 4](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>) (SessionHost Deployment from Image)   
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)  
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML)
