---
title: "Windows 10 Image Series – Part 2 - Artifacts and Applications"
date: 2021-07-23
layout: post
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/07/image-9.png"
categories:
  - "Devops"
  - "Azure"
  - "Azure Virtual Desktop"
tags:
  - "Azure"
  - "Azure Virtual Desktop"
  - "DevOps"
  - "Infrastructure as code"
  - "Artifacts"
  - "AVD"
  - "Imagebuilding"
  - "Windows10"
source_url: "https://www.nielskok.tech/devops/windows-10-image-series-part-2/"
---

In this series I am going to show you how build a Windows 10 Image via Azure Pipelines and DevOps without 3rd party tooling, welcome to part 2!

Part 2 is about artifacts. Uploading the image variables to an artifact, uploading applications to an artifact, downloading applications from the artifacts and creating artifact feeds.

#### Prerequisites

Part 1 of this series needs to be completed. This is the link:  
  
[Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>)  
  
Therefore, the prerequisites from part 1 are also required for this part. So:

Firstly, I am assuming that you have knowledge of Azure DevOps. These are the parts that already need be setup:

In addition, if you don’t have knowledge about Azure DevOps and still want to follow this series please let me know. I might write a blog about the preparing Azure DevOps.

#### Checkout/Skip to the other parts

0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps) 

1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline)  
  
3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
3.1 [Windows 10 Image Series – Part 3.1](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/>) (Create test VM from Shared Image Gallery)   
  
4\. [Windows 10 Image Series – Part 4](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>) (SessionHost Deployment from Image)  
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)  
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML) 

## Building the Windows 10 Image Artifact feeds

#### Creating the Artifact Feed:

Firstly, you need to logon to [Azure DevOps](<https://dev.azure.com/>).  
  
After that, go to Artifacts:

![Windows 10 Image Artifact feeds](https://www.nielskok.tech/wp-content/uploads/2021/06/image-46.png)

Click on, Create Feed:

![Windows 10 Image Artifact feed](https://www.nielskok.tech/wp-content/uploads/2021/06/image-48.png)

After that, name the feed and configure like the screenshot:

![Windows 10 Image Create new artifact feed](https://www.nielskok.tech/wp-content/uploads/2021/06/image-49.png)

It’s available as an active feed:

![Artifact feeds](https://www.nielskok.tech/wp-content/uploads/2021/06/image-50.png)

#### Publish the image variables to the artifact feed

We are going to publish the image name to an artifact. That way, we can grab the name of the image version from the artifact when building a WVD hostpool.  
  
Go to the Azure Pipeline from Part 1:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-53.png)

Edit the pipeline and a job:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-54-1024x424.png)

Search for Universal and this task:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-55-1024x286.png)

Name the task and fill in these variables:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-56.png)

Select the “Image” feed, name the packge and fill in the description. In addition, the description is the image name variable. **NOTE:** Make sure to use lowercase letter and no spaces!

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-57.png)

The metadata for the build is also published:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-59.png)

Save and run the pipeline:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-58-1024x491.png)

When the job is finished, the artifact is uploaded to the feed. After a couple of runs it will look like this:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-60-1024x258.png)

Versioning is applied automatically:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-61-1024x272.png)

#### Upload an application to the Azure Pipelines artifact feed

I use a DevOps agent based on Windows to run my jobs. This is GUI based so I can show you how to upload the an application with installation script to an artifacts. **Please note that your agent must have access to the files that you are uploading!**

For the example I have created a local folder on the DevOps agent with the Microsoft Teams installer and the installation script:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-62.png)

Go back to Azure DevOps, go to pipelines and create a new pipeline:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-63-1024x318.png)

Select:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-64.png)

After that, select your repository:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-65.png)

Start with an empty job:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-66.png)

Name the pipeline and select the agent pool:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-67.png)

Add a “Copy Files” job:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-68-1024x304.png)

