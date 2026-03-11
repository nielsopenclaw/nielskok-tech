---
layout: post
title: "Microsoft Office 365 Configuration via Intune"
date: 2020-03-05
categories: ["Microsoft365"]
tags:
  - "Profiles"
  - "Microsoft 365"
  - "Intune"
  - "ADMX"
  - "Office 365 Pro Plus"
  - "Microsoft Intune"
  - "Microsoft365"
  - "Office365"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/03/image.png"
original_url: "https://www.nielskok.tech/microsoft365/microsoft-office-365-configuration-via-intune/"
---

Since a couple of months Microsoft Intune offers the possibility to use ADMX templates to configure Microsoft Office 365 Pro Plus. Earlier on this was possible via scripting to configure Microsoft Office. This is not ideal because our managed services department had a hard time controling this. When I read about the ADMX templates I was eager to start testing. I will share my experiences with you.  
  
In the examples I assume that the users that you configure these options for work on fat clients.

## Preparation for Intune

For the examples I will elaborate on these parts of the Microsoft Office 365 suite:  
  
Outlook  
OneDrive  
  
First things first, let’s start with an example to configure a profile with an ADMX template:  
  
1\. Log on to https://devicemanagement.microsoft.com/ to enter Microsoft Endpoint manager.  
  
2\. Go to devices and configuration profiles:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image.png)

3\. Click on create profile:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-1.png)

4\. Set the profile name, set the plaform, the configuration type and click on create:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-2.png)

The new configuration profile is now created. After that, there are 3 options available:  
  
Properties  
Settings  
Assignment

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-3.png)

You can use the properties option to change the name and description for configuration profile.  
The settings option is for the ADMX settings.  
With the assignment option you can add the profile to users or devices.  


## Example for Microsoft Office 365 Outlook

Firstly I will start with Outlook since this application has the most impact for the most users.  
  
I would start with these options:  
  
Automatically configure profile based on Active Directory Primary SMTP address once._– “Set to enabled”_  
Disable First Run Movie – “ _Set to enabled”_  
Disable Office First Run on application boot – “ _Set to enabled”_  
Calendar week numbers.- _“Set to enabled”_  
Turn off Coming Soon – “ _Set to enabled”_   
Disable Opt-in Wizard on first run – “ _Set to enabled”_  
Disable shared mail folder caching – “ _Set to enabled”_  
Download shared non-mail folders – “ _Set to disabled”_  
  
I would disable the shared folder caching because Outlook doesn’t handle this well when multiple users make a lot of changes to same mailbox. For example: When 5 users work in the same shared mailbox and they catagorize e-mail, it takes a while to sync when using cache mode. If you don’t use cache mode for the shared mailboxes the changes are visible instantly for all 5 users.   
  
It is always possible to configure more options. The option that I suggest form a basic configuration for Outlook which I find fitting for our users. There are always specific cases for clients.

## Example for Microsoft Office 365 OneDrive

Secondly we are going to configure OneDrive via a policy. In addition, I would advise you to create a seperate policy for OneDrive since not all users can or may use OneDrive.   
  
I would start with these options:  
  
Coauthor and share in Office desktop apps – “ _Set to enabled”_  
Disable the tutorial that appears at the end of OneDrive Setup – “ _Set to enabled”_  
Prevent users from changing the location of their OneDrive folder – “ _Set to enabled”_  
Prevent users from redirecting their Windows known folders to their PC – “ _Set to enabled”_  
Require users to confirm large delete operations – “ _Set to enabled”_  
Silently move Windows known folders to OneDrive – “ _Set to enabled”_ (Don’t forget to enter your tenant ID)  
Silently sign in users to the OneDrive sync client with their Windows credentials – “ _Set to enabled”_  
Use OneDrive Files On-Demand – “ _Set to enabled”_   
  
These options form a nice basis for the OneDrive configuration and help the user get rid of all the first run movies and such. Furthermore these policy’s help protect their data and help them to collaborate with their co-workers.

## Conclusion

We have created a new configuration profile for Windows 10 devices which we use to configure Microsoft Outlook and Microsoft OneDrive. I have shown some examples and elaborated on that.   
  
In conclusion, I think you will agree that configuration profiles are a nice addition for Microsoft Intune and if you have any questions don’t hesitate to ask.

## References

What’s new in Intune: <https://docs.microsoft.com/en-us/intune/fundamentals/whats-new>   
<https://www.scconfigmgr.com/2019/07/10/extended-administrative-templates-in-microsoft-intune/>
