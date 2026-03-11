---
layout: post
title: "Device Bulk Actions via Microsoft Intune"
date: 2020-03-09
categories: ["Microsoft365"]
tags:
  - "Microsoft 365"
  - "Intune"
  - "MEM"
  - "Endpointmanager"
  - "INtune"
  - "Microsoft Intune"
  - "Microsoft365"
  - "MIcrosoft Office"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/03/image-4.png"
original_url: "https://www.nielskok.tech/microsoft365/device-bulk-actions-via-microsoft-intune/"
---

This is a new feature in Microsoft Intune. Device bulk actions for devices. An example would be that you have implemented Windows 10 AutoPilot before the naming convention was released and now you are stuck with the naming convention “DESKTOP-XXXXXXX”. You can now rename all devices in bulk.

## Example for a bulk action

Let’s grab the example to rename multiple devices. First of all we need to log in to Microsoft Intune/Endpoint Manager.  
  
Go to the URL https://devicemanagement.microsoft.com and logon.  
  
Go to “devices” and after that go to “all devices”:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-4.png)

In the right hand pane the option “Bulk Device Actions” comes available, it is located right here:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-5.png)

When you click on this button the following options will show:  
  
You can select every OS that you can manage with Microsoft Endpoint Manager and perform bulk actions on them:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-6.png)

In addition, these are the options available for bulk actions:  
NOTE: not all of these options are available for every OS but since my example will cover Windows I am showing these.

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-7.png)

I am renaming my device to NKOWS (NKOWorkStation) – SerialNumber.

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-8.png)

Since I only have 1 device it will look like this:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-9-1024x286.png)

Furthermore you have the option to filter your search results:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-10.png)

And this is it! That’s how you can rename your Windows 10 AutoPilot devices. I haven’t checked what is possible via Powershell, maybe that’s an idea for a later blog.  


## References

<https://docs.microsoft.com/en-us/intune/fundamentals/whats-new>
