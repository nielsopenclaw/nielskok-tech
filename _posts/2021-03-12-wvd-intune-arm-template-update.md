---
layout: post
title: "WVD Intune ARM Template Update"
date: 2021-03-12
categories: ["Azure Virtual Desktop"]
tags:
  - "Azure"
  - "Azure Virtual Desktop"
  - "Intune"
  - "AzureAD"
  - "Endpoint"
  - "Endpointmanager"
  - "INtune"
  - "Windows10"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/03/image-20.png"
original_url: "https://www.nielskok.tech/azure-virtual-desktop/wvd-intune-arm-template-update/"
---

You can deploy Windows Virtual Desktop (WVD) via an ARM Template. There is a possible update incoming for this template. This update may contain native join to Microsoft Intune (Endpoint Manager) from within the WVD ARM template.  
  
Firstly, I would like to refer to [Sander Rozemuller](<https://rozemuller.com/>) who noticed the change in the ARM template at first. I am going to elaborate and state the changes and where to find them.

#### Find the new template

You can find the template by adding sessionhost through the portal. Firstly logon to [Azure](<https://portal.azure.com/>).  
  
Then go to an existing WVD Hostpool via the WVD portal:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image.png)

Go to your existing Hostpool:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-1.png)

Then click on your existing Hostpool:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-2.png)

After that, click on sessionhosts and click on add:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-4.png)

Fill in all the parameters needed for the deployment and go the “Review + Create” stage:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-5.png)

When all the parameters are filled as required by Microsoft this option becomes available in the Review + create stage:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-7.png)

And that is where you can find the template:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-8-1024x565.png)

#### Changes in the template

I am not going to discuss all the changes made in this template because there is a lot of them. These are the ones that stand out the most:

Firstly, this change in the parameters (Intune and aadJoin):

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-9.png)

They have also added the possibilty to set Availability Zones and Fault/Update Domains:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-10.png)

Let’s discuss the elephant in the room. The possibility (maybe) to add your WVD directly to Azure AD. When you drill down in the deployment. (You can find this when you go to your resource group where you have deployed the template from the previous chapter). For example:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-12.png)

The deployments you see in the picture above are the deployment created by the hostpool creation template. This hostpool creation template contains nested templates and based on which choices you make in the creation process the nested templates are called upon. The main template is the UpdateHostpool deployment:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-13.png)

After that the nested template “vmCreation-linkedTemplate” will be called upon to the deploy the VM’s, join them to the domain (maybe Azure AD), and join them to the hostpool. When you click on this “vmCreation-linkedTemplate”:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-14.png)

You see the components deployed by the nested template:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-16.png)

When you click on template in this window:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-17.png)

This shows the ARM template:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-19-1024x523.png)

I noticed that an extension was added:

![](https://www.nielskok.tech/wp-content/uploads/2021/03/image-20.png)

This may point to a big update in the near future. This is all I noticed for a possible WVD ARM Template Intune Update.

#### References

Other posts:  
[HostPool Registration Key](<https://www.nielskok.tech/windows-virtual-desktop/wvd-hostpool-registration-key/>)  
[Snapshot Managed Disk to Storage Account](<https://www.nielskok.tech/azure/snapshot-managed-disk-to-storage-account/>)