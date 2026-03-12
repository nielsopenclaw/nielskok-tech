---
layout: post
title: "Windows 10 Image Series - Part 0 - Prepare Azure DevOps -"
date: 2021-10-17
categories: ["Devops"]
tags:
  - "Azure"
  - "DevOps"
  - "Infrastructure as code"
  - "Automation"
  - "AVD"
  - "Azure"
  - "community"
  - "community]"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/08/image-68.png"
original_url: "https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/"
---

In this series I am going to show you how build a Windows 10 Image via Azure Pipelines and DevOps without 3rd party tooling, welcome to part 0!   
  
On many request I will write about preparing your Azure/Azure DevOps environment. In addition, this will help you start with part 1.  
  
These topics will be discussed in order to prepare you to create part 1:

  * Creating the Azure DevOps environment
  * Initiliaze your GIT repository and connecting it to Visual Studio Code
  * Connect Microsoft Azure to Azure DevOps (Service Connection)
  * Use self hosted agents


#### Prerequisites

There are some but not many prerequisites. These are the things you need:

  * Microsoft Azure tenant (with enough permissions to create an App Registration)
  * Microsoft Azure Subscription


#### Checkout/Skip to the other parts

1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows 10 VM)  
  
2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation)  
  
3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/windows-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
3.1 [Windows 10 Image Series – Part 3.1](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/>) (Create test VM from Shared Image Gallery)  
  
4\. [Windows 10 Image Series – Part 4 ](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>)(SessionHost Deployment from Image)  
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)  
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML) 

### Creating the Azure DevOps environment

Firstly, we need to create an Azure DevOps organisation.  
  
Go to <https://dev.azure.com/>  
  
You will land on this page:  


