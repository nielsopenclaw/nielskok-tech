---
layout: post
title: "SQL ODBC Driver via Intune"
date: 2021-10-01
categories: ["Intune"]
tags:
  - "Intune"
  - "Microsoft 365"
  - "Powershell Automation"
  - "Application deployment"
  - "Automation"
  - "INtune"
  - "MEM"
  - "Microsoft 365"
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/09/image-41.png"
original_url: "https://www.nielskok.tech/intune/sql-odbc-driver-via-intune/"
---

This post is about deploying the SQL ODBC Driver via Intune with the required runtimes. It took myself a while to get this working. So, I thought let’s share this with the world.  
  
Firstly, I have created a package which contains the ‘VC_Redist.x64.exe’ and the ‘msodbcsql.msi’, installs them silently, creates a log file and has a custom detection method. After that, you can directly configure a SQL ODBC connection.

#### Prerequisites

This are the prerequisites for using this configuration:

  * Intune/Microsoft Endpoint Manager enrolled device
  * Proper licensing
  * Win32App packaging tool. [Downloadlink](<https://github.com/microsoft/Microsoft-Win32-Content-Prep-Tool/archive/refs/tags/1.8.3.zip>)


#### Creating the package

This part guides you through creating the package. This process uses the Win32App tool. Make sure you downloaded this application in the prerequisites.  
  
Firstly, create a folder where all the package files will reside. All the files in this folder will be captured in the package. Make sure no unnecessary files are in this folder.  
  
I called this folder SQLODBC and it contains these files:

![SQL ODBC Driver via Intune - Installation FIles](https://www.nielskok.tech/wp-content/uploads/2021/09/image-22.png)

The custom scripts are available in my github and I added the links for the installer files:

Next up, open a Powershell window and browse to the Win32App Tool:

![SQL ODBC Driver via Intune - Win32App Tool](https://www.nielskok.tech/wp-content/uploads/2021/09/image-23-1024x260.png)

In addition, I downloaded and extracted the Win32App tool to the C:\temp folder.   
  
After that, run the Win32App Tool and specify the folders accordingly:

![SQL ODBC Driver via Intune - Package build info](https://www.nielskok.tech/wp-content/uploads/2021/09/image-25.png)

As a result, this is package is created:

![SQL ODBC Driver via Intune - Package Intunewin file](https://www.nielskok.tech/wp-content/uploads/2021/09/image-26.png)

#### Add the package to Intune

Next up, adding the package to Intune.  
  
Firstly, log on to [Microsoft Intune](<https://endpoint.microsoft.com/>).  
  
After that, go to “Apps” and “Windows”:

![SQL ODBC Driver via Intune - Add the app to intune](https://www.nielskok.tech/wp-content/uploads/2021/09/image-27.png)

Next, click on “Add”:

![SQL ODBC Driver via Intune - Add the app to intune 2](https://www.nielskok.tech/wp-content/uploads/2021/09/image-28.png)

Select the proper App type:

![SQL ODBC Driver via Intune - Select the app type](https://www.nielskok.tech/wp-content/uploads/2021/09/image-29.png)

Follow these steps to configure the app properly.  
  
Firstly, select the .intunewin file on your local machine:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-30-1024x260.png)

Fill out the App Information:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-31.png)

After that, fill out these install commands and restart behavior:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-32.png)

In addition, the install commands in text for easier copy and paste actions:

  * Install.cmd
  * C:\ProgramData\AppDeployment\SQLODBC\SQLODBCUninstall.ps1


Requirements:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-33.png)

After that, configure this detection rule. Use the “Use custom detection script” option and upload the SQLODBCDetection.ps1:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-34.png)

I have not configured the dependencies and supersedence. So, I skip these parts. After that, assign this application:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-35-1024x329.png)

I am using this for test purposes. So, I have assigned it to all users.  
  
Lastly, at “Review and Create” click on **create**!

#### Package Deployment and testing

When the application is assigned and the deployment has started, this toast notification will appear:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-36-1024x340.png)

After that, these files are created:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-37-1024x327.png)

In the InstallationLog.Log file these lines will appear:

![SQL ODBC Driver via Intune - Deployment Log](https://www.nielskok.tech/wp-content/uploads/2021/09/image-38.png)

In addition, this log will also report when the installation **has failed**. For example:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-40.png)

These are applications installed:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-41-1024x532.png)

And that is how you deploy the SQL ODBC Driver via Intune!

#### References

Other posts:  
  
[Deploy Single Windows Store Application via Intune](<https://www.nielskok.tech/intune/deploy-single-microsoft-store-app-via-intune/>)  
  
[Upload Autopilot device info menu script](<https://www.nielskok.tech/intune/autopilot-devices-script-menu/>)