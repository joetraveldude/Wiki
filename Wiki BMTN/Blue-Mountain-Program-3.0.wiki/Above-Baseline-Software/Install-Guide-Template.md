# App Installation Guide

App = {app name, like "Jira"}

**{App developers must fill in this document with the information specific to their application. Then delete these comments in curly brackets}**

[[_TOC_]]

## 1.0 Installation Sources
| Vendor | Software | Version | Download Link | Hash |
| - | - | - | - | - |
| <addvalue> | <addvalue> | <addvalue> | <addvalue> | |

<br/>

## 2.0 Vendor Documentation and Links

**{Please Provide a link to Vendor Documentation
If we have a PDF of an installation guide from the vendor, please provide that in the Doc folder.
Include any troubleshooting steps and reference documents from the vendor that were helpful}**

<br/>

## 3.0 Required Resources for complete installation
- App Package (bicep infra code, app installers, app dependencies installers, cmtrace.exe, PS ADT, documentation, GPOs). The documentation should include this document and the ConOps for this app.
- VMs/Session Host deployed
- VM Requirements
  - Powershell on VM
  - Access to enclave storage account
  - Access to SQL Server **{if app requires this, otherwise delete this line}**
  - Access to Microsoft Edge Browser **{if app or file transfer requires this, otherwise delete this line}**

<br/>

## 4.0 Prep - before hands on keyboard in Production env
1. ID files that need to be transfer and collecting them into a zip
  a. Pull the repo zip for the application from ADO (right click on app folder and download as zip)
  b. Pull any separate installers that were kept separate due to their large file size
  c. Ensure files include bicep scripts to deploy infra/VMs, CMTrace.exe, GPOs, and any app dependencies installers
1. Move all files to DTA/AFT folder for transfer **{process being developed, contact ABS team leads}**
1. Notify the Ops team that the files are in the DTA queue and be on alert for installation
1. License/Trial License: **{Specify Trial License needs and instructions, this can refer to the section in the ConOps if it is complete}**
1. Send request via IcM for the following:
  a. AD Security groups:
**{Specify the Security Groups (using the naming convention) needed and OUs needed.}**
  b. AD service accounts
**{Specify service accounts needed (using the naming convention)}**
  c. GPOs
**{Specify any GPOs needed for this app}**
1. In the target enclave key vault, ensure the following secrets exist with these name:value pairs: `ad-domain-name`:"phc-msft.us", `domain-account`:"______DSAccount", `domain-password`:the domain joining account password, and `ou-path`:"OU=Services,OU=Wallaby,OU=Organizations,DC=PHC-MSFT,DC=US". If they don't exist, create them.

## 5.0 Environment Access **{this will be production specific, no changes are needed here}**
1. Go to facility (e.g. customer site, SOC) with access to the production environment listed in the ConOps for this app
1. Go to the physical office and End Terminal (AAP, TTC, etc) with access to production
1. Perform login steps to get access to the production environment Azure Portal (i.e. PAW/vPAW)