Select the Source Folder accordingly for your environment. For me it is “C:\Temp\MicrosoftTeams”.  
  
Furthermore, in a production environment this would be my package share and I would schedule this pipeline for every time the files are updated. You can also directly install from the package share. The artifact is just an example.

The “Target Folder” is: “$(build.artifactstagingdirectory)”:

![](https://www.nielskok.tech/wp-content/uploads/2021/06/image-69.png)

After that, add another job. Search for Universal and add this job:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-1-1024x225.png)

Name the job, name the Artifact and select Azure Pipelines:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-2-1024x189.png)

Save and run the pipeline:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-6-1024x375.png)

#### Install the Package Artifact (Microsoft Teams) via the pipeline on the Windows 10 image

Go to the pipeline which deploys your Windows 10 VM:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-5-1024x312.png)

Edit the Pipeline and add a Azure CLI job:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-7-1024x297.png)

After that, name the job and move it so the jobs run in the following order:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-8.png)

Fill in these variables:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-10-1024x487.png)

Use the following script to download and install the artifact:  
  
**Use the variables accordingly!** (Store the PAT Token in the keyvault) (The Artifact build variable is the run number of the pipeline run.)
[code] 
    $content = {
    #Variables
    $PackageName = 'MicrosoftTeams'
    $ArtifactName = 'teams_installer'
    $ArtifactBuild = '427'
    $DevOpsOrgName = 'YOUR ORGANIZATION NAME'
    $DevOpsProjectName = 'YOUR DEVOPS PROJECT NAME'
    $Path = 'C:\ProgramData\DevOpsInstallFolder\'+$PackageName
    $token = "YOUR PAT TOKEN"
    
    #CreationOfFolders
    If (!(Test-Path 'C:\ProgramData\DevOpsInstallFolder')){
        New-Item 'C:\ProgramData\DevOpsInstallFolder' -ItemType Directory
    }
    
    If (!(Test-Path $Path)){
        New-Item $Path -ItemType Directory
    }
    
        
    $url="https://dev.azure.com/$($DevOpsOrgName)/$($DevOpsProjectName)/_apis/build/builds/$($ArtifactBuild )/artifacts?artifactName=$($ArtifactName)&api-version=6.1-preview.5&%24format=zip"
    
    $token = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes(":$($token)"))
        
    $response = Invoke-RestMethod -Uri $url -Headers @{Authorization = "Basic $token"} -Method Get -ContentType application/zip -OutFile "$Path\Artifact.zip"
    
    Expand-Archive -LiteralPath "$Path\Artifact.zip" -DestinationPath $Path
    
    $installScript = Get-ChildItem $Path -Filter *.ps1 -Recurse
    
    If (Test-Path $installScript.Fullname ){
    
        .$installScript.Fullname
    }
    Else {
    
        Write-Host "Install Script not available"
    }
    
    }
    
    Set-Content -Path .\Artifactinstall.ps1 -Value $content
    
    $VMName = "$(VMName)"
    
    $vm = Get-AzVM -Name $VMName
    $vm | Invoke-AzVMRunCommand -CommandId "RunPowerShellScript" -ScriptPath .\Artifactinstall.ps1
[/code]

After all that. Run the pipeline. The Windows 10 VM is created, the teams artifact is installed, the machine sysprepped, the image is captured and the artifact is published:

![](https://www.nielskok.tech/wp-content/uploads/2021/07/image-9-1024x606.png)

This was Windows 10 Image Series – Part 2, check out the other parts:  
  
0\. [Windo](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>)[ws 10 Image Series – Part 0](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>) (Preparing Azure/Azure DevOps)   
  
1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows VM Pipeline)  
  
3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
3.1 [Windows 10 Image Series – Part 3.1](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/>) (Create test VM from Shared Image Gallery)   
  
4\. [Windows 10 Image Series – Part 4](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>) (SessionHost Deployment from Image)   
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)  
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML)
