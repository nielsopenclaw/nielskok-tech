---
layout: post
title: "ADFS versus PTA"
date: 2020-05-15
categories: ["Microsoft365"]
tags:
  - "Azure"
  - "Microsoft 365"
  - "ADFS"
  - "Azure"
  - "Azure AD"
  - "Microsoft365"
  - "Office365"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/01/ADFSredundant.jpg"
original_url: "https://www.nielskok.tech/microsoft365/adfs-versus-pta/"
---

In this blog I will talk about ADFS(Active Directory Federation Services) and PTA (Passthrough Authentication). This is my personal opinion about when to use these SSO (Single Sign On) methods.   
  
Let’s first discuss the techniques used here. I will give a basic description of both techniques as I would deploy them. 

### ADFS

ADFS is around since Windows Server 2003 and is therefore the “legacy” method. This is installed as a windows role on Windows Server. After you published this windows role via a proxy of your choosing you can use this service for authentication with in house applications or third parties. In a (HLD) High Level Design it would look like this: 

![](https://www.nielskok.tech/wp-content/uploads/2020/01/ADFSredundant.jpg)

The HLD shows that 4 servers are needed to make this service redundant and you need load balancing services as well. You want make these services redundant because if you don’t and 1 service(proxy, ADFS) is not available, users can’t logon.

### PTA

PTA is around for 2 years now. This service is available through Azure AD. You need to have Azure AD Connect running and you need to install 2 or more agents on servers joined to your domain. If you want you can put these in a separate network for security reasons. After you configured this service in Azure AD you can use this service for authentication with in house applications or third parties. In a High Level Design it would look like this:

![](https://www.nielskok.tech/wp-content/uploads/2020/01/PTARedundant.jpg)

As you can see the PTA method only need 2 servers with agents installed on them to create a redundant setup.

### Conclusion

Each SSO method has its own pro’s and cons. The PTA setup is easier to implement and maintain but requires you to synchronise your Active Directory to Azure AD. ADFS requires you to implement a lot of servers and is not easy to maintain but gives you complete control over the implementation  
  
PTA support the following protocols: “SAML2.0, OAUTH, OATH2.0, OpenIDConnect”. If you don’t mind to synchronize your Active Directory to the Microsoft Cloud (I don’t) and your third party supports SSO via Azure AD I would always choose PTA. This solution is much more elegant than ADFS and easier to maintain. I would like to point out that it is cheaper than ADFS because Azure AD basic is enough to use the basics of these services.   
  
In my experience SysAdmins find it hard to maintain ADFS and that is not so strange. In most projects a Consultant deploys the ADFS farm and when a change needs to be made it’s very likely that it’s the replacement of a SSL certificate 1 or 2 years later. As a result that causes that SysAdmins are not familiar with ADFS.   
  
If you don’t want to synchonize your Active Directory to the Microsoft Cloud or your third party application does not support Azure AD as SSO method you must use ADFS. This gives you complete control over your deployment. If I may give you a tip for deployment: let your SysAdmins help deploy the farm and configure the third parties for authentication. This way they are familiar with the deployment when issues arise or changes need to be made.   
  
Thanks,   
  
Niels