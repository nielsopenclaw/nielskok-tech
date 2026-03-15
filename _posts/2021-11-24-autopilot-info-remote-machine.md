---
layout: post
title: "Collect AutoPilot Info from Remote Machine - NielsKok.Tech Intune"
date: 2021-11-24
author: "Niels Kok"
categories: ["Intune"]
tags: ["Microsoft365", "powershell"]
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/06/image-1.png"
description: "Collect AutoPilot Info from Remote Machine. You can this by using a registering a powershell profile on the remote computer."
original_url: "https://www.nielskok.tech/intune/autopilot-info-remote-machine/"
---

# Collect AutoPilot Info from Remote Machine

November 24, 2021June 10, 2020 by [Niels Kok](<https://www.nielskok.tech/author/admin/> "View all posts by Niels Kok")

How can you collect AutoPilot information from a Remote Machine? In this blog I will show you how you can grab this information.

### Prerequisites Remote Machine

You need to have the following prequisites ready:  
  
1\. On the remote machine the WinRM service must be running.  
2\. The network must allow WinRM connections.   
3\. The Windows Firewall on the remote machine must allow WinRM connections.

### Example Collect AutoPilot Info

This is an example of grabbing AutoPilot Information for a remote Windows Domain joined machine.  
  
Firstly, you need to start an elevated Powershell session on the machine where you want initiate the session from.  
  
After that, run this command: (use accordingly for your situation)
    
    
    $Enter-PSSession -ComputerName "Remote Device" -Credential "Your Account"

You will get a pop-up for credentials:

![Credential Pop-up](https://www.nielskok.tech/wp-content/uploads/2020/06/image-1.png)

You now have entered the remote Powershell Session. Check this by seeing the remote computer name in your Powershell Window:

![Remote Powershell Session For AutoPilot info collect](https://www.nielskok.tech/wp-content/uploads/2020/06/image-2.png)

We now need to register a Remote PS Session configuration. Otherwise the double hop problem occurs, [link](<https://docs.microsoft.com/en-us/archive/blogs/sergey_babkins_blog/another-solution-to-multi-hop-powershell-remoting>).  
  
Run the following command:
    
    
    Register-PSSessionConfiguration -Name RPS -RunAsCredential "Your Account" -MaximumReceivedDataSizePerCommandMB 1000 -MaximumReceivedObjectSizeMB 1000

Another credential window will appear with a warning that you must trust the remote computer. (we will remove the session configuration at the end of the blog)

![](https://www.nielskok.tech/wp-content/uploads/2020/06/image-3.png)

Now exit this Powershell Session returning to the orginal one with the “exit” command:

![](https://www.nielskok.tech/wp-content/uploads/2020/06/image-4.png)

Enter a new session with the registered Remote PS Session Configuration by using this command:
    
    
    Enter-PSSession -ComputerName NKOTEST5 -Credential "Your Account" -ConfigurationName RPS

You will get another credentials pop-up:

![Credential Pop-up](https://www.nielskok.tech/wp-content/uploads/2020/06/image-1.png)

We are now ready to collect the AutoPilot info. Run the following code: (make sure you can access the Share where you are going to put the CSV file)
    
    
    If ($InstalledScripts.name -notcontains "Get-WindowsAutoPilotInfo") {
        Write-Host Installing NuGet &amp; AutoPilot Info Script
        Install-PackageProvider -Name nuget -force
        Start-Sleep 10
        Install-Script -Name "Get-WindowsAutoPilotInfo" -force
    
    }
    #Set ExecutionPolicy to bypass temporarily
    set-executionpolicy bypass
    
    Get-WindowsAutoPilotInfo.ps1 -OutputFile "\\SHARENAME\AutoPilotInfo\Computers.csv" -Append

Check your CSV file on the share mentioned earlier and see that the info has been added:

![Collect AutoPilot Info from Remote Machine](https://www.nielskok.tech/wp-content/uploads/2020/06/image-5.png)Collect AutoPilot Info from Remote Machine

The last thing to do is to remove the PS Session configuration. You can do so by using this code:
    
    
    Unregister-PSSessionConfiguration -Name RPS

Now there are no encrypted password anymore on the remote device and the AutoPilot information has been captured.  
  
I am going to automate this further. I will create a script which collects Autopilot information from several remote machines.

### References

Ofcourse the script that is downloaded is created by Micheal Niehaus:

<https://www.powershellgallery.com/packages/Get-WindowsAutoPilotInfo/2.3>

<https://docs.microsoft.com/en-us/archive/blogs/sergey_babkins_blog/another-solution-to-multi-hop-powershell-remoting>  
<https://www.nielskok.tech/microsoft365/windows-10-autopilot-info-upload-script/>