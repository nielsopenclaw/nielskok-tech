---
layout: post
title: "Flow Triggered WVD DevOps Build"
date: 2020-10-12
categories: ["Azure Virtual Desktop"]
tags:
  - "Azure Virtual Desktop"
  - "Flow/PowerAutomate"
  - "DevOps"
  - "Flow"
  - "WindowsVirtualDesktop"
  - "WVD"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/10/image-6.png"
original_url: "https://www.nielskok.tech/azure-virtual-desktop/flow-triggered-wvd-devops-build/"
---

This is a blog about a new WVD DevOps build triggered by Flow. This Pipeline creates a new Windows 10 Immutable image which I use for my WVD deployment. To trigger this build you can use Microsoft Flow (PowerAutomate).

#### Prerequisites

You need the Flow premium license, more [information ](<https://docs.microsoft.com/en-us/power-platform/admin/powerapps-flow-licensing-faq>)about the licensing.  
  
Install the mobile Microsoft Flow app.  
  
Furthermore, you need to have permissions in the DevOps project. More [information](<https://docs.microsoft.com/en-us/azure/devops/organizations/security/permissions?view=azure-devops&tabs=preview-page>) about permissions in Azure DevOps.

#### Build the flow

Go the [Microsoft Flow Panel](<https://flow.microsoft.com/>).  
  
Click on create and after that click on “Automated Flow”:

![Create Flow for start WVD DevOps build.](https://www.nielskok.tech/wp-content/uploads/2020/10/image-1.png)

Fill in the name and click on skip. In addition, we pick a trigger later on.

![Flow name for WVD DevOps Build](https://www.nielskok.tech/wp-content/uploads/2020/10/image-2.png)

Select the trigger; “Flow button for mobile”:

![Flow button for mobile trigger for WVD DevOps](https://www.nielskok.tech/wp-content/uploads/2020/10/image-3.png)

After that, click on “new step”:

![New step for flow for WVD DevOps trigger](https://www.nielskok.tech/wp-content/uploads/2020/10/image-4.png)

Search for “Build” and select the option “Queue a new build”:

![Action for new flow for a new WVD DevOps build](https://www.nielskok.tech/wp-content/uploads/2020/10/image-5.png)

You now need to select the pipeline that you want to be triggered by Flow. (in the red rectangle) In addition, you can also select a “Source Branch and Parameters”. (In the blue rectangle):

![Parameters for new build in flow](https://www.nielskok.tech/wp-content/uploads/2020/10/image-6.png)

Lastly, click on “New Step”:

![New step in flow DevOps build](https://www.nielskok.tech/wp-content/uploads/2020/10/image-7.png)

Search for “mobile” and select “Send me a mobile notification”:

![](https://www.nielskok.tech/wp-content/uploads/2020/10/image-8.png)

Enter a name for the notification. Furthermore, you can add dynamic content if you want to. Shown in the blue rectangle:

![](https://www.nielskok.tech/wp-content/uploads/2020/10/image-9-1024x595.png)

Click on save to save the flow.

#### Example for the flow

Open up the Power Automate/Microsoft Flow app on your phone. Your newly created Flow should be available. It looks like this, press the button to queue a new build:

![](https://www.nielskok.tech/wp-content/uploads/2020/10/image-11-576x1024.png)

This is the notification shown that a new build has been created:

![](https://www.nielskok.tech/wp-content/uploads/2020/10/image-10-576x1024.png)

After that, the DevOps Pipeline will start:

![](https://www.nielskok.tech/wp-content/uploads/2020/10/image-12.png)

This is the Win 10 Image Builder:

![](https://www.nielskok.tech/wp-content/uploads/2020/10/image-13-1024x614.png)

And that’s how you create a WVD DevOps build triggered by Flow. Furthemore, I choose to use a mobile app to trigger the build. For example, another possiblity is to use a bot in teams to trigger a new build.

#### References

[Flow Pricing](<https://flow.microsoft.com/en-us/pricing/>)  
  
Other posts about WVD:  
  
[Logoff WVD users via Powershell](<https://www.nielskok.tech/windows-virtual-desktop/wvd-logoff-users-hostpool-powershell/>)  
  
[Set Drain mode WVD hostpool](<https://www.nielskok.tech/windows-virtual-desktop/wvd-set-drain-mode-powershell/>)