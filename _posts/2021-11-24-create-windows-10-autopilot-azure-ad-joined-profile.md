---
layout: post
title: "Create Windows 10 AutoPilot Azure AD joined profile - NielsKok.Tech"
date: 2021-11-24
author: "Niels Kok"
categories: ["Intune"]
tags: ["Autopilot", "INtune", "Microsoft Intune", "Microsoft365", "Office365"]
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/05/image.png"
description: "This blogpost is about creating a Windows 10 AutoPilot deployment profile based on a Azure AD joined scenario via Microsoft 365 or Microsoft Intune."
original_url: "https://www.nielskok.tech/intune/create-windows-10-autopilot-azure-ad-joined-profile/"
---

# Create Windows 10 AutoPilot Azure AD joined profile

November 24, 2021May 1, 2020 by [Niels Kok](<https://www.nielskok.tech/author/admin/> "View all posts by Niels Kok")

This blogpost is about creating a Windows 10 AutoPilot deployment profile based on a Azure AD joined scenario via Microsoft 365 or Microsoft Intune.

## Creating the profile

Go to <https://devicemanagement.microsoft.com/> and log on.  
  
Go to Devices and to Windows:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image.png)

After that click on “Windows Enrollment”:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-1.png)

We first need to setup automatic enrollment. Click on automatic enrollment:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-2.png)

You need the configure the MDM user scope. You can select a group or enable it for all users. I have test tenant so I have enabled it for all users:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-3.png)

Windows Hello for Business and the Enrollment Status Page are optional to configure:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-4.png)

We first need to create a dynamic group. Go to <https://portal.azure.com/> and go to Azure Active Directory:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-5.png)

Go to groups:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-6.png)

Click on New group:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-7.png)

Name the group and click on add dynamic query:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-8.png)

The dynamic query must be stated like this:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-9-1024x255.png)

This is the query: 
    
    
    (device.devicePhysicalIDs -any (_ -contains "[ZTDId]"))

  
  
This query is needed to have Autopilot devices targetted which where uploaded via CSV upload. If you want to know how to do so click on this link:  
  
<https://www.nielskok.tech/microsoft365/windows-10-autopilot-info-upload-script/>  
  
**For testing purpose to include Virtual Machines I have used the query:**  

    
    
    (device.deviceModel -contains "Virtual")

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-40.png)

  
  
Now it is time to create the AutoPilot profile, click on Deployment Profiles:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-11.png)

Click on “Create Profile”

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-12.png)

Name the policy and select the “Convert all targeted devices to Autopilot”:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-13.png)

Select the following options in the profile:  
In addition, You can choose to allow White Glove OOBE, I enabled it by default.

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-14.png)

At assignments select the dynamic group created earlier:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-15.png)

At review and create check your settings and click on create!  
  
If you have done everything right and uploaded your devices to Microsoft 365 you should see this when you boot your AutoPilot device for the first time:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-41.png)

You enter your company credentials and your device will be enrolled.