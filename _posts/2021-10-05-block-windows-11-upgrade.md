---
layout: post
title: "Block Windows 11 Upgrade"
date: 2021-10-05
categories: ["Intune"]
tags:
  - "Intune"
  - "Microsoft 365"
  - "Endpoint"
  - "Endpointmanager"
  - "INtune"
  - "MEM"
  - "Office365"
  - "Windows10"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/10/image-5.png"
original_url: "https://www.nielskok.tech/intune/block-windows-11-upgrade/"
---

So, Windows 11 is happening. What now? In this blog I will talk about how to block the windows 11 upgrade via Endpoint Manager. Not everybody is ready to upgrade to Windows 11. For example, legacy applications are not tested yet and the adoption program for Windows 11 has not started yet.  
  
I created a lab with a managed device which is managed by Microsoft Endpoint Manager. After that I applied a Windows Update for Business policy to this device. This device runs **Windows 10 Enterprise 21H1**.  
  
What am I investigating? I try to answer the following question. How can you block the Windows 11 upgrade for Endpoint managed Windows 10 devices? This includes a manual upgrade by searching for it using Windows Update. We want to disable that also.

#### Windows Updates For Business

Firstly, I show you the Windows Update for Business policy that I applied to my managed device:

![Windows 10 Upgrade to 11 posibilities](https://www.nielskok.tech/wp-content/uploads/2021/10/image-2.png)

These changes could be applied to make sure the Windows 11 upgrade is not installed.   
  
The Windows 11 upgrade is a feature update. So, let’s first extend the “Feature update deferral period”. The biggest number you can enter is “365”. It’s a limitation by Microsoft. So, set the option to ‘365’:

![](https://www.nielskok.tech/wp-content/uploads/2021/10/image-3.png)

Furthermore, there is also the posibility to use the feature update deployment ring. Set this to the version Windows 10 you like and the devices will stay on that feature update:

![](https://www.nielskok.tech/wp-content/uploads/2021/10/image-6.png)

After that, disable the option: “Option to check for Windows Updates”. This disables the option that users can look for Windows Updates themselfs. That way they can not manually update to Windows 11:  
  
**NOTE:** This feature is in preview at this time of writing

![](https://www.nielskok.tech/wp-content/uploads/2021/10/image-4.png)

As a result, the “Check for updates” button is greyed out:

![](https://www.nielskok.tech/wp-content/uploads/2021/10/image-5.png)

After you disabled the upgrade posibilities you need to make a plan to upgrade to Windows 11 gracefully. So please start with it right away. Define a group of pilot users and upgrade them to Windows 11 to test every aspect of your environment. 

#### References

[Microsoft Doc about Windows 11](<https://docs.microsoft.com/en-us/windows/whats-new/windows-11-plan>)  
  
Other posts:  
[How to configure Windows Update for Business](<https://www.nielskok.tech/microsoft365/windows-10-updates-via-microsoft-intune/>)