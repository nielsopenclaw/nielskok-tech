---
title: "Convert Packer JSON File to HCL2"
date: 2021-09-15
layout: post
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/09/image-8.png"
categories:
  - "Packer"
  - "DevOps"
  - "Infrastructure as code"
tags:
  - "DevOps"
  - "Infrastructure as code"
  - "Packer"
  - "Automation"
  - "AVD"
  - "Azure"
  - "Azure Virtual Desktop"
  - "ConvertFIlestoHCL"
source_url: "https://www.nielskok.tech/packer/convert-packer-json-file-to-hcl2/"
---

Packer has announced that classic JSON files are now supported as legacy files. The limitations within classic JSON make it harder to configure the packer template. That’s why Hashicorp has decided to create HCL support for Packer. This blog is about how to convert your Packer JSON File to HCL2 (Hashi Corp Language 2).   
  
Furthermore, JSON is not completely out of the picture. You can still use these files with the json.pkr file extension. For my packer files I am moving to HCL2 since this is more easy to configure. In addition, you could compare this to Bicep versus ARM Templates.  
  
In this blog I will convert the Packer JSON File to HCL2 using just a Windows 10 machine. I use Powershell to do so. I will convert the default Packer Template as an example.

#### Prerequisites

Windows 10 machine/VM  
  
Download Packer [here](<https://www.packer.io/downloads>).

#### Converting the JSON template

Firstly, you need to download packer, you can do that [here](<https://www.packer.io/downloads>).  
  
After that, unzip the downloaded package and copy that to a folder. I use C:\temp:

![Convert Packer JSON File to HCL2 - Packer executable](https://www.nielskok.tech/wp-content/uploads/2021/09/image-5.png)

Next up is locating the Packer Template, for the example I will put this in the same folder as the packer executable.  
  
Like this:

![Convert Packer JSON File to HCL2 - Packer files](https://www.nielskok.tech/wp-content/uploads/2021/09/image-6-1024x288.png)

If you need an empty template, you can find this default template in my Github [here](<https://github.com/Ruthhl3ss/public/tree/main/Packer-Templates>).  
  
Next up is opening Powershell and navigating the folder which holds Packer and the template file:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-7.png)

After that, run the following line of code (use accordingly to your locations):
[code] 
    .\packer.exe hcl2_upgrade -with-annotations .\PackerTemplate.json
[/code]

The conversion runs and you see that the conversion has completed:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-8-1024x444.png)

**NOTE:** The file ends with .json.pkr.hcl, just to avoid confusion, remove the .json part of the file.  
  
**NOTE:** Some complex configurations don’t convert properly, handle with care.  
  
And you end with these files in your C:\Temp folder:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-9-1024x248.png)

This is part of the syntax of the converted file:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-10-1024x823.png)

#### References

[Packer](<https://www.packer.io/downloads>)  
  
Other Posts:  
[Windows 10 Image Series](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>)  
[Azure Subscription Select Menu in Powershell](<https://www.nielskok.tech/powershellautomation/azure-logon-subscription-menu/>)
