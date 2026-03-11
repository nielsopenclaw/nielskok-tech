---
layout: post
title: "Windows 10 AutoPilot Tips & Tricks"
date: 2020-01-22
categories: ["Microsoft365"]
tags:
  - "Microsoft 365"
  - "1903"
  - "Intune"
  - "Autopilot"
  - "Endpointmanager"
  - "INtune"
  - "1909"
  - "Microsoft365"
original_url: "https://www.nielskok.tech/microsoft365/windows-10-autopilot-tips-tricks/"
---

Windows 10 AutoPilot is a magnificent piece of software to deploy your Windows 10 machines. I have deployed multiple customers and I will share some tips & tricks. We used Microsoft Intune, soon to be Microsoft Endpoint Manager, stand alone to do so.   
  
If you are new to Windows 10 Autopilot and just want to deploy the very basics for testing I would recommend this guide: [Link](<https://docs.microsoft.com/en-us/intune/enrollment/enrollment-autopilot>)   
  
First of all I will state some “Do’s” and some “Don’ts”.

## Do’s

1\. Use Windows 10 1903 and above. There are a couple of reasons I would prefer at leatst Windows 10 1903:

  * The upgrade from Windows 10 1809 to 1903 is very big (around 3,5 GB). If you have a lot of small offices with low bandwidth it will be hard to get everyone to upgrade to the latest version
  * If you deploy Win32 apps you can enforce them in the enrollment status page. Windows 10 1903 and above support this feature.
  * In Windows 10 1903 and above the applications specified at the enrollment status page are installed first. At Windows 1809 and lower this is not the case. For example, this causes that configuration always runs differently for every deployment. This is hard for troubleshooting. 
  * The enrollment status page has more features and is more stable. For example, I had an issue with a Windows 10 1809 deployment. I didn’t change anything but upgrade to client to 1903 and the deployment ran smoothly again.



2\. Your hardware vendor can supply you with the AutoPilot devices information.,as a result, the devices are registered in your Intune tenant. They can also add it automatically to your tenant if you want to. You can also ask your hardware vendor to supply your hardware with Windows 10 1903, for example HP can do this.

3\. When you use the Enrollment status page, use it to block the usage of the machine and to deploy Office. It is best practises to set the time-out to 90 minutes. In addition, I if you have more then 6 of these try to select the most important ones. I would select the most important one’s. For example; Antivirus, Office and a core application.

4\. Use the ADMX profiles to configure as much as possible. When this is not possible reside to a powershell script to configure what you want. The ADMX profiles are updated frequently. Each week I see new options.

5\. Use branding for your tenant. I configure this for all customer, as a result the end-user always recognizes the company logo. This way they are more trusting towards the enrollment of their windows device.

## Don’ts

1\. Don’t use Windows 1809 or lower, it is supported but in my experience the deployment is a little buggy and troobleshooting is hard because of the reasons stated above.

2\. Powershell scripts are in my experience not ment for application installation. You can use them inside a Win32 App but don’t install applications via “Powershell” scripts. I have had an issue where the installation of a Win32 App ran at the same time as the Powershell Script installation. The windows installer was in use by the Powershell script when the Win32 App was deployed, after that the deployment was stuck. So I would advise you not to use Powershell scripts like that.

3\. Don’t deploy applications that are over 4 GB via Win32 Apps. I have tried, I couldn’t get it to work properly. I would advise a maximum of 2 GB.

4\. Try to use the built-in tools as much as possible. Don’t try to customize your way to serve the functionality that you or your end users need. The customization with scripting makes troubleshooting hard for those that have to maintain the solution.

## Tips & Tricks

Here are some Tips & Tricks:

1\. Dependencies are great but you can’t use them to make a Win32App dependant on a Line of Business Application. You can make a Win32App dependant on a Win32App. The same goes for the line of business application. 

2\. If you plan to use Windows update rings via Intune use more then one. Preferably 3, in the first ring you should put your test users and install updates as soon as they come available. The second ring should contain more users. The last ring should contain the rest of the rest. You should have checks in place to make sure updates are installed and are not causing problems. For example, mark test users in your ITSM tool so ServiceDesk Engineer can quickly spot them.

3\. If you plan to use Single Sign On and the Google Chrome browser install the “Windows 10 Accounts” extension. This extension makes sure of the Single Sign On experience. I will create a blog how to do so later on, so stay tuned!

4\. Use delivery optimization for deployment for your updates. In my experience this saves around 25% of bandwidth.

5\. Enable Self Service Password Reset. This way users can reset their own password via the Windows 10 logon screen. This helps reduces calls to the helpdesk.

6\. Use update compliance in combination with Log Analytics in Azure. For example, this way you can create a Power BI dashboard to check the update statusses of your machines. 

## General Information

In addition I will state some usefull websites I use for deployment guides and general information:

Peter van der Woude <https://www.petervanderwoude.nl/>

System Center Dudes <https://www.systemcenterdudes.com/>

Peter Klapwijk <https://www.inthecloud247.com/>
