[[_TOC_]]

<IMG  src="https://psappdeploytoolkit.com/img/welcomeprompt_withcloseanddefer.png"  alt="Welcome Message"/>

# Getting Started
## Resources
[PS ADT](https://psappdeploytoolkit.com]): The method of installing applications in a consistent way using different installer formats and processes from many vendors.
[SilentInstallHQ](https://silentinstallhq.com/): One resource to find PS ADT commands specific to different applications.
[PowerShell](https://learn.microsoft.com/en-us/powershell/)

# Test Environment Setup

# App Package Format
## Folder Structure
Within the "<appName>\code" folder:
### Infra
This folder should contain the infra bicep folder from our ADO repo for [infra](https://dev.azure.com/BlueMountainAGMP/Blue%20Mountain%20Program%203.0/_git/ABS?path=/abs/appFolderTemplate/code/infra&version=GBmain&_a=contents)
### App Folder
This folder should be named `app-<appName>` and should contain the PS ADT and vendor files to install the app or client.

### License Server
This folder should be named `licenseServer` if the app requires a license server and should contain the PS ADT and vendor files to install the app or client.  The license server could be installed on the same VM as a client app or a different VM.

# Installation Testing
This is the process that would be used to script the installation of any vendor component (the client, the license server, or app server). Usually, these components are separated if they are installed on separate VMs or come from the vendor as separate installers.

## Installer File Types
The packaging process differs for different installer file types that the vendor chooses to use:
1. MSI - Windows Installer
1. EXE - Executable install, could be InstallShield, Wise installer, compressed file install etc. 
1. ZIP - Compressed File

## Editing Deploy-Application.ps1 -- note only edit the following sections.
### VARIABLE DECLARATION
1. Change the following
 - **"$appVendor"**: change to the vendor/author/manufacturer.   Examples are Microsoft, Adobe, MatLab
 - **"$appName"**
 - **"$appVersion"**
 - **"$appScriptDate"** - change to current date
 - **"$appScriptAuthor"** - change to your name

### PRE-INSTALLATION
This section is where you would perform any clean up steps before the install.  Example is removing files or uninstall an existing version of the install.   Example is removing a MSI install 'Remove-MSIApplications -Name "Nessus Agent (x64)"'

### INSTALLATION
This Section is where you would perform any install actions examples are 
- Installing MSI file - Execute-MSI -Action Install -Path "$dirFiles\NessusAgent-x64.msi"
- Executing a EXE install - Execute-Process -Path "$ExePath32" -Parameters "/s INSTALL_SILENT=Enable AUTO_UPDATE=Disable WEB_JAVA=Enable WEB_JAVA_SECURITY_LEVEL=VH WEB_ANALYTICS=Disable EULA=Disable REBOOT=Disable NOSTARTMENU=Enable SPONSORS=Disable REMOVEOUTOFDATEJRES=1 /L $($logTempFolder)\JRE_8x86-Install.log" -WindowStyle Hidden

To find the installation switches can be complicated, most of the time the vendor will provide either with the licensed software or on the website the silent installation procedures.  Some vendors still don't provide this information, you may have to perform an internet query for the "{software name} silent install" or you may have to resort to a list of silent install switches (https://www.advancedinstaller.com/silent-install-exe-msi-applications.html) and try to figure out which one the installer is. 

Tips: 
1. Verify the Execute-Process command is using the correct syntax in its "-Parameters" parameter. For example, If the parameter you are using contains the `` " `` character, use the `` ` `` character to avoid the `` " `` from delimiting the string, and causing the parameter for Execute-Process from ending prematurely.
1. Include any steps in the Install Guide that need to be updated before the PS ADT script is executed. Ex: license files or keys, etc.

### POST-INSTALLATION
This Section is where you would perform any actions to customize the application after the install.  It could be copying a file to a file folder, make registry entries to configure the application, or delete files that need to be removed.
 
### PRE-UNINSTALLATION
This section is for any steps that are needed to complete before the application is uninstalled.  Most of the time this section does not need any additions.
 
### UNINSTALLATION
This section is where the uninstall action occurs. Example is removing a MSI install 'Remove-MSIApplications -Name "Nessus Agent (x64)"' 

### POST-UNINSTALLATION
This section is for any steps that would need to be completed after the applications is uninstalled.   Most of the time this section does not need to be modified.

Note: The Repair sections are not currently being utilized

## Installation Testing

### Run Deploy-Application.ps1
The screen output should not be the color red, indicating error.
The install log will be compressed into a zip file in c:\windows\logs\software\


# Uninstallation Testing

### Run Deploy-Application.ps1 uninstall
The screen output should not be the color red, indicating error.
The install log will be compressed into a zip file in c:\windows\logs\software\