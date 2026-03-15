---
layout: post
title: "Autopilot Devices Script Menu - NielsKok.Tech Intune"
date: 2021-11-24
author: "Niels Kok"
categories: ["Intune"]
tags: ["AddDevice", "Autopilot", "Endpoint", "Endpointmanager", "Grouptag"]
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/05/image-1.png"
description: "I have created a script which builds a menu where you can choose a group tag for your autopilot devices. This can later be used in Intune."
original_url: "https://www.nielskok.tech/intune/autopilot-devices-script-menu/"
---

![](https://www.nielskok.tech/wp-content/uploads/2021/05/image-1.png)

# Autopilot Devices Script Menu

November 24, 2021May 11, 2021 by [Niels Kok](<https://www.nielskok.tech/author/admin/> "View all posts by Niels Kok")

I have written a couple of posts which describe add Autopilot devices to Intune. The feedback that I got was that it might be easier for some people to include a menu to add group tags. I have created a script which builds a menu where you can choose a group tag for your autopilot devices. Furthermore, this group tag can be used later on in Microsoft Intune/Endpoint Manager.

#### Prerequisites

You have to be an administrator on the machine where you run the script.

#### Example

Firstly, save the script as a “.ps1” file. The script syntax is at the bottom of the page.  
  
Then change these values accordingly:

![](https://www.nielskok.tech/wp-content/uploads/2021/05/image-8.png)

After that, run the “.ps1” file.  
  
For example:

![Autopilot devices menu script run](https://www.nielskok.tech/wp-content/uploads/2021/05/image.png)

After that, this dialog box pops up:

![](https://www.nielskok.tech/wp-content/uploads/2021/05/image-1.png)

Choose a type of device. For the demo I choose “Personal Device” and click on “OK”

![](https://www.nielskok.tech/wp-content/uploads/2021/05/image-2.png)

This is the output. Please log on with your Microsoft Endpoint Manager Administrator account:

![](https://www.nielskok.tech/wp-content/uploads/2021/05/image-3-1024x361.png)

This the result, the group tag personal added:

![](https://www.nielskok.tech/wp-content/uploads/2021/05/image-4.png)

From the portal perspective:

![](https://www.nielskok.tech/wp-content/uploads/2021/05/image-5-1024x300.png)

And that is how you create a script with a menu to upload your Autopilot Devices with a group tag!

#### Script Syntax

Script:
    
    
    $Tenantname = "youtenant.onmicrosoft.com"
    $GroupTags = "Personal Device","Shared Device","Kiosk Device"
    
    Add-Type -AssemblyName System.Windows.Forms
    Add-Type -AssemblyName System.Drawing
    
    $form = New-Object System.Windows.Forms.Form
    $form.Text = 'Add Autopilot device menu'
    $form.Size = New-Object System.Drawing.Size(400,250)
    $form.StartPosition = 'CenterScreen'
    
    $okButton = New-Object System.Windows.Forms.Button
    $okButton.Location = New-Object System.Drawing.Point(165,180)
    $okButton.Size = New-Object System.Drawing.Size(75,23)
    $okButton.Text = 'OK'
    $okButton.DialogResult = [System.Windows.Forms.DialogResult]::OK
    $form.AcceptButton = $okButton
    $form.Controls.Add($okButton)
    
    $cancelButton = New-Object System.Windows.Forms.Button
    $cancelButton.Location = New-Object System.Drawing.Point(250,180)
    $cancelButton.Size = New-Object System.Drawing.Size(75,23)
    $cancelButton.Text = 'Cancel'
    $cancelButton.DialogResult = [System.Windows.Forms.DialogResult]::Cancel
    $form.CancelButton = $cancelButton
    $form.Controls.Add($cancelButton)
    
    $label = New-Object System.Windows.Forms.Label
    $label.Location = New-Object System.Drawing.Point(10,20)
    $label.Size = New-Object System.Drawing.Size(360,40)
    $label.Text = 'Choose the type of device that you want to add:'
    $form.Controls.Add($label)
    
    $listBox = New-Object System.Windows.Forms.ListBox
    $listBox.Location = New-Object System.Drawing.Point(10,60)
    $listBox.Size = New-Object System.Drawing.Size(360,20)
    $listBox.Height = 80
    
    foreach ($GroupTag in $GroupTags){
        [void] $listBox.Items.Add($GroupTag)
    
    }
    
    $form.Controls.Add($listBox)
    
    $form.Topmost = $true
    
    $result = $form.ShowDialog()
    
    if ($result -eq [System.Windows.Forms.DialogResult]::OK)
    {
        $x = $listBox.SelectedItem
    
        Write-Host "Adding the device as $($x)"
    
        # Check if AutoPilotScript is installed
        $InstalledScripts = Get-InstalledScript
        If ($InstalledScripts.name -notcontains "Upload-WindowsAutopilotDeviceInfo") {
           Install-Script -Name Upload-WindowsAutopilotDeviceInfo -force
        }
            
        # collect Windows Autopilot info and Upload it to Azure
        Upload-WindowsAutopilotDeviceInfo.ps1 -TenantName $Tenantname -GroupTag $x -Verbose
    
    
    }

#### References

[Microsoft Docs about Powershell menu](<https://docs.microsoft.com/en-us/powershell/scripting/samples/selecting-items-from-a-list-box?view=powershell-7.1>)  
  
Other posts:  
[Powershell function for creating WVD hostpools](<https://www.nielskok.tech/powershellautomation/powershell-function-for-creating-wvd-hostpools/>)  
[General Information about Hostpools](<https://www.nielskok.tech/windows-virtual-desktop/wvd-create-hostpool/>)