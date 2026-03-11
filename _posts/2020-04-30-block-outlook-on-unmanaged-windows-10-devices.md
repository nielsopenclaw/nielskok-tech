---
layout: post
title: "Block Outlook on Unmanaged Windows 10 Devices"
date: 2020-04-30
categories: ["Intune"]
tags:
  - "Microsoft 365"
  - "Intune"
  - "Teams"
  - "MEM"
  - "App Protection Policy"
  - "Office"
  - "INtune"
  - "Outlook"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/04/image-58.png"
original_url: "https://www.nielskok.tech/intune/block-outlook-on-unmanaged-windows-10-devices/"
---

When you move to Exchange Online the connectivity to your mailboxes is no longer limited by your firewall. Microsoft enables everything by default. So if you don’t want users to configure Outlook on their unmanaged Windows 10 devices you need create policy to do so. To block Outlook on unmanaged Windows 10 devices you need to create an app protection policy.  
  
This business case was about using the teams client but blocking the Outlook client.

## Prerequisites

The devices that you want to block Outlook on need to be Azure AD registered otherwise the policy won’t work. You can enforce this through a Terms of Use Conditional Access Policy:  
  
<https://docs.microsoft.com/nl-nl/azure/active-directory/conditional-access/terms-of-use>  
  
Furthermore you need to have the appropiate licenses.

## App Protection Policy

Go to <https://endpoint.microsoft.com/> and log on.  
  
Go to Apps and after that go to App Protection Policies:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-58.png)

Create a new policy for Windows 10:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-60.png)

Name the policy and select “Without Enrollment”:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-61.png)

Select “Add” and Desktop Apps After that:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-62-1024x318.png)

Enter the following data:  
  
**Name:** Outlook  
**Publisher:** CN=Microsoft Corporation, O=Microsoft Corporation, L=Redmond, S=Washington, C=US  
**Productname:** Microsoft Outlook  
**File:** Outlook.exe  
**Min Version:** *  
 **Max Version:** *  
 **Action:** Deny  
  
It needs to look like to this (2 screenshots):

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-63.png) ![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-64.png)

Select block and check whether your corporate identity has been entered:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-65.png)

I have left the advanced settings to default but you can edit them to enforce a more complex Windows Hello passcode. For example:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-66.png)

Assign the policy to a group. Please test first:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-67.png)

And click on create.

## Testing the Policy

I have built a Windows 10 Hyper V machine for test purposes. This machine is not Azure AD joined. This machine is Azure AD registered through the Terms of Use Conditional Access policy stated in the prerequisites.  
  
I have downloaded Microsoft Office 365 Pro Plus from https://portal.office.com/ with my user account.  
  
When I start outlook as this test user and try to configure my corporate profile I get this error:

![](https://www.nielskok.tech/wp-content/uploads/2020/04/image-69.png)

**NOTE:** This only works on newly enrolled machines. Machines that already have been registered can configure outlook. So these machines need to be re-registered.
