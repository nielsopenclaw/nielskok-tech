---
layout: post
title: "Microsoft Teams Quick Start Configuration - NielsKok.Tech"
date: 2021-11-24
author: "Niels Kok"
categories: ["Teams"]
tags: ["Microsoft365", "Office365", "Quickstartguide", "Teams"]
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/04/image-27.png"
description: "Microsoft Teams needs some quick start configuration to make sure, for example, the creation of teams does not get out of hand."
original_url: "https://www.nielskok.tech/teams/microsoft-teams-first-start-configuration/"
---

# Microsoft Teams Quick Start Configuration

November 24, 2021April 16, 2020 by [Niels Kok](<https://www.nielskok.tech/author/admin/> "View all posts by Niels Kok")

This could count as a quick start guide to use Microsoft Teams. Microsoft Teams usage has exploded these days. So I want to help people get started with some basic configuration settings. These settings form a nice baseline in which, for example, the creation of teams has been restricted. By default, everthing in teams is enabled. That is not what most Administrators want. I am not saying you need to restrict everything but when some settings are disabled this is for the better. 

## Configuration items

I am going to talk about these settings, I am going to show you how to configure these later on. These are 5 settings for a first configuration, after this you can let users use Microsoft Teams.  
  
1\. Assign a group for the creation of teams. Otherwise everybody in your organisation can create teams. Before you know it you will have more teams then users in your organisation.   
  
2\. Disable the option to connect Citrix Files, Dropbox, Google Drive & Box to your teams. This way you will keep the data in Microsoft Teams/SharePoint Online.  
  
3\. Disable the usage of 3rd Party Apps untill your company has a policy about the company data being stored in these Apps.  
  
4\. Change the Pinned Apps section to make sure your users have everything they need.  
  
5\. Disable Cloud recording. Allow this only for specific users who need it to do their jobs.

## Configuration Settings 

#### **Microsoft Teams Creation**

I have created a reference to the Microsoft article at the bottom of the page but this is how you should do it in my words:  
  
Firstly, you need to create a group which can create Teams. You can also use an existing group. It does not matter if this group is synced from Active Directory or not.  
  
Secondly, you need to use the Azure AD Preview Powershell module. **Uninstall** the Azure AD module **First!** After that install the **Azure AD Preview** module. If you don’t want to remove modules from your machine use a Windows 10 test device to install the module and run the script.  
  
This is the script:
    
    
    $GroupName = "&lt;SecurityGroupName&gt;" #Use Accordingly
    $AllowGroupCreation = "False"
    
    Connect-AzureAD
    
    $settingsObjectID = (Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ).id
    if(!$settingsObjectID)
    {
    	  $template = Get-AzureADDirectorySettingTemplate | Where-object {$_.displayname -eq "group.unified"}
        $settingsCopy = $template.CreateDirectorySetting()
        New-AzureADDirectorySetting -DirectorySetting $settingsCopy
        $settingsObjectID = (Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ).id
    }
    
    $settingsCopy = Get-AzureADDirectorySetting -Id $settingsObjectID
    $settingsCopy["EnableGroupCreation"] = $AllowGroupCreation
    
    if($GroupName)
    {
    	$settingsCopy["GroupCreationAllowedGroupId"] = (Get-AzureADGroup -SearchString $GroupName).objectid
    }
     else {
    $settingsCopy["GroupCreationAllowedGroupId"] = $GroupName
    }
    Set-AzureADDirectorySetting -Id $settingsObjectID -DirectorySetting $settingsCopy
    
    (Get-AzureADDirectorySetting -Id $settingsObjectID).Values

#### **Disable Citrix Files, Dropbox, Google Drive & Box**

Go to https://admin.teams.microsoft.com/ and log on.   
  
After that go to “Org-wide settings”:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-27.png)

Go to “Teams Settings”

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-28.png)

And from here scroll down to disable the storage settings:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-29.png)

#### Disable 3rd party Apps

I would disable 3rd Party Apps at first because there is simply no telling where your company data is being stored. In addition, you could enable some apps by policy. I will show you how to do so.   
  
I assume you are still in the teams admin panel.  
  
Go to Teams Apps and after that go to Permission Policies:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-32.png)

Go to Third Party Apps:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-33.png)

And select “Block All Apps”:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-34.png)

If you want to allow some specific apps please select “Allow specific apps and block all others”:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-35.png)

#### Change Pinned Apps

I still assume you are in the teams admin panel.  
  
Go to “Teams apps” and “Setup policies”:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-36.png)

Now select the global policy:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-37-1024x354.png)

Here you can see the default Pinned apps:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-38.png)

You can add, for example, Power BI or Power Automate.  
  
Furthermore you can set the settings for users pinning and the upload of custom apps here:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-39.png)

#### Disable Cloud recording

A lot of customer asked me to disable this feature and only allow it for some when explicitly needed for their job. I am going to describe this scenario.  
  
I still assume you are in the Teams Admin panel.  
  
Go to “Meetings” and after that to “Meeting policies”:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-40.png)

Click on the global policy:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-41.png)

Now scroll down to the Cloud Recording settings and turn it off:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-42.png)

Go back to the policy section:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-43-1024x230.png)

Create a new policy:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-44.png)

Set the name and leave the cloud recording setting turned on:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-45.png)

Don’t forget to click on save!  
  
Select the policy and click on manage users:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-46.png)

Assign the users:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-47.png)

Now cloud recording for the organisation is disabled and the setting is enabled for some users.

## References

<https://docs.microsoft.com/en-us/microsoft-365/admin/create-groups/manage-creation-of-groups?view=o365-worldwide>