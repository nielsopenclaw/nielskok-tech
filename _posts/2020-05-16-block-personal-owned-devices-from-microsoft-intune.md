---
layout: post
title: "Block personal owned devices from Microsoft Intune"
date: 2020-05-16
categories: ["Microsoft365"]
tags:
  - "Intune"
  - "Microsoft 365"
  - "devices"
  - "Endpointmanager"
  - "INtune"
  - "ManagedDevices"
  - "MDM"
  - "MEM"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/02/image-1024x257.png"
original_url: "https://www.nielskok.tech/microsoft365/block-personal-owned-devices-from-microsoft-intune/"
---

Every administrator for Microsoft Intune has run in to this problem. For example, users install office on their personal devices (mobile and laptops/workstations), Microsoft asks them to log on. They log on with their company credentials and after that the user gets the question whether he wants to let his company manage his device. This is the default setting, most users just click “next, next, finish” to be done with it. This results in the user registering his personal device in the company MDM solution. The purpose of this blog is to block personel owned devices from Microsoft Intune.

## Example personal owned device in Intune

  
For example:

![Personal Device in Intune](https://www.nielskok.tech/wp-content/uploads/2020/02/image-1024x257.png)

This results in having personal and not compliant devices in your Microsoft Intune tenant. Furthermore in my deployments this always means that the device which is not compliant is not encrypted. In other words, this means that we unencrypted devices with company data. This is not something to be taken litely.  
  
Lucky for us there is a solution. It is just a couple of mouse clicks away. Please follow the next steps to configure this in your tenant. 

## Configure device restrictions

Log on to https://devicemanagement.microsoft.com/ and click on devices:

![EndPoint Manager devices](https://www.nielskok.tech/wp-content/uploads/2020/02/image-1.png)

Go to the policy section and click on device restrictions:

![EndPoint Manager Enrollement Restrictions](https://www.nielskok.tech/wp-content/uploads/2020/02/image-2.png)

Click on “Create restriction” and “Device type restriction” 

![EndPoint Manager Enrollment Restrictions](https://www.nielskok.tech/wp-content/uploads/2020/02/image-4.png)

Choose a name and click on next:

![EndPoint Manager Enrollment Restrictions](https://www.nielskok.tech/wp-content/uploads/2020/02/image-5.png)

The platform settings page is where you can set all the restrictions. Block any type you want in this section:

![EndPoint Manager Enrollment Restrictions](https://www.nielskok.tech/wp-content/uploads/2020/02/image-6-1024x329.png)

Assign this to a test group first to make sure everything works as expected!

![EndPoint Manager Enrollment Restrictions](https://www.nielskok.tech/wp-content/uploads/2020/02/image-7.png)

Click on create to create your policy.  
  
If you have a question about the deployment just drop it in the comments  
  
Regards,  
  
Niels

## References

Information about App Protection Policies:  
<https://docs.microsoft.com/en-us/mem/intune/apps/app-protection-policy>  


Block personal devices all together:  
<https://www.nielskok.tech/microsoft365/block-personal-owned-devices-from-microsoft-intune/>