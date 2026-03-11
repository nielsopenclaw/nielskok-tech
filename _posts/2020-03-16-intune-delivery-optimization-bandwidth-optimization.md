---
layout: post
title: "Intune Delivery Optimization/bandwidth optimization"
date: 2020-03-16
categories: ["Microsoft365"]
tags:
  - "DeliveryOptimization"
  - "BandwidthOptimization"
  - "Win32App"
  - "Microsoft 365"
  - "Intune"
  - "Endpointmanager"
  - "INtune"
  - "Optimization"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/03/image.png"
original_url: "https://www.nielskok.tech/microsoft365/intune-delivery-optimization-bandwidth-optimization/"
---

You will have probably heard about the Intune delivery optimization. In this blogpost I will explain and show the delivery optimization profiles options. I have configured this in my lab. I will walk you through every detail of the implementation.   
  
A good thing to know is that delivery optimization (peer to peer) is enabled by default in Windows 10 1709 and above. But like always we want to control the situation so we setup a configuration profile to do so.

## Preparation

First thing first, let’s start with a preparation block. I have use the following setup:  
  
1\. I have created a seperate VLAN on my home network which has seperate DHCP server and DNS server settings. The DNS server points to my Domain Controller in Azure which I use to host the caching server. This is only for test purposes, in production this never should be a Domain Controller. If your network already has a deployment server use this for cache.  
2\. Create a Windows 10 VM which sits the newly created VLAN so that he can communicate with domain controller for the caching server.  
3\. Enroll the Windows 10 VM via Windows 10 Autopilot so we can configure the configuration profile for delivery optimization.

## Create Delivery Optimization profile

Firstly we need to configure the optimization profile  
  
1\. Log on to https://devicemanagement.microsoft.com/ to enter Microsoft Endpoint manager.  
  
2\. Go to devices and configuration profiles: 

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image.png)

3\. Click on create profile: 

![This image has an empty alt attribute; its file name is image-1.png](https://www.nielskok.tech/wp-content/uploads/2020/03/image-1.png)

4\. Set the profile name, set the plaform, the configuration type and click on create: 

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-11.png)

## Configure Delivery Optimization Profile

The delivery optimization profile has been created and it is now time to configure the profile. These are the options that are available:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-12.png)

Configure the download mode to your liking. This settings depends on your network configuration. For example, if you use VPN in your network to concentrate all connections through your data center you need a different configuration. These are the options you have:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-13.png)

I have configured the “Restrict Peer Selection” to “Subnet Mask”. I have configured this because each site of our customers have a different subnet. As a result the peer selection doesn’t go through the WAN links to other sites.  
  
I have configured the bandwidth with the recommended values from Microsoft:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-14.png)

In addition, I would recommend the values from Microsoft for the bandwidth optimization. These seem very reasonable.  
  
The caching settings are configured like this:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-15.png)

I have configured the recommended values except for “Maximum Cache Age” and “VPN peer caching”. I wanted to extend the age because the recommended value is 7 days but that seemed to short for me. Furthermore I have disabled VPN peer caching because I don’t want WAN links to be affected by caching.  
  
Last but not least these are the setting for the cache server settings:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-16.png)

Note: You should never configured a Domain Controller for caching purpose. This was just an example for testing purposes.  
  
After your configured is complete assign the policy to correct group so the deployment can start.

## Deploy Delivery Optimization Profile

When you want to check if your deploy has succeeded go to the delivery optimization profile and click on device status:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-17.png)

Here you see that the deployment has succeeded:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-19-1024x33.png)

When you want to see the details go to the device in Microsoft Endpoint Manager en search the configuration profile. You will see these configurations:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-20.png)

I have deployed Log Analytics for Windows Updates for Business to check whether the delivery optimization really works. You can check this out on this site:   
  
<https://www.systemcenterdudes.com/how-to-configure-windows-analytics-with-log-analytics/>   
  
The end result will look something like this:

![](https://www.nielskok.tech/wp-content/uploads/2020/03/image-21.png)

Here you see that 31.9% bandwidth savings are reached just by creating this simple profile. (this log analytics has over 500 devices in it)

## References

<https://docs.microsoft.com/en-us/mem/intune/apps/apps-win32-app-management#delivery-optimization>  
<https://docs.microsoft.com/en-us/mem/intune/fundamentals/whats-new>

Regards,

Niels
