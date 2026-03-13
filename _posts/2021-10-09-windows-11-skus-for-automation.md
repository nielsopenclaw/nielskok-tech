---
title: "Windows 11 SKU's for Automation"
date: 2021-10-09
layout: post
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/10/image-9.png"
categories:
  - "Azure Virtual Desktop"
  - "Azure"
  - "Infrastructure as code"
tags:
  - "Azure"
  - "Azure Virtual Desktop"
  - "Infrastructure as code"
  - "ARM"
  - "AVD"
  - "AzureVirtualDesktop"
  - "BICEP"
  - "MEM"
source_url: "https://www.nielskok.tech/azure-virtual-desktop/windows-11-skus-for-automation/"
---

Need to upgrade your Windows 11 Sku’s in your automation like Packer, Azure Image Builder or manual Bicep/ARM Templates? Use these tables below in order to find the correct information for your scripts.  
  
I will list the Windows 11 SKU’s for these versions:

  * Windows 11 PRO ZH-CN – Gen2
  * Windows 11 PRO N – Gen2
  * Windows 11 PRO – Gen2
  * Windows 11 Enterprise N – Gen2
  * Windows 11 Enterprise multi-session – Gen2
  * Windows 11 Enterprise – Gen2


#### Windows 11 SKU Tables

Windows 11 PRO ZH-CN – Gen2

Publisher|  microsoftwindowsdesktop   
---|---  
Offer|  windows-11   
SKU|  win11-21h2-pro-zh-cn   
  
Windows 11 PRO N – Gen2

Publisher |  microsoftwindowsdesktop   
---|---  
Offer |  windows-11  
SKU |  win11-21h2-pron   
  
Windows 11 PRO – Gen2

Publisher |  microsoftwindowsdesktop   
---|---  
Offer |  windows-11   
SKU |  win11-21h2-pro   
  
Windows 11 Enterprise N – Gen2 

Publisher |  microsoftwindowsdesktop   
---|---  
Offer |  windows-11   
SKU |  win11-21h2-entn  
  
Windows 11 Enterprise multi-session – Gen2

Publisher |  microsoftwindowsdesktop   
---|---  
Offer |  windows-11   
SKU |  win11-21h2-avd  
  
Windows 11 Enterprise – Gen2

Publisher |  microsoftwindowsdesktop   
---|---  
Offer |  windows-11   
SKU |  win11-21h2-ent  
  
You might ask yourself, how can I find the sku’s myself?  
  
I first tried Powershell, but this did not give me the feedback I wanted. Because, at this time of writing, the feedback from this command is: 

![Windows 11 SKU's for Automation - Powershell command](https://www.nielskok.tech/wp-content/uploads/2021/10/image-7.png)

  
I use the Azure portal in order the find the correct SKU.  
  
Let me show you.  
  
When you create a Virtual Machine, at the “review and create” stage, you can click on “Download a template for automation”

![Windows 11 SKU's for Automation - Manual SKU collection](https://www.nielskok.tech/wp-content/uploads/2021/10/image-8.png)

After that, you will find the image sku’s:

![](https://www.nielskok.tech/wp-content/uploads/2021/10/image-9.png)

And that’s how you find the Windows 11 SKU’s for Automation.

After the comment from Tom Hickling I have added the following. If you run this code:
[code] 
    Get-AzVMImageSku -Location westeurope -PublisherName MicrosoftWindowsDesktop -Offer windows-11 | Select Skus
[/code]

This the result:

![](https://www.nielskok.tech/wp-content/uploads/2021/10/image-10-1024x223.png)

#### References

[More information about Windows 11](<https://docs.microsoft.com/en-us/windows/whats-new/windows-11-prepare>)  
  
[Direct downloadlink for Windows 11](<https://www.microsoft.com/en-us/software-download/windows11>)  
  
[Windows Image Series](<https://www.nielskok.tech/windows-10-image-series/>)
