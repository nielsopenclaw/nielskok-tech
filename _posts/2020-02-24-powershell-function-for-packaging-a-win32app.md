---
title: "Powershell Function for packaging a Win32App"
date: 2020-02-24
excerpt: "Powershell function for automatic creation of .Intunewin files for Win32Apps that need to be packaged and upload to Intune."
featured_image: "https://www.nielskok.tech/wp-content/uploads/2020/02/image-14.png"
categories:
  - "Microsoft 365"
  - "Powershell Automation"
tags:
  - "Function"
  - "INtune"
  - "MEM"
  - "Microsoft365"
  - "powershell"
---

(code is at the bottom of the page)  
  
This blog is about the powershell function that I wrote for packaging Win32Apps. Normally this would take sometime creating folder and find the tools etc… Now you just only need to supply these 5 parameters:

![](https://www.nielskok.tech/wp-content/uploads/2020/02/image-14.png)

## Preparation/Set Parameters

5 parameters need to entered like this:  
_T_ he _parameters need to be entered without quotes, even when you have spaces in the installers location or the install command._  
  
**ApplicationName:** This is the name of the application, there are 2 folders being created after this name. I will specify them later on.  
**InstallerLocation:** This is location of the installer this could anywhere on your PC. This file will be copied to the folder that is going to be packaged by the function.  
**Packaging:** This is a boolean value, so you need to enter $True of $False. I have put this in because I want to expand this function to automatically upload the .intunewin file to Intune. In that case it wouldn’t always be necessary to package an app.  
**InstallCommand:** This is the command which triggers the installation of the app. This is often the “appname.exe /silentinstallcommand”. You should check this before running the function.  
**UninstallCommand:** This is the command which triggers the removal of the app. I often use “wmic product where “name like ‘**AppName** %'” call uninstall”

## Packaging Example

The script starts to run. The base folders are created:

![](https://www.nielskok.tech/wp-content/uploads/2020/02/image-15.png)

A folder is created by the first parameter (ApplicationName). A folder to download and store the IntuneWinAppUtil. Last but not least a folder is created for storing the .intunewin file, the output folder. (A folder with the ApplicationName) is also created in this folder.  
  
The installer is copied to the folder with the ApplicatioName, in this example, Greenshot. Furthermore there are 2 files created. A file called Install.cmd & UnInstall.cmd. These files contain the parameters correspondant with the files.

![](https://www.nielskok.tech/wp-content/uploads/2020/02/image-16.png)

I have created these generic files so that when the packaged is uploaded to Intune the Install- and Uninstallcommand is always the same.  
  
After that the .intunewin is created in the OutPut\ApplcationName (Greenshot) folder:

![](https://www.nielskok.tech/wp-content/uploads/2020/02/image-17.png)

Now you are ready to upload the package to Microsoft Intune.  
  
I am planning to expand this script to automatically upload the IntuneWin file Intune, fill the parameters and assign the package. If you want to do so you can add this parameter:
    
    
            [Parameter(Mandatory = $True, Position = 6, ValueFromPipeline = $False)]
    		[string]$ClientName

And at the end of the script you need add an “If Statement” to set the upload to Intune in motion. That could be something like this:
    
    
    if ($clientname -eq "NKO") {
    
        Connect-MSGraph -CertificateThumbprint &lt;ThumPrint&gt;
    
    }

  
  
  
  
This the function that packages the application:
    
    
    # 
    # Niels Kok 
    # Function for Application packaging v0.1
    #
    # Get Information and set parameters
    # Do not use quotes when you enter the parameters
    Function ApplicationPackaging {
    	Param (
    		[Parameter(Mandatory = $True, Position = 1, ValueFromPipeline = $False)]
    		[String]$ApplicationName,
            [Parameter(Mandatory = $True, Position = 2, ValueFromPipeline = $False)]
            [String]$InstallerLocation,
            [Parameter(Mandatory = $True, Position = 3, ValueFromPipeline = $False)]
            [Boolean]$Packaging,
            [Parameter(Mandatory = $True, Position = 4, ValueFromPipeline = $False)]
            [String]$InstallCommand,
            [Parameter(Mandatory = $True, Position = 5, ValueFromPipeline = $False)]
            [String]$UnInstallCommand
    	)
    
    #Package
    if ($Packaging -eq $True) {
    #Create folders for Output and packaging NOTE: After you have ran the script multiples times you can delete this section. This is only to make sure no folders are missing
    if (!(Test-Path -Path C:\Packaging)){
        try {
            Write-Host Packaging Folder is being created -ForegroundColor Green
            New-Item -Path C:\Packaging -ItemType Directory
        }
        catch {
            Write-Host Folder C:\Packaging could not be created -ForegroundColor Red
        }
    }
        if (!(Test-Path -Path C:\Packaging\OutPut)) {
            try {
                Write-Host PackagingOutput Folder is being created -ForegroundColor Green
                New-Item -Path C:\Packaging\OutPut -ItemType Directory
            }
            catch {
                Write-Host Folder C:\Packaging\OutPut could not be created -ForegroundColor Red
            }
    
        }
            if (!(Test-Path -Path C:\Packaging\IntuneWinAppUtil)) {
                try {
                    Write-Host PackagingWinAppUtil Folder is being created -ForegroundColor Green
                    New-Item -Path C:\Packaging\IntuneWinAppUtil -ItemType Directory
                }
                catch {
                    Write-Host Folder C:\Packaging\IntuneWinAppUtil could not be created -ForegroundColor Red
                }
                
            }
    #Create Application related Folders
    if (!(Test-Path C:\Packaging\$ApplicationName)) {
        try {
            Write-Host $ApplicationName Folder is being created -ForegroundColor Green
            New-Item -Path C:\Packaging\$ApplicationName -ItemType Directory -Force
        }
        catch {
            Write-Host $ApplicationName folder in C:\Packaging could not be created -ForegroundColor Red
        }  
    }
    if (!(Test-Path C:\Packaging\OutPut\$ApplicationName)) {
        try {
            Write-Host $ApplicationName OutPutFolder is being created -ForegroundColor Green
            New-Item -Path C:\Packaging\OutPut\$ApplicationName -ItemType Directory -Force
        }
        catch {
            Write-Host $ApplicationName Output folder could not be created -ForegroundColor Red
        }
    }
    #Create new variables for packaging
    $FoldertoPackage = "C:\Packaging\$ApplicationName"
    $OutputFolder = "C:\Packaging\OutPut\$ApplicationName"
    
    #Copy Installer to packaging folder
    Copy-Item $InstallerLocation $FoldertoPackage
    Write-Host $ApplicationName Installer is being copied -ForegroundColor Green
    
    #Create new variable for the application.exe for processing with WinAppUtil
    $InstallerName = Get-ChildItem -Path $FoldertoPackage -Recurse
    
    #Create Installation Files
    $InstallCommand | Out-File $FoldertoPackage\install.cmd
    Write-Host $ApplicationName InstallFile is being created -ForegroundColor Green
    $UnInstallCommand | Out-File $FoldertoPackage\uninstall.cmd
    Write-Host $ApplicationName UninstallFile is being created -ForegroundColor Green
    
    #Packagingsection
    $IntuneWinAppUtil = "C:\Packaging\IntuneWinAppUtil\IntuneWinAppUtil.exe"
        #Check if IntuneWinAppUtil is present and download if not available
        if (!(Test-Path -Path $IntuneWinAppUtil)) {
            try {
            $Path = "C:\Packaging\IntuneWinAppUtil"
            $Shortcut = "IntuneWinAppUtil.exe"
            $null = Invoke-WebRequest "https://github.com/microsoft/Microsoft-Win32-Content-Prep-Tool/raw/master/IntuneWinAppUtil.exe" -OutFile $Path\$Shortcut
            
            }
            catch {
            Write-Host IntuneWinAppUtil could not be downloaded -ForegroundColor Red
            }
        
        }
    #Package Creation
    if ((Get-ChildItem -Path C:\Packaging\Output\$ApplicationName).count -eq 0) {
            try {
                C:\Packaging\IntuneWinAppUtil\IntuneWinAppUtil.exe -c $FoldertoPackage -s $InstallerName.Name -o $OutputFolder -a $FoldertoPackage -q
            }
            catch {
                Write-Host The .Intunewin file could no be created -ForegroundColor Red
            }
    } 
    else {
        $LastPackageDate = (Get-ChildItem -Path C:\Packaging\OutPut\$ApplicationName -Recurse).LastWriteTime
        $timespan = New-TimeSpan -Minutes 1
         if (((Get-Date) - $LastPackageDate) -gt $timespan) {
            try {
                C:\Packaging\IntuneWinAppUtil\IntuneWinAppUtil.exe -c $FoldertoPackage -s $InstallerName.Name -o $OutputFolder -a $FoldertoPackage -q
            }
            catch {
                Write-Host The .Intunewin file could no be created -ForegroundColor Red
            }
        }
    }
    }
