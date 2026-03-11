---
layout: post
title: "Windows 10 AutoPilot Hybrid AD Join vs Azure AD Join"
date: 2020-05-06
categories: ["Intune"]
tags:
  - "Hybrid"
  - "Microsoft 365"
  - "HybridAdJoin"
  - "Intune"
  - "Autopilot"
  - "MEM"
  - "Endpointmanager"
  - "AzureADJoin"
featured_image: "https://docs.microsoft.com/nl-nl/azure/active-directory/devices/media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png"
original_url: "https://www.nielskok.tech/intune/windows-10-autopilot-hybrid-ad-join-vs-azure-ad-join/"
---

If you are looking to deploy Windows 10 AutoPilot you are going to have to make a choice. Are you going to use an Hybrid AD Join or a Azure AD Join deployment? I am going to give you my thoughts on a Windows 10 Autopilot Hybrid AD Join vs Azure AD join. Furthermore I am going to elaborate on which deployment I am going to use in which situation.  
  
In addition, these are my build guides for Hybrid AD Join & Azure AD Join:  
  
[Hybrid AD Join Build Guide](<https://www.nielskok.tech/intune/create-windows-10-autopilot-hybrid-azure-ad-joined-profile/>)  
  
[Azure AD Join Build Guide](<https://www.nielskok.tech/intune/create-windows-10-autopilot-azure-ad-joined-profile/>)  


### Hybrid AD Join

Firstly, let’s talk about the architecture of a Windows 10 Autopilot Hybrid AD Joined deployment. This a high level design of a Hybrid AD Joined deployment:

![Hybride Azure AD-gekoppelde apparaten](https://docs.microsoft.com/nl-nl/azure/active-directory/devices/media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

In an Hybrid AD Join deployment the device needs to be able to contact the Microsoft Cloud and the Domain Controller. This is needed because the device needs to be joined to Azure AD and the “normal” Active Directory. This has advantages and disadvantages.  
  
The main advantage of the Hybrid AD Joined devices is that you can manage your devices via Intune Policies and Group Policy objects. For example, you can enforce Windows Updates via Microsoft Intune and use your current GPO’s to manage the devices. This scenario decreases the time to configure Microsoft Intune dramatically because your current configuration for computer settings and users settings still can be used.   
  
The main disadvantage of the Hybrid AD Joined devices is that you need to enroll your devices in a network where a Domain Controller is present. To perform a domain join the device needs to be able to contact a Domain Controller.  
  
I would choose the Hybrid AD Join method in the following situation. Your organization has no problem with the enrollment in the network where a domain controller can be contacted. Your organization doesn’t need to enroll machine by users from home. Last but not least, if your organization wants to enroll machine from Intune fast and use the current GPO configurations.

### Azure AD Join

Secondly, let’s talk about the architecture of a Windows 10 Autopilot Azure AD Joined deployment. This a high level design of a Azure AD Joined deployment:

![Azure AD-gekoppelde apparaten](https://docs.microsoft.com/nl-nl/azure/active-directory/devices/media/concept-azure-ad-join/azure-ad-joined-device.png)

When a device is enrolled in Azure Active Directory via Windows 10 AutoPilot only the Microsoft Cloud needs to be contacted. The deployment can be done from every location which has an internet connection and enough bandwidth. This also has it’s advantages and disadvantages.  
  
The main advantage is that devices can be enrolled from every location which has an internet connection. If you offices don’t have the capabilities to deployment hundreds of machine you can do this at another location. You can send devices via mail to your users. Your users can enroll them from home because they only need an internet connection.   
  
The main disadvantage is that devices are enrolled only in Azure Active Directory. This means that you need to reconfigure your current GPO’s in to Intune configurations. You need to create policies to automatically configure your devices. This will increase the time to configure and test the deployment.  
  
The Azure AD Join deployment fits well for organizations which have a lot of small offices. The enrollment only requires an internet connection which most small offices have. Furthermore, users can enroll devices from home and you don’t any servers to deploy these devices.

### Conclusion

Which deployment should you pick? This depends very much on your situation.  
  
For example, does your organization have a couple of large offices? Are these offices network able to contact a domain controller? Is your current Windows 10 configuration controlled by GPO’s and do you want to keep it that way? In my opinion you need to pick an Hybrid AD Joined Windows 10 deployment.  
  
Another example, does your organization have hundred of small offices with limited bandwidth? Do you want to have users enroll their machines from home? Do you want to be dependent on the cloud only for the deployment of your machines? Then you should pick an Azure AD joined deployment.  
  
The choices are dependent on your situation. You need to assess whether your current configuration needs an update/upgrade. Please perform an assessment on your current deployment and configuration. Is it absolutely necessary to perform your configuration from GPO’s? Your log on times will decrease if you put your configurations in the deployment in stead of policies. Microsoft Intune offers almost all GPO’s in their configuration polices. You can check out this link for an example:  
  
<https://www.nielskok.tech/microsoft365/microsoft-office-365-configuration-via-intune/>  
  
So, to wrap it all up. The choice for the deployment is very much dependent on your situation. Please consider every aspect of your current deployment and decide whether you are going to keep it that way.

### References

<https://docs.microsoft.com/en-us/azure/active-directory/devices/concept-azure-ad-join-hybrid>  
<https://docs.microsoft.com/en-us/azure/active-directory/devices/concept-azure-ad-join>  
  
**Build guides:**  
Hybrid AD Join:  
<https://www.nielskok.tech/intune/create-windows-10-autopilot-hybrid-azure-ad-joined-profile/>  
Azure AD Join:  
<https://www.nielskok.tech/intune/create-windows-10-autopilot-azure-ad-joined-profile/>