![Windows 10 Image Series - Part 0 - Prepare Azure DevOps](https://www.nielskok.tech/wp-content/uploads/2021/08/image-36-1024x464.png)

After that, click on sign in and log on with your Azure Active Directory credentials.

![Windows 10 Image Series - Part 0 - Sign in to Azure DevOps](https://www.nielskok.tech/wp-content/uploads/2021/08/image-37-1024x460.png)

In addition, this is not mandatory but if you want to work with your team mates on the same project, you can add them later on.  
  
After that, you will see this notification. This is where we create the Azure DevOps organization:  
  
Name the project and select the project visibility:

![Windows 10 Image Series - Part 0 - Create the Azure DevOps organization](https://www.nielskok.tech/wp-content/uploads/2021/08/image-38.png)

After that, you will land on this page. In addition, an organization name has already been created:

![Windows 10 Image Series - Part 0 - Organization Name](https://www.nielskok.tech/wp-content/uploads/2021/08/image-39-1024x489.png)

Furthermore, If you would like another organization name, please click on new organization:   
  


![Windows 10 Image Series - Part 0 - New Organization](https://www.nielskok.tech/wp-content/uploads/2021/08/image-40-1024x489.png)

Next up! Initiliaze your GIT repository and connecting it to Visual Studio Code

### Initiliaze your GIT repository and connecting it to Visual Studio Code

Firstly, you need to install 2 applications. Git & Visual Studio Code. Here are the links to both applications:

[Git](<https://git-scm.com/download/win>)  
  
[Visual Studio Code](<https://code.visualstudio.com/sha/download?build=stable&os=win32-x64-user>)  
  
**Important!** Please follow [this guide](<https://towardsdatascience.com/installing-github-in-visual-studio-code-for-windows-10-6abd3325ab1>) to install GIT. In addition, the most important part is that you do not miss this part:  


![Windows 10 Image Series - Part 0 - Git installation](https://miro.medium.com/max/503/1*tLwZL96_IujnNNR4ZffDBQ.png)

After that, you need to initialize the repository. Go back to Azure DevOps and click on your project:

![Windows 10 Image Series - Part 0 - Git DevOps project](https://www.nielskok.tech/wp-content/uploads/2021/08/image-41.png)

Click on “Repos”:

![Windows 10 Image Series - Part 0 - Git DevOps repo](https://www.nielskok.tech/wp-content/uploads/2021/08/image-42.png)

After that, click on initialize:

![Windows 10 Image Series - Part 0 - Git initialize devops repo](https://www.nielskok.tech/wp-content/uploads/2021/08/image-44-1024x707.png)

As a result, you see the emptry repo with a read me file:

![Windows 10 Image Series - Part 0 - Git Devops Repos](https://www.nielskok.tech/wp-content/uploads/2021/08/image-46-1024x665.png)

Lastly, we need to clone the repository to Visual Studio Code. Please click on clone:

![Windows 10 Image Series - Part 0 - Git DevOps repo clone](https://www.nielskok.tech/wp-content/uploads/2021/08/image-47-1024x529.png)

After that, click on clone in Visual Studio Code:

![Windows 10 Image Series - Part 0 - Git Clone in VS Code](https://www.nielskok.tech/wp-content/uploads/2021/08/image-48.png)

You get this prompt in VS Code, Click on open

![Windows 10 Image Series - Part 0 - Git VS Code prompt](https://www.nielskok.tech/wp-content/uploads/2021/08/image-50.png)

Select a folder. In addition, don’t choose a folder which is synchronised with OneDrive or an application like that. This causes problems. Trust me, I know.

![Windows 10 Image Series - Part 0 - Git folder select](https://www.nielskok.tech/wp-content/uploads/2021/08/image-51.png)

Furthermore, I always create a seperate GIT folder on my machine to store the repository’s.

After that, you get a logon prompt. Login with your credentials:

![Windows 10 Image Series - Part 0 - Git VS Code login prompt](https://www.nielskok.tech/wp-content/uploads/2021/08/image-52.png)

As a result, the repository is now available in Visual Studio Code:

![Windows 10 Image Series - Part 0 - Git VS Code repos](https://www.nielskok.tech/wp-content/uploads/2021/08/image-53.png)

Furthermore, I like to configure this at the repository setting:

![Windows 10 Image Series - Part 0 - Git VS Code repositry view](https://www.nielskok.tech/wp-content/uploads/2021/08/image-54.png)

As a result, your repos look like this:

![Windows 10 Image Series - Part 0 - Git VS Code repositry view 2](https://www.nielskok.tech/wp-content/uploads/2021/08/image-55.png)

This was the GIT part. 

### Connect Microsoft Azure to Azure DevOps (Service Connection)

Firstly, log on to [Azure Active Directory](<https://portal.azure.com/>).  
  
After that, go to:  


![Windows 10 Image Series - Part 0 - Git Azure Active Directory](https://www.nielskok.tech/wp-content/uploads/2021/08/image-56.png)

Next, go to App Registrations. In addition, copy the “Tenant ID” to a notepad.

![Windows 10 Image Series - Part 0 - Git tenant id](https://www.nielskok.tech/wp-content/uploads/2021/08/image-58.png)

Create a new registration:

![Windows 10 Image Series - Part 0 - Git App reg](https://www.nielskok.tech/wp-content/uploads/2021/08/image-59.png)

After that, fill in the Name and click on Register:

![Windows 10 Image Series - Part 0 - Git App reg name](https://www.nielskok.tech/wp-content/uploads/2021/08/image-61.png)

The app registration is created. In addition, copy the Application (client ID) in the same notepad as the tenant ID:

![Windows 10 Image Series - Part 0 - Git App ID](https://www.nielskok.tech/wp-content/uploads/2021/08/image-62-1024x697.png)

After that, go to Certificates & secrets:

![Windows 10 Image Series - Part 0 - Git Certificates secrets](https://www.nielskok.tech/wp-content/uploads/2021/08/image-63-1024x697.png)

Create a new secret:

![Windows 10 Image Series - Part 0 - Git Certificates secrets 2](https://www.nielskok.tech/wp-content/uploads/2021/08/image-64-1024x694.png)

After that, name the secret and click on add:

![Windows 10 Image Series - Part 0 - Git Certificates secrets naming](https://www.nielskok.tech/wp-content/uploads/2021/08/image-65.png)

In addition, copy the **Value** of the secret to the same notepad as the App ID & the tenant ID.

![Windows 10 Image Series - Part 0 - Git Certificates secrets values](https://www.nielskok.tech/wp-content/uploads/2021/08/image-66-1024x233.png)

Lastly, go to subscriptions in the Azure portal:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-74.png)

After that, select the subscription where you want to deploy resources in via Azure DevOps. For example:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-75.png)

Go to Access Control (IAM):

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-76.png)

After that, click on Add:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-77.png)

Search for the app registration created earlier and give it the permissions it needs:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-78.png)

Furthermore, copy the subscription ID to your notepad with all the information:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-79.png)

The notepad looks like this:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-80.png)

Go back to Azure DevOps and got to your project:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-68.png)

After that, go to project settings:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-69.png)

Then click on “Service Connections’:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-70.png)

Click on “Create service connection”:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-71.png)

After that, select Azure Resource Manager:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-72.png)

Then select Service principal (manual):

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-73.png)

Fill in all the variables we stored in the notepad, you can choose the subscription name and service connection name yourself:  
  
Subscription: –> “Subscription ID from Notepad”  
Subscription Name: –> “Name this yourself”  
Service Principal Id: –> “Application ID from Notepad”  
Service Principal Key: –> “Secret from Notepad”  
Tenant ID: –> “Tenant ID from Notepad”  
Service Connection Name: –> “Name this yourself”

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-81.png)

After that, click on verify, name the service connection and click on add:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-82.png)

  
After that, the service connection is created and your connection to Azure tenant has been setup!

### Use self hosted agents

Lastly, in Windows 10 Image Series part 0, self hosted agents. The newly created Azure DevOps organization do not allow you to use free Microsoft Hosted agents anymore. As a result we need to provide our own agents which will run our Azure Pipelines.  
  
You can run these agents on any OS. If it is just for testing purposes, you can use a VM on your laptop to run your Azure DevOps self hosted agent. **You need to install Azure CLI and Powershell Modules on these agents!**  
  
I prefer Azure Container Instances to run my agents. You can use [this post](<https://bgelens.nl/azuredevops-container-instance/>) to configure this. I use the windows based agents.  
  
For the example I will show you how to use Windows based agents on Windows 10.  


#### PAT Token

Firstly, we need to create a PAT Token. Go to your [Azure DevOps environment](<https://dev.azure.com/>).  
  
After that, go to personal access tokens:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-90-1024x427.png)

Create a new token:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-91-1024x308.png)

  
Name the token, select the expiration and **only** select the Agent Pools permissions:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-92.png)

After that copy the token to a notepad. We need this later in the process:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-93.png)

#### Agent Pool

Secondly, we need to create an “Agent Pool”.   
  
After that, go to your organization settings:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-83.png)

Next, go to “Agent pools”:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-84-985x1024.png)

Create a new agent pool:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-85-1024x415.png)

  
Select the pool type: “Self-hosted”, name the agent pool and select the permissions:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-86.png)

After that, you have a new agent pool:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-87.png)

Click on the pool and click on “New Agent”:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-88-1024x224.png)

  
You now can download the agent. Make sure you download, or move, the downloaded agent to the machine where you want to run the agent. Click on download:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-89.png)

#### Configuring the agent

After that, logon to the machine where you want to run your agent from. Unzip the downloaded agent so you have these files in front of you:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-94.png)

In addition, I usually unpack the zip file to the folder where I am going to run the agent. This is where files and logs are stored. Make sure this is the proper folder to do so.

Open a command prompt/Powershell as administrator and browse to the folder in question:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-95.png)

After that, run the config.cmd and fill in these variables accordingly:  
  
(Use your local user account to run the agent or use a specific account)

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-96.png)

When the variables are entered correctly you should have an agent available in your agent pool:

![](https://www.nielskok.tech/wp-content/uploads/2021/08/image-97-1024x328.png)

This was Windows 10 Image Series – Part 0, check out the other parts:  


1\. [Windows 10 Image Series – Part 1](<https://www.nielskok.tech/devops/windows-10-image-series-part-1/>) (Creating the Windows 10 VM)  
  
2\. [Windows 10 Image Series – Part 2](<https://www.nielskok.tech/devops/windows-10-image-series-part-2/>) (Artifacts & Application Installation)  
  
3\. [Windows 10 Image Series – Part 3](<https://www.nielskok.tech/windows-virtual-desktop/windows-10-image-series-part-3/>) (Shared Image Gallery)  
  
3.1 [Windows 10 Image Series – Part 3.1](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-3-1-deploy-test-vm/>) (Create test VM from Shared Image Gallery)   
  
4\. [Windows 10 Image Series – Part 4 ](<https://www.nielskok.tech/azure-virtual-desktop/windows-10-image-series-part-4/>)(SessionHost Deployment from Image)  
  
5\. [Windows 10 Image Series – Part 5](<https://www.nielskok.tech/azure/windows-10-image-series-part-5/>) (Convert the Image Build pipeline to YAML)  
  
6\. [Windows 10 Image Series – Part 6](<https://www.nielskok.tech/devops/windows-10-image-series-part-6-yaml-bicep-sessionhost-deployment/>) (Deploy Sessionhosts with Bicep and YAML)