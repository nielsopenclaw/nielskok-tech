---
layout: post
title: "Exchange Online Hybrid Connection error HCW8108 -"
date: 2020-05-06
categories: ["Exchange Online"]
tags:
  - "Hybrid"
  - "HybridConnectionWizard"
  - "Exchange Online"
  - "Exchange"
  - "ExchangeOnline"
featured_image: "https://1.bp.blogspot.com/-8N0Hf9C6qGk/Xjs8IrvxwJI/AAAAAAAAlDA/DTCwZXvyAOISLkU4OTbmwRN7gu7vluZQgCK4BGAYYCw/s640/HCW%2BError.png"
original_url: "https://www.nielskok.tech/exchange-online/exchange-online-hybrid-connection-error-hcw8108/"
---

When you configure the Exchange Online Hybrid Connection through the wizard it is possible that you face the error:”HCW8108″. The hybrid connection wizard will state the following error:

![](https://1.bp.blogspot.com/-8N0Hf9C6qGk/Xjs8IrvxwJI/AAAAAAAAlDA/DTCwZXvyAOISLkU4OTbmwRN7gu7vluZQgCK4BGAYYCw/s640/HCW%2BError.png)

The error in the logs will state the following:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-48.png)

## The “Fix”

You probably used your on-premises Exchange Admin Centre to download the Hybrid Connection Wizard. This version of the tool still points to an endpoint from Office 365 which is no longer used.   
  
To download the correct version of the Hybrid Connection Wizard you can do the following:  
  
Go to https://outlook.office.com/ecp and logon  
  
Go to Hybrid:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-49.png)

And click on configure:

![](https://www.nielskok.tech/wp-content/uploads/2020/05/image-50.png)  


After you have run this wizard the error no longer appears.

## References

<https://blog.expta.com/2020/02/hybrid-configuration-service-may-be.html>  
<https://community.spiceworks.com/topic/2260401-hybrid-config-wizard>
