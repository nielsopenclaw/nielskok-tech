---
layout: post
title: "Intune Compliancy Policies"
date: 2020-03-26
categories: ["Microsoft365"]
tags:
  - "Encryption"
  - "Microsoft 365"
  - "Intune"
  - "Bitlocker"
  - "Microsoft Intune"
  - "ConditionalAccess"
  - "Microsoft365"
  - "Compliance"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/03/image-23.png"
original_url: "https://www.nielskok.tech/microsoft365/intune-compliancy-policies-conditional-access/"
---

Intune compliancy policies are nice way to check whether your managed devices are compliant with the policies you have made. For example; you want all your machines to be encrypted, it is possible to check this is enforced through a compliance policy. 

## Example for an Intune Compliancy Policy

The compliancy policy that we are going to create is the compliancy for encryption. After that we create a conditional access policy which only allows compliant machines access.

Let’s create the compliance policy which checks compliant machines.  
  
1\. Log on to <https://devicemanagement.microsoft.com/>

2\. Go to “Devices” and after that go to Compliance policies:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-23.png)

3\. Click on create policy:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-24.png)

4\. Fill in a name and choose the correct platform. After that enable “Require Bitlocker”.

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-27-1024x363.png)

5\. The policy is now created. The last step is to assign the policy to the devices that you want to check for compliancy:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-26.png)

## Conditional Access policy configuration

This is the configuration of the Conditional Access policy and it is pretty straightforward.  
  
It applies to all Apps and the only way to access these Apps is to have a compliant device AND use multi factor authentication.  
  
Go to (from the Endpoint Manager portal) Endpoint security and after that to Conditional Access: 

![This image has an empty alt attribute; its file name is image-30.png](https://www.nielskok.tech/wp-content/uploads/2020/03/image-30.png)

Create a new policy:

![This image has an empty alt attribute; its file name is image-31-1024x228.png](https://www.nielskok.tech/wp-content/uploads/2020/03/image-31-1024x228.png)  


Enter a name to the policy and assign users to the policy. NOTE: please assign a test user first. It is possible to**lock** yourself out!

![This image has an empty alt attribute; its file name is image-32.png](https://www.nielskok.tech/wp-content/uploads/2020/03/image-32.png)

I have selected All cloud apps: 

![This image has an empty alt attribute; its file name is image-34.png](https://www.nielskok.tech/wp-content/uploads/2020/03/image-34.png)

Finally we set the Access Controls: 

![This image has an empty alt attribute; its file name is image-35.png](https://www.nielskok.tech/wp-content/uploads/2020/03/image-35.png)

## Compliancy Policy check

It is now time to check if the policy has applied. Go to the device in the Intune management portal and search for the device on which you applied the compliancy policy.

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-28-1024x462.png)

Here you see that the encryption policy has applied and that the device is compliant. In addition you can click on the policy to check the details:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-29-1024x200.png)

Finally, this is what you will see when you access the cloud app from a non compliant machine:

![This image has an empty alt attribute; its file name is image-22.png](https://www.nielskok.tech/wp-content/uploads/2020/03/image-22.png)

In honest opinion this should be the baseline for all company because this actually ensures that company data is only accessed from company owned devices. Furthermore I would require that the devices need to patched to a certain to get access to your organisation. 

Finally, this is just a basic example. If you need help configuring a complex Compliancy/Conditional Access configuration feel free to contact me!
