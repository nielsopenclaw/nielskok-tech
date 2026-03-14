---
title: "Migrate UserProfiles from FileServer to OneDrive"
date: 2019-12-12
excerpt: "How to migrate Userprofiles from a fileserver to OneDrive using ShareGate and Powershell. This can also be used for fileserver to SharePoint"
categories:
  - "Microsoft 365"
  - "Powershell Automation"
tags:
  - "Automation"
  - "Microsoft365"
  - "Migration"
  - "Office365"
  - "OneDrive"
---

I would like to share my migration method for moving UserProfiles from a FileServer to OneDrive in Office 365. The migrationtool ShareGate is used for this migration. ShareGate is really powerful tool to move your data from a FileServer (Shared data & User Profiles) to Office 365 (SharePoint Online/Onedrive).  
  
There are 6 steps involved in this migration.   
  
Step 1: Create an overview of the profiles that you want to migrate. Which sources do you use? Which folders in the UserProfiles do you want to exclude? (configuration folder for example)  
  
Step 2: Make sure that you assign a license to every user you want to migrate. Unlike Exchange Online you can’t synchronise files if you don’t have a license assigned.   
  
Step 3: When you have assigned the licenses in Office 365 for OneDrive the OneDrive folders aren’t created automatically. The user needs to sign in to create the OneDrive folder. You can force this action using Powershell. There are 2 steps involved:  
  
Create a CSV file in the following format: 
    
    
    User
    
    user1@domain.com
    user2@domain.com

If you are done creating your CSV file run this script: 
    
    
    Import-Module “Microsoft.Online.Sharepoint.PowerShell” -Force
    $credential = Get-credential
    Connect-SPOService -url https://tenantname-admin.sharepoint.com -Credential $credential
    $InputFilePath = “C:\Temp\OneDrivePreProvision.csv” #useyoupathaccordingly
    $CsvFile = Import-Csv $InputFilePath
    ForEach ($line in $CsvFile)
    {
    Request-SPOPersonalSite -UserEmails $line.User -NoWait
    Write-Host Personal site provisioned for $line.User -ForegroundColor Yellow
    }

This script will create a provisioning request for each user. This means that the OneDrive folder isn’t created instantly. It can take up to an hour for all OneDrive folders to be created.  
  
Step 4: When all the OneDrive folders are created you need assign the migration account administrative permissions to each OneDrive folder. You can do this in ShareGate and the report function. It’s best if you use a generic admin account so users are not alarmed when they see that an admin has permissions to their OneDrive Folder.  
  
Step 5: Its now time to populate a CSV File containing the source folder for the homefolder and the destination, the OneDrive Folder. You need to use the following format:
    
    
    DIRECTORY;ONEDRIVEURL
    Y:\USERNAME\PROFILE;https://tenant-my.sharepoint.com/personal/USERNAME_tenant_com/
    Y:\USERNAME\HOMEFOLDER;https://tenant-my.sharepoint.com/personal/USERNAME_tenant_com/

To help populate this CSV you can use this script to populate the “DIRECTORY” column: 
    
    
    Function Get-ChildItemToDepth {
         Param(
                  [String]$Path = $PWD,
                  [String]$Filter = “*”,
                  [Byte]$ToDepth = 255,
                  [Byte]$CurrentDepth = 0,
                  [Switch]$DebugMode
    )
    $CurrentDepth++
     If ($DebugMode) {
           $DebugPreference = “Continue”
    }
    
    Get-ChildItem $Path | %{$_ | ?{ $_.Name -Like $Filter }
      If ($_.PsIsContainer) {
      If ($CurrentDepth -le $ToDepth) {
          Get-ChildItemToDepth -Path $_.FullName -Filter $Filter ` -ToDepth 
          $ToDepth -CurrentDepth $CurrentDepth
       }
     Else {
       Write-Debug $(“Skipping GCI for Folder: $($_.FullName) ” + ` “(Why: Current depth $CurrentDepth vs limit depth $ToDepth)”)
    }
    }
    }
    }

To populate the second column you can export all the users from the Office 365 Admin Panel to a CSV.  
  
Example:
    
    
    Username@domain.com

Replace @domain.com with _domain_com/ so that you get the format username_domain_com/  
  
Then insert <https://tenantname-my.sharepoint.com/personal/> before username_domain_com/ so that you will end up with: 
    
    
    https://tenantname-my.sharepoint.com/personal/username_domain_com/

Then add the 2 columns so the output is: 
    
    
    DIRECTORY;ONEDRIVEURL
    Y:\USERNAME\PROFILE;https://tenant-my.sharepoint.com/personal/USERNAME_tenant_com/
    Y:\USERNAME\HOMEFOLDER;https://tenant-my.sharepoint.com/personal/USERNAME_tenant_com/

The last step is the migration script. Run this script to upload your profiles to OneDrive: 
    
    
    #Import ShareGate module
    Import-Module Sharegate
    
    #Exclude source folders
    $ExcludeFolders = “examplefolder”,”examplefolder”
    
    #Set CopySettings Incremental
    $copysettings = New-CopySettings -OnContentItemExists IncrementalUpdate
    
    #Example CSV file with columns titled DIRECTORY and ONEDRIVEURL:
    $csvFile = “C:\temp\example.csv”
    
    #load the CSV file into a table
    $table = Import-Csv $csvFile -Delimiter “;”
    
    #Get credentials
    $cred = Get-Credential
    
    #Cycle through each row
    foreach ($row in $table)
    
    {
    
    #connect to the destination OneDrive URL
    $dstSite = Connect-Site -Url $row.ONEDRIVEURL -UserName $cred.UserName -Password $cred.Password
    
    #select destination document library, named Documents by default in OneDrive, this is different for each language, double check this.
    $dstList = Get-List -Name Documenten -Site $dstSite
    
    #Get list for profile folder and exclude folders
    foreach ($SourceFolder in $row.DIRECTORY)
    
        {
        $folders = Get-ChildItem -Path $SourceFolder -Exclude $ExcludeFolders
        }
    
    #Import folders
     foreach ($folder in $folders)
    
     {
     $uriFolder = [System.Uri]$folder.FullName
     Import-Document -SourceFilePath $uriFolder -DestinationList $dstList -InsaneMode -CopySettings $copysettings
     Write-Host Uploaded Files for $row.Directory -ForegroundColor Yellow
     }
     }

If you have any questions about this migration method feel free to contact me.  
  
Regards,  
  
Niels