## 6.0 Deploy Resources
1. Login to target environment (i.e. production) portal using Mission Plane Admin (MPA) creds
1. Navigate to the storage account (SA) with the app installer package and files. This could be the SA where the files landed after the DTA/AFT.
1. Create a SAS token using default settings and copy URL. It may be wise to paste this in notepad on the target VM in case there are download errors. This SAS URL will be used to download files onto the vPAW, app VM, and any other VMs deployed for this app.
1. Azure Bastion into the enclave vPAW associated with the customer requiring this app (e.g. a delta vPAW to deploy a delta enclave app)
1. Open Edge/IE and enter the SAS URL to download and save the app package files to enclave vPAW here: `C:\abs\`
1. While the files are downloading, open the portal in a separate tab. Navigate to the key vault for the target enclave (the naming convention is to put the key vault in the Resource Group with "...core..." sometimes called "...core-iaas". (Leave this tab open).
1. Unzip the files to `C:\abs\` which will generate a folder like: `C:\abs\<appname>`
1. Format your directory structure so that the following paths are valid.
    1. `C:\abs\DeltekwInsight\Code`.
    2. `C:\abs\DeltekwInsight\Docs`.
1. Open the infra parameters file located here `C:\abs\<app name>\code\`. Add key vault secrets where the secret name matches the string in the parameters file for VM local username and password. 
1. Update the remaining parameters to match the destination resources (subnet ID, Kev Vault resource group, etc).
1. Open PowerShell as administrator
1. Verify that the Bicep CLI is installed by running the command `az bicep version`. An error will indicate that the Bicep CLI is not installed, contact Service Desk or download and install from environment storage account with these tools.
1. Change directory to folder containing app package.
`cd C:\abs\<app name>\code\infra` **{update with your app info}**
1. Run bicep command using the bicep file for your environment (i.e. Fairfax, RX, EX, Fir) and the json parameters files specific to this app that should be located here: `C:\abs\<app name>\code\*.json`. The commands can be found in the `infra` folder `readme.md`. **{These instructions should be tailored to this app}**
`az cloud set --name "AzureUSGovernment"`
`az login`
`az account set --subscription [subscription_name]` **{"SMT-ME_MP_AGES_PHC_MISSION_DEV2_JALLEN" for Fairfax}**
`az deployment group create --name [deplyoment_name] --resource-group [resource_group_name] --template-file .\[bicep_file].bicep --parameters ..\[param_file].json`
1. The command should indicate it was successful and confirm VM(s) exist in the portal


<br/>

## 7.0 App Installation
### Install CMTrace
1. Unzip CMTrace file in `C:\abs\<app name>\code\infra\tools\`.
1. Open new CMTrace folder.
1. Open `Powershell as Admin`. Tip: right-click on Start Menu button.
1. Change directory to where installation package is located.
`cd C:\abs\<app name>\code\infra\tools\CMTrace...\`
1. Execute command: `.\Deploy-Application.ps1 -DeploymentType "Install" -DeployMode "Silent"`.
1. When the script finished and is successful, open CMTrace from Start Menu.
### App Install Silently with PSAppDeployToolkit
1. Login to environment portal
1. Azure Bastion into enclave vPAW for the customer requiring this app
1. Use SAS URL to download app files to
`C:\abs\`
1. RDP from vPAW to app VM
1. Copy files to VM here: `C:\abs\`:
  a. Try to download files to the VM: Open Edge/IE and enter the SAS URL to download and save the app package files to enclave vPAW.
  b. If the VM does not have access to the storage account containing the app package files, copy files via RDP from vPAW to VM Desktop. 
  c. If copy and paste is disabled via RDP, you will need to mount the vPAW hard drive: open Remote Desktop Connection in the Start Menu, click "Options", click "Local Resources" tab, click "More", click "Drives", select the "C" drive to share it in your RDP session. It will show up in the target VM's File Explorer "This PC".
1. Unzip the files to `C:\abs\` which will generate a folder like: `C:\abs\<appname>`
1. Ensure executable files are not blocked (due to appearance of coming from the internet via SAS download). Right click on .exe files and look for an option to "unblock" at the bottom, if so click `unblock` and click `apply` then `ok`.
1. Open `Powershell as Admin`. Tip: right-click on Start Menu button.
1. Change directory to where installation package is located.
`cd C:\abs\<app name>\code\<appNameFolder>\` **{update with your app info}**
1. Execute the command `dir` to ensure a `Deploy-Application.ps1` file exists in the current directory.
1. Be ready to switch to `CMTrace` quickly to open the log file once it is created after the next command.
1. Execute command: `.\Deploy-Application.ps1 -DeploymentType "Install" -DeployMode "Silent"`
1. **Quickly** switch to `CMTrace` window (opened previously) and open log file from path specified above to monitor the install.

	- App log will appear in this directory: **{default location: `C://Windows/Logs/Software/<appNameAndOptionalInfo>.log`. Include the log file location}**

1. Once install is successful, you can see the App when you search in the `Start Menu` (often at the top left) or `Control Panel > Add or Remove Programs`
1. Install Nessus and McAfee agents on the VM. The installers can be found in the `infra` folder in the hardening folder. The parameters will be specific for the environment. Operators should have access to a TSG explaining how to perform this task. When the installation is successful you can see it in the Windows VM "Add & Remove Programs" list.
1. Remote Apps will need the Remote Desktop Agent and Bootloader installed on the VM with the registration key from the Host Pool that will host the app. 

### Additional VMs
**{If additional installers are installed on other VMs, list those steps here. Copy from above and edit as needed}**

### License Activation
1. **{Include license instructions here. Ex: How is a license applied? If no license is needed state that here too}**

### Remote Apps
For apps that are published to the user via Remote App (e.g. client apps not server apps)
1. **{Review steps for publishing Remote Apps in this wiki and include appropriate steps here. Ex: The `infra` should have installed additional resources for AVD. The app VM also needs AVD agents installed.}**

### SQL Server
For apps that need SQL Server:
1. **{Create DB, User and Schema on SQL Server}**

### Configure APP
1. Any SSL or certs needed
1. Connect to SQL Server
1. Setup Admin Account
1. Create Sample App Project
1. Firewall rules/GPOs
1. DNS
1. Install Windows Updates
1. etc


# Appendix
Some of the steps below are scripted in the installation script.  These manual steps are documented for future upgrades or gotchas that took awhile to resolve.