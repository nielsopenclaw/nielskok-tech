---
layout: post
title: "Deploy Single Microsoft Store App via Intune"
date: 2021-09-21
categories: ["Intune"]
tags:
  - "Intune"
  - "App deployment"
  - "Application"
  - "Application deployment"
  - "INtune"
  - "MEM"
  - "Microsoft 365"
  - "Microsoft Endpoint Manager"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/09/image-21.png"
original_url: "https://www.nielskok.tech/intune/deploy-single-microsoft-store-app-via-intune/"
---

This blogpost is about how to deploy a single microsoft store app via Intune/Microsoft Endpoint Manager. This method uses a direct URL to the Microsoft Store App to install the application.  
  
Futhermore, this might come in handy when you completely lockdown the machine for App installation. This way you can publish Microsoft Store application via the company portal app.  
  
Let’s get started!

#### Prerequisites

An active Microsoft Endpoint Manager / Intune subscription. Read about the subscription [here](<https://docs.microsoft.com/en-us/mem/intune/fundamentals/licenses>).  
  
A test Windows 10 machine enrolled in Microsoft Endpoint Manager. I like to deploy these configurations to VM’s first. I used Windows 20H2 fully updated to CU9. 

#### Deploy the single Microsoft Store App

Firstly, log on to [Microsoft Endpoint Manager](<https://endpoint.microsoft.com/>).  
  
After that, go to Apps and Windows:

![Deploy Single Microsoft Store App via Intune - Windows Apps ](https://www.nielskok.tech/wp-content/uploads/2021/09/image-11.png)

Next, go to “Add”:

![Deploy Single Microsoft Store App via Intune - Windows Apps add](https://www.nielskok.tech/wp-content/uploads/2021/09/image-12.png)

Select the app type:

![Deploy Single Microsoft Store App via Intune - Select the app type ](https://www.nielskok.tech/wp-content/uploads/2021/09/image-13.png)

For the example we deploy the “Netflix” Microsoft Store App. Fill the following variables:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-18.png)

The variables are:  
  
**Name:** Netflix  
**Description:** NielsKok.Tech  
**Publisher:** Netflix  
**Appstore URL:** https://www.microsoft.com/en-us/p/netflix/9wzdncrfj3tj _(In addition, more information about other apps is discussed later)_  
**Category:** Photo’s & Media  
**Information URL:** https://www.microsoft.com/en-us/p/netflix/9wzdncrfj3tj#activetab=pivot:overviewtab  
**Logo:** Logo can be downloaded [here](<https://store-images.s-microsoft.com/image/apps.46851.9007199266246365.1f6d0339-ecce-4fe5-840a-652cd84111ad.ddef6be7-6304-41c0-a347-1d571b66dfb2?mode=scale&q=90&h=300&w=200>).

Furthermore, if you want to deploy another app, this is what you need to do.   
  
Firstly, go to the [Microsoft Store](<https://www.microsoft.com/en-us/store/apps/windows?rtc=1>) and search for your app:  
  
For example: “Hulu”  
  


![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-15-1024x447.png) ![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-16-1024x458.png)

After that, click on the app and the select the URL:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-17-1024x510.png)

Copy the part of the URL up to the question mark. That is the URL you need to deploy the app in Microsoft Endpoint Manager.  
  
Secondly, click on next:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-19.png)

Lastly, assign a group to make this app available in the Company Portal App:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-20.png)

After assigning the app, open up the company portal app on your test device and this is the result:

![Deploy Single Microsoft Store App via Intune - Result](https://www.nielskok.tech/wp-content/uploads/2021/09/image-21.png)

And that is how you deploy a single Microsoft Store App via Intune!

#### References

Other posts about Microsoft Endpoint Manager:  
  
[Windows 10 Autopilot device upload script with a menu](<https://www.nielskok.tech/intune/autopilot-devices-script-menu/>)  
  
[Deploy fonts via Intune](<https://www.nielskok.tech/intune/deploy-fonts-via-intune/>)