---
layout: post
title: "Add Microsoft Store For Business to Intune"
date: 2020-05-20
categories: ["Microsoft365"]
tags:
  - "Intune"
  - "Microsoft 365"
  - "Microsoft Intune"
  - "Microsoft Store"
  - "Microsoft365"
  - "Office365"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/05/image-72.png"
original_url: "https://www.nielskok.tech/microsoft365/add-microsoft-store-for-business-to-intune/"
---

You want to manage Microsoft Store Apps for Windows 10 via Intune. But how do you add the Microsoft Store for Business to Intune? In this blogpost I will show you.  


## Add the Microsoft Store

Log on to Microsoft Intune at [Link](<https://portal.azure.com/#blade/Microsoft_Intune_DeviceSettings/ExtensionLandingBlade/overview>)  
  
Go to Client Apps:

![Client Apps in the Microsoft Intune Portal.](https://www.nielskok.tech/wp-content/uploads/2020/05/image-72.png)

Go to the Microsoft Store for Business:

![Microsoft Store for Business](https://www.nielskok.tech/wp-content/uploads/2020/05/image-73.png)

First you will need to enable the Microsoft Store for Business. After that you need to need to login using the link on the page:

![Intune Settings Microsoft Store for Business](https://www.nielskok.tech/wp-content/uploads/2020/05/image-74-1024x568.png)

When click on the link you need to sign in at the top right corner of the page:

![Sign in page Microsoft Store for Business](https://www.nielskok.tech/wp-content/uploads/2020/05/image-75.png)

Log in with the account that you use to manage Intune. There are some more settings we need to enable in the Microsoft Store for Business. Firstly, click on “Manage” in the Microsoft Store for Business:

![Manage for Microsoft Store for Business](https://www.nielskok.tech/wp-content/uploads/2020/05/image-76.png)

Go to settings:

![Settings in MIcrosoft Store for Business](https://www.nielskok.tech/wp-content/uploads/2020/05/image-77.png)

The next step is to click on distribute devices:

![Distribute private store](https://www.nielskok.tech/wp-content/uploads/2020/05/image-78.png)

Furthermore we need to activate these services:

![Activate Services](https://www.nielskok.tech/wp-content/uploads/2020/05/image-79-1024x261.png)

Now go back to Intune and sync the Microsoft Store for Business with Microsoft Intune:

![Sync settings](https://www.nielskok.tech/wp-content/uploads/2020/05/image-80-1024x542.png)

Note that this is going to change in August 2020 because this portal is going to be depreciated. Therefore from that point you will need to configure this in the endpoint manager portal. 

## References

<https://docs.microsoft.com/en-us/microsoft-store/microsoft-store-for-business-overview>  
  
<https://www.nielskok.tech/microsoft365/windows-10-autopilot-tips-tricks/>