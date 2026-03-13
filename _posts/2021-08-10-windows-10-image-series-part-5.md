---
title: "Windows 10 Image Series – Part 5 - YAML Image Build Pipeline"
date: 2021-08-10
layout: post
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/08/image-19.png"
categories:
  - "Azure"
  - "Azure Virtual Desktop"
  - "DevOps"
tags:
  - "Azure"
  - "Azure Virtual Desktop"
  - "DevOps"
  - "Infrastructure as code"
  - "Automation"
  - "AVD"
  - "AzureDevOps"
  - "powershell"
source_url: "https://www.nielskok.tech/azure/windows-10-image-series-part-5/"
---

In this series I am going to show you how build a Windows 10 Image via Azure Pipelines and DevOps without 3rd party tooling, welcome to part 5!

We built the image, deployed a sessionhost in the previous parts. Next up is converting the Image Pipeline to YAML Pipelines. In addition, YAML pipelines hold all the features. Classic Pipelines do not. 

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
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML)   


#### Converting the WVD Image Build Pipeline to YAML

In this paragraph, we are actually converting the pipeline to YAML. The proces is quite simple. For instance, this is the part which creates the Windows 10 VM:
[code] 
    pool:
      name: 'NKO Agents'
    
    variables:
    - group: Windows10ImageVariables
    - group: AzureKeyVault
    
    stages:
    - stage: VirtualMachineCreation
      jobs:
      - job: VirtualMachineCreation
        steps:
        - task: AzureResourceManagerTemplateDeployment@3
          displayName: 'ARM Template deployment: Windows 10 VM'
          inputs:
            azureResourceManagerConnection: 'WVDSpokeSub_Full'
            subscriptionId: 'ceb791cb-3889-4794-988c-367f7f6ff4f6'
            resourceGroupName: 'RG_WE_Win10ImageBuild_Temp'
            location: 'West Europe'
            csmFile: ARMTemplates/Windows10Template.json
            overrideParameters: '-location "$(Location)" -networkInterfaceName "W10BuildNIC" -subnetName "$(Subnet)" -virtualNetworkId "$(VNetResourceID)" -virtualMachineName "$(VMName)" -virtualMachineComputerName "W10Build" -osDiskType "Premium_LRS" -virtualMachineSize "Standard_B4ms" -adminUsername "W10Admin" -adminPassword $(LocalAdminPassword) -patchMode "Manual"'
[/code]

Firstly, we need to create a YAML file in your repository in Azure DevOps. So, log on to [Azure DevOps](<https://dev.azure.com/>) and go to repo’s:

![Windows 10 Image Series – Part 5 - Repository's](https://www.nielskok.tech/wp-content/uploads/2021/08/image-6-1024x701.png)

After that, create a new file:

![Windows 10 Image Series – Part 5 - Create new YAML File](https://www.nielskok.tech/wp-content/uploads/2021/08/image-7.png)

After that, name the file:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-8.png)

In addition, you can name the file anything you like.  
  
Next up, copy the contents of my [YAML file on my public Github Repo](<https://github.com/Ruthhl3ss/public/blob/main/YAMLTemplates/WVDImageBuildYAML.yml>) and paste it into your YAML file.   
  
Please replace these values in the file:  
  
<<Service Connection Name>>  
<<Subscription ID>>  
<<PAT Token>>  
<<Artifact Build>>  
<<DevOps Org Name>>  
<<DevOps Project Name>>  
  
After that, click on commit:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-9-1024x302.png)

Secondly, we create the new YAML based pipeline. Go to Pipelines and click on new:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-10-1024x636.png)

After that, click on “Azure Repos Git (YAML)”. In addition, you can also connect to the other Git repo options if you prefer that.

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-11.png)

Choose the correct repository:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-12.png)

After that, pick “Existing Azure Pipelines YAML file” (the one we create earlier).

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-13.png)

And select the proper file:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-14.png)

Save the pipeline:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-15.png)

#### Running the Pipeline

Go to your newly created YAML pipeline and click on run:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-16-1024x360.png)

When you run the pipeline, these options are available:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-17.png)

When we look at stages, you can select which parts of the pipeline are run:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-18.png)

Please select the stages you want to use and click on run.  
  
Eventually the pipeline has completed with the following result:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-19.png)

This was Windows 10 Image Series – Part 5, check out the other parts:   
  
0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps) 

1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline) 

2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation) 

3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
3.1 [Windows 10 Image Series – Part 3.1](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/>) (Create test VM from Shared Image Gallery)   
  
4\. [Windows 10 Image Series – Part 4](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>) (SessionHost Deployment via Powershell and Classic Pipelines)  
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML)
