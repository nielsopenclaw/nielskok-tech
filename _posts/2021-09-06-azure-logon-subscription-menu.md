---
title: "Azure Logon Subscription Menu"
date: 2021-09-06
layout: post
featured_image: "https://www.nielskok.tech/wp-content/uploads/2021/09/image.png"
categories:
  - "Powershellautomation"
  - "Azure"
  - "Powershell Automation"
tags:
  - "Azure"
  - "Powershell Automation"
  - "Automation"
  - "Menu"
  - "powershell"
  - "Scripting"
  - "Select"
  - "Subscription"
source_url: "https://www.nielskok.tech/powershellautomation/azure-logon-subscription-menu/"
---

Don’t you hate it when you want to logon to Azure and you need to select a subscription every time? Firstly, you need to logon using: “Connect-AzAccount”. After that, you need to select your subscription but you obviously don’t know your subscription id. You run: “Get-AzSubscription”, copy the subscription id and run: “Set-AzContext”. I have created a simple script where you can directly select your Azure Subscription at Logon via a menu.  
  
This is the menu:

![Azure Logon Subscription Menu](https://www.nielskok.tech/wp-content/uploads/2021/09/image.png)

As you can see, I have 4 subscriptions and this way I can easily select the one I want to use at logon via Azure Powershell.  
  
I will show you how it works!

#### Prerequisites

  * Microsoft Azure tenant + subscription(s)
  * Powershell Modules ([install guide](<https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-6.3.0>), please upgrade your Powershell to version 7.)
  * **Optional:** Powershell profile ([creation guide](<https://www.howtogeek.com/126469/how-to-create-a-powershell-profile/>))  


#### Script syntax + explanation

This is the script syntax:
[code] 
    Function AzureLogon {
    
        Connect-AzAccount
    
        $Subscriptions = Get-AzSubscription | Select-Object Name, Id
    
        Add-Type -AssemblyName System.Windows.Forms
        Add-Type -AssemblyName System.Drawing
    
        $form = New-Object System.Windows.Forms.Form
        $form.Text = 'Log on to Azure and select Subscription'
        $form.Size = New-Object System.Drawing.Size(500,250)
        $form.StartPosition = 'CenterScreen'
    
        $okButton = New-Object System.Windows.Forms.Button
        $okButton.Location = New-Object System.Drawing.Point(165,180)
        $okButton.Size = New-Object System.Drawing.Size(75,23)
        $okButton.Text = 'OK'
        $okButton.DialogResult = [System.Windows.Forms.DialogResult]::OK
        $form.AcceptButton = $okButton
        $form.Controls.Add($okButton)
    
        $cancelButton = New-Object System.Windows.Forms.Button
        $cancelButton.Location = New-Object System.Drawing.Point(250,180)
        $cancelButton.Size = New-Object System.Drawing.Size(75,23)
        $cancelButton.Text = 'Cancel'
        $cancelButton.DialogResult = [System.Windows.Forms.DialogResult]::Cancel
        $form.CancelButton = $cancelButton
        $form.Controls.Add($cancelButton)
    
        $label = New-Object System.Windows.Forms.Label
        $label.Location = New-Object System.Drawing.Point(10,20)
        $label.Size = New-Object System.Drawing.Size(460,40)
        $label.Text = 'Choose the subscription you want to use in Powershell:'
        $form.Controls.Add($label)
    
        $listBox = New-Object System.Windows.Forms.ListBox
        $listBox.Location = New-Object System.Drawing.Point(10,60)
        $listBox.Size = New-Object System.Drawing.Size(460,20)
        $listBox.Height = 80
    
        foreach ($Subscription in $Subscriptions){
            [void] $listBox.Items.Add($Subscription)
        }
    
        $form.Controls.Add($listBox)
        $form.Topmost = $true
        $result = $form.ShowDialog()
    
        if ($result -eq [System.Windows.Forms.DialogResult]::OK)
        {
            $x = $listBox.SelectedItem
            Write-Host "Selecting subscription $($x)"
            Select-AzSubscription $x.Id
        }
    }
[/code]

This is the explanation for the Azure logon subscription menu is as follows:  
  
Firstly, save the Powershell script above as a .ps1 file.  
  
After that run the file:

![Azure Logon Subscription Menu - Start Function](https://www.nielskok.tech/wp-content/uploads/2021/09/image-1.png)

After this, you are able to run the “AzureLogon” command:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-2.png)

An authentication prompt pops up and after that you are able to select your subscription:

![Azure Logon Subscription Menu - fully functioning menu](https://www.nielskok.tech/wp-content/uploads/2021/09/image-3.png)

In addition, I use Windows Terminal as my default command window. Check out how to install [here](<https://docs.microsoft.com/en-us/windows/terminal/get-started>).

#### Add the script to your Powershell profile

When you want to use this script more frequently I advise you to add this script to your Powershell profile. I have put the link to create a powershell profile in the prerequisites but [here](<https://www.howtogeek.com/126469/how-to-create-a-powershell-profile/>) it is again.  
  
After you created the Powershell profile you can put this powershell function directly in there.  
  
That way, when Powershell starts, you are directly able to use this function and can easily logon to Azure Powershell:

![](https://www.nielskok.tech/wp-content/uploads/2021/09/image-4.png)

#### References

[Install Powershell modules](<https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-6.3.0>)  
  
[Create Powershell Profile](<https://www.howtogeek.com/126469/how-to-create-a-powershell-profile/>)  
  
Other Posts:  
  
[AutoPilot device scope tag script with menu](<https://www.nielskok.tech/intune/autopilot-devices-script-menu/>)  
  
[Windows 10 Image Series](<https://www.nielskok.tech/devops/windows-10-image-series-part-0-prepare-azure-devops/>)
