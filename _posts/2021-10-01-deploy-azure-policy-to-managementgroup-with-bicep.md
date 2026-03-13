---
title: "Deploy Azure Policy to ManagementGroup with Bicep"
date: 2021-10-01
layout: post
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/10/image-1.png"
categories:
  - "Infrastructure As Code"
  - "Azure"
  - "DevOps"
tags:
  - "Azure"
  - "DevOps"
  - "Infrastructure as code"
  - "Automation"
  - "AzurePolicy"
  - "BICEP"
  - "IAC"
  - "InfrastructureASCode"
source_url: "https://www.nielskok.tech/infrastructure-as-code/deploy-azure-policy-to-managementgroup-with-bicep/"
---

I have tinkered with this for a bit. So, I thought let’s share it with the world. This blog is about how to deploy azure policy to a managementgroup using the Bicep language.  
  
A management group helps you to assign policy’s to multiple azure subscriptions. Furthermore, new subscriptions can easily be added to the existing management groups. As a result, policy’s are assigned automatically to new subscriptions.  
  
Let’s start!

#### Prerequisites

**Optional** : Install Visual Studio Code with GIT – [Guide for Installation](<https://towardsdatascience.com/installing-github-in-visual-studio-code-for-windows-10-6abd3325ab1>)

Azure Powershell Modules – [Download link](<https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-6.4.0>)  
  
Powershell 7 (recommended) – [Download link](<https://github.com/PowerShell/PowerShell/releases/download/v7.1.4/PowerShell-7.1.4-win-x64.msi>)  
  
Bicep compiler/modules – [Download link](<https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/install>)  


#### Management Group creation

Firstly, we need to create a management group.   
  
We use powershell to do so. Log on to Azure Powershell with the following code:
[code] 
    Connect-AzAccount
[/code]

After that, run this piece of code to create the management group
[code] 
    New-AzManagementGroup -GroupName 'Nielskok.tech-Demo' -DisplayName 'Nielskok.tech DEMO'
[/code]

As as result, this managementgroup is available:

![Deploy Azure Policy to ManagementGroup with Bicep - Management group creation](https://www.nielskok.tech/wp-content/uploads/2021/10/image-1024x399.png)

By default, there are no subscriptions assigned to a management group. We assign the subscriptions later in the process.

#### Policy assigment via Bicep

We use the bicep language to assign an Azure Policy to the management group. The policy which we use is:  
  
‘audit-vm-manageddisks’  
  
This policy checks whether virtual machines uses managed disks. Managed disks are covered by the 99,95% SLA coverage by Microsoft. So, we need to make sure that our virtual machines use these disks.  
  
The syntax of the bicep file looks like this:
[code] 
    targetScope = 'managementGroup'
    
    param policyAssignmentName string = 'audit-vm-manageddisks'
    param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'
    
    resource assignment 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
        name: policyAssignmentName
        scope: managementGroup()
        properties: {
            policyDefinitionId: policyDefinitionID
        }
    }
    output assignmentId string = assignment.id
    
[/code]

Save this file as ‘AzurePolicy.bicep’.

We use the Azure CLI application to deploy this bicep template to Azure. It is fairly simple.  
  
Firstly, use this command to logon with the Azure CLI:
[code] 
    az login
[/code]

After that, use this command to select the right subscription:
[code] 
    az account set --subscription "YOUR SUBSCRIPTION ID"
[/code]

Lastly, deploy the bicep template with this code:
[code] 
    az deployment mg create `
     --location westeurope `
     --management-group-id "YOUR MANAGEMENT GROUP NAME/ID" `
     --template-file "YOUR PATH TO YOUR BICEP Template"
[/code]

This is the result of the template deployment:

![Deploy Azure Policy to ManagementGroup with Bicep - Management group assignment](https://www.nielskok.tech/wp-content/uploads/2021/10/image-1-1024x467.png)

And that is how you deploy Azure Policy to a ManagementGroup with Bicep!

#### References

[Bicep Template for Policy assignment](<https://docs.microsoft.com/nl-nl/azure/governance/policy/assign-policy-bicep?tabs=azure-powershell>)  
  
Other Posts:  
  
[Azure Logon Subscription Menu](<https://www.nielskok.tech/powershellautomation/azure-logon-subscription-menu/>)  
  
[Snapshot Managed disk to storage account](<https://www.nielskok.tech/azure/snapshot-managed-disk-to-storage-account/>)
