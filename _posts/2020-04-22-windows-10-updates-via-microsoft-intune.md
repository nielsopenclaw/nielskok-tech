---
layout: post
title: "Windows 10 Updates via Microsoft Intune"
date: 2020-04-22
categories: ["Microsoft365"]
tags:
  - "Microsoft 365"
  - "Intune"
  - "MEM"
  - "Endpointmanager"
  - "INtune"
  - "Windows10"
  - "Updatesforbusiness"
  - "Updates"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/04/image-48.png"
original_url: "https://www.nielskok.tech/microsoft365/windows-10-updates-via-microsoft-intune/"
---

Updating your Windows 10 devices was always a challenge. The devices were not on the corporate network or for some other reason they were not able to install Windows Updates. Microsoft Intune now partly solves this problem. Configuring Windows Updates is fairly easy. 

## Prerequisites

#### Update Rings

We are going to create update rings. These update rings contain the users. The names of the update ring tell what kind of users they contain:

  * Development Ring (first test users)
  * Test Ring (second test users)
  * Production Ring (production users)



De development ring contain the first test users. For example, this could be the IT department. The development ring get the updates first.  
  
After the development ring users have installed the updates the test ring comes in to place. This ring contains test users. This must be users which work for the business and have no technical background. These users must test every functionality after the updates have been installed.   
  
The production ring is the last ring and this ring contains the rest of the users.   
  
Make sure you collect the information to fill the update rings first and create the groups accordingly. This make the creation of the updates ring a lot easier.

#### Devices

You need to have at least Azure AD joined devices and/or Hybrid AD joined devices. These devices need to run a supported version of Windows 10. More information:  
  
<https://support.microsoft.com/en-us/help/13853/windows-lifecycle-fact-sheet>

## Create Windows 10 Update Rings

We are now going to create the Windows 10 Update Rings.   
  
Firstly, log on the Intune management portal at https://endpoint.microsoft.com/  
  
Go to devices and Update rings:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-48.png)

Click on create profile:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-49-1024x367.png)

Fill the name “WindowsUpdates_DevelopmentRing”

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-50.png)

Configure the Update Ring:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-51.png)

The feature update is set to 365 days (max). It is better to have the feature update planned because this is a high impact update. The Quality Update is immediately installed because this is the development ring. We want them to have the update as soon as possible. Furthermore the deadlines are set to have the updates installed as soon as possible also.  
  
Next assign the group that needs to have this update policy:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-52.png)

Lastly click on create:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-53.png)

We need to follow the same steps for the test ring and the production ring. I am not going to describe them completely. I am going to give you the configuration section.  
  
Test ring configuration:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-54.png)

Production Ring configuration:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-55.png)

If you have configured the profiles correctly the Windows 10 Updates Rings screen should look like this:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-56-1024x285.png)

You are now all set!

## Windows 10 Updates for Business Logs

I have created a log analytics workspace for my updates. I have followed the following tutorial:  
  
<https://www.systemcenterdudes.com/how-to-configure-windows-analytics-with-log-analytics/>  
  
This creates the following dashboard:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-57-1024x444.png)

Here you can get a quick overview.
