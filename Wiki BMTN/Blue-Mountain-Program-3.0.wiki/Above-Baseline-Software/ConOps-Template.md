# Concept of Operations (CONOPS) for App

APP = <app name>

**{App developers must fill in this document with the information specific to their application and remove comments like this. This format can be used to sparingly add <span style="color:blue">color</span>.}**

## License

```plaintext
System POC Information

    Contractor Name: Microsoft Federal - BMTN
    Contractor Address: 12012 Sunset Hills Rd, Reston, VA 20190
    Software POC: Junaid Paracha
    Packager POC: {Your Name}
    Integration POC: {Name of MIO/OPS, AFS, or Mantech Personnel}
    Customer App POC provided to MIO/Ops: {Y/N ensure a customer POC was provided to MIO/Ops on the production network}
    
```

[[_TOC_]]


## 1.0 Intro and Background

### 1.1 Application Overview

- **Name** : {Prod Env} ABS App - {Full Name of your app}
  - **Vendor** : {YourAppInfo}
  - **Version** :  {YourAppInfo}
  - **Size of Installer** :  {YourAppInfo}
  - **Open Source** : {Yes/No} (include link if 'yes')
  - **Foreign** : {Yes/No} (include link to foreign entity)
  - **Trial License Summary** : {Ex: Yes, for 14 days and requires MAC address to activate}
  - **Number of Concurrent Users** : {100}
  - **Number of Licenses** : {may be different than users for some apps}
  - **Dependencies** : {Java, python, SQL, etc}
  - **ADMX Available** : {y/n and include a summary of the discussions with the vendor. ADMX files make it easier to configure an app with GPOs}
  - **App Level STIG available** : {y/n; provide any relevant information (version, date)}

**{The information in this table is entered into the Change Request (CR)}**
**Name**|**Description**
--|--------------------
 **Summary and Product Description** | {E.g. "Deploy 'app name' as a remote app in RX. This app was previously installed in Cedar and EX." OR "Deploy 'app name' web app..."} 
 **APP Category** | {e.g. Cat1, Cat2, Cat3a, Cat3b, or Cat4} 
 **Test Plan** | *Please add a test plan to ABS repo under your docs folder. Provide Location here  
 **Impacted System(s) or Component(s)** | {i.e. Cedar, RX, EX} 
 **Purpose** | {Example: This app provides engineering modeling and simulation capabilities to users} 
 **Implementation Plan** | {Please add install guide to ABS repo under doc. Provide repo location here}       
 **Rollback Procedures** | The Install Guide linked above includes the uninstall commands. The Resources created for this app would be deleted. 
 **Proposed Date/Time** | {12/25/2022} 
 **Impact to Users** | No impact to all users. Users who require RemoteApps will need to restart once added to the app security group. Users of Web Apps will not need to restart but will need to request access to the app security group.  
 **Configuration Items (CIs) Impacted** | {include the CIs impacted for your app: e.g.: AD, SQL Server, Gates Desktop, _______ Enclave} 

## 2.0 Technical Overview

### 2.1 User Experience

**{Delete one of these based on the application type}**
RemoteApp: Users will access APP from an icon on the desktop from within the Gates Virtual Desktop Infrastructure (VDI), Remote Desktop Services (RDS) Session Host from a Trusted Thin Client (TTC). The APP will be hosted via an RDS Session Host that has published the application through RemoteApp. The icon will be provided to select users via Active Directory (AD) group membership.

WebApp: Users will access APP via a browser to the APP URL from within the Gates Virtual Desktop Infrastructure (VDI), Virtual Desktop from an Azure Access Point (AAP). The JIRA application will be hosted on a web app server. Access to the web app will be provided to select users via Active Directory (AD) group membership.

### 2.2 Backend Systems and Data

**{Delete one of these based on the application type}**
RemoteApp: APP will be hosted via a single Session Host configured to publish a RemoteApp. The RDS Session Host that publishes APP via RemoteApp will be located in a subnet specific to the application and will be controlled via AD group membership. Authorized users will be provisioned with an APP icon within the Gates Desktop session start menu. 

Web App: APP will be hosted via a single IIS server with the APP Web App installed. The web app server will be located in a subnet specific to the application and will be controlled via AD group membership. Authorized users will be provisioned an APP icon within the Gates Desktop session. 

**{A license Server description is only needed if the app requires a license server}**
**{Example:}** A License Server will be installed on the same server as the application. A ten-user floating license will be the initial license installed. The license will facilitate ten simultaneous users. It is anticipated that additional licenses will be purchased and installed in the future.

Data Storage
Data being edited by APP will reside in a Sentris protected file share within the Data Plane. Data files that have been edited by the APP will reside inside a Sentris enabled file share within the Collaboration Suite Virtual Network (VNET). The file share is provided to users via a mapped network drive. **{describe the types of data and how the app stores the information. E.g. ".txt" files are saved to the documents folder which is hosted on the sentris enabled fileshare}**
**{or}**
Data being edited by APP will reside in a database or server (i.e. SQL Server). **{describe the types of data, how the app stores the information, how users/admins get access to that data}**

Local admin Usernames and Passwords will be stored in the enclave key vault.

### 2.3 Architecture Diagram 
![Sample Image](assets/SampleImageConops.png "Sample Image")
 **{Replace the image reference above with the architecture diagram file path that matches the app category for this app (ex: assets/abs_cat2 "Category 2"). The latest images are stored in the ABS repo but a description can be found [here](/Above-Baseline-Software/Packaging-Process/Diagrams-for-Apps)}**

### 2.4 PPSM

#### 2.4.1 NSG Rules needed **{Below is an example for XML Spy on Cedar}**
PPSM Summary: **{APP communicates to the user browser via port 443, the license server via port ___, SQL Server via port ___, and LDAP via port 636.}**

Standard inbound rules for Administration, ACAS, HBSS, WSUS, etc are included with the NSGs created for the Mission Enclave.
##### **App Specific NSGs**

Inbound Security Rules **{update with port rules inbound from external resources (AD, AVD, etc) to the app vnet}**
|**Purpose** | **Rule Name** | **Source** | **Source Port** | **Destination** | **Destination Port** | **Protocol**|
|---|---|---|---|---|---|---|
|NSGs required for RemoteApps | Create AllowInboundRSD1AllToXMLSpyHost | RDS Connection Broker IP | any | XMLSpy Host IP | 135,139,443,445,3389,5504,5985,49152-65535 | TCP
Create NSG and rules for new XMLSpy subnet| AllowInboundSubnetCommunication | XMLSpy subnet | any | XMLSpy subnet | any | any
Add NSG rules to application (VDI) NSG | Create AllowInboundXMLSpyHostToRDS1All | XMLSpy Host IP | any | RDS Connection Broker IP | 135,139,443,445,3389,5504,5985,49152-65535| TCP|


Outbound Security Rules **{update with port rules outbound to external resources (AD, AVD, etc) to the app vnet}**
**Purpose** | **Rule Name** | **Source** | **Source Port** | **Destination** | **Destination Port** | **Protocol**
---|---|---|---|---|---|---|
NSGs required for RemoteApps | Create AllowInboundRSD1AllToXMLSpyHost | RDS Connection Broker IP | any | XMLSpy Host IP | 135,139,443,445,3389,5504,5985,49152-65535 | TCP
Create NSG and rules for new XMLSpy subnet | AllowOutboundSubnetCommunication | XMLSpy subnet | any | XMLSpy subnet | any | any
Add NSG rules to application (VDI) NSG | Create AllowOutboundRds1AllToXMLSpyHost | RDS Connection Broker IP | any | XMLSpy Host IP | 135,139,443,445,3389,5504,5985,49152-65535 | TCP

**{under development - Andy}**
#### 2.4.2 GPOs
Hyperscale will have existing GPOs
GPOs can be applied at domain, OU, or local computer levels depending on customer need.

Need to revisit if needed in production.
#### 2.4.3 Exemptions to STIGs
#### 2.4.4 Security Groups
The Access Mgr group is delegated authority to add people to the remaining groups unless specified.
| Group Purpose | Name |
|--|--|
|Access Mgr| **{ex: sg-g-jira-access-mgr}**|
|App User |**{ex: sg-g-jira-users}**|
|App Admin |**{ex: sg-g-jira-admins}**|
|Remote App |Same as user group|
#### 2.4.5 Service Accounts
|VM Name|Service Name|Permissions|
|--|--|--|
|**{STK Client}**|**{svc-stk-app}**||
|**{STK License Server}**|**{svc-stk-license}**||


## 3.0 System Information

### 3.1 System Resources

- **{Add lists of Azure resources: VMs, nic, disk, host pool, application group, workspace, etc}**

## 4.0 System Components

### 4.1 RDS Session Hosts

#### 4.1.1 Virtual Machine

- Server Name: **{Add your servername here}**
- Function: **{is this the client VM, etc}**
- Quantity: **{add quantity}**
- Operating System: **{Windows 10 Multi-session for Remote Apps or Windows Server 2019 for servers}** (latest in environment)
- Virtual Machine Size: **{the recommended size based on the vendor recommendation, e.g. Standard D32s_v5}**
- CPU: **{32 vCPUs}**
- Memory: **{128 GB of RAM}**
- Storage: **{128 GB Operating System Drive}**
- Size Justification: **{why was this size and storage type selected? e.g. app requires high memory, or high number of users drive the need for high memory, etc}**
- URL to sizing documentation: **{add url to vendor site on sizing. If no URL exists, cite reliable source (vendor email, etc) and describe the source here}**

#### 4.2 Agents/Tools (installed on VMs)
**{delete Splunk if this app is deployed to hyperscale}**
**Name**|**Vendor**|**Version**
---|---|----
|Splunk Forwarder |Splunk|8.2.0|
|McAfee Security Platform| McAfee|10.7.0.2421|
|McAfee Security Threat Prevention| McAfee| 10.7.0.252|
|McAfee Agent|McAfee|5.7.4.399|
|Nessus Agent|Tenable|Auto updated by Nessus|
The agent for Defender for Cloud and LogA are installed automatically by Azure.
Agents may be updated to current versions.

**{Research if there is a Sentinel pluggin for this app. https://docs.microsoft.com/en-us/azure/sentinel/data-connectors-reference  If yes: fill out the below Section. If no, add a statement here that a plugin is not available for this APP and add "none" to the table below with the app name}**
| Sentinel Plugin | Info|
|--|--|
|||

## 5.0 Gates Services

The following environment managed services will be used to support the APP:

- AD Domain Services
- VDI
- Domain Name Services (DNS)
- Windows Update (WSUS)
- Nessus Security Center (SC)
- McAfee Complete Endpoint Protection
- Network Time Protocol (NTP)
- LogA (hyperscale) or Splunk (Edge)
- Defender for Cloud

## 6.0 Security Controls

### 6.1 Access Control (AC)

User access to the application will be enforced via AD Security Group membership. 
**{Select one of the following based on their app data needs}**
**{Common for non-web apps}** User access to data edited with the APP will reside within a Sentris enabled file server.
**{Common for web apps}** The data will reside in a customer specific SQL deployment located within the enclave.

### 6.2 Auditing (AU)

APP server will inherit the audit configuration and capabilities from the secure OS baseline and AD group policies. VMs will have the Azure Monitor agent installed.

### 6.3 Configuration Management (CM)

All changes associated with the environment that support APP will follow existing CM processes and will take place within approved maintenance windows. Changes will be initially developed and tested within the development environment with automated installations created for repeatability. Test plans with expected results will be developed prior to gaining approval for the change to be approved within the production environment.

### 6.4 Contingency Planning (CP)

VM will leverage the existing platform environment backup solution for VM backups/restore.

### 6.5 Identification & Authentication (IA)

General and privileged users will leverage Gates AD credentials.

### 6.6 Risk Assessment (RA)

Nessus SC will be leveraged for vulnerability/compliance scanning. Gates cyber personnel will execute/analyze scan results.

### 6.7 System and Communication Protection (SC)

**{The developer should use one of the following 2 sentences as a starting point}**
RemoteApp: The application will reside on a RDS Session Host published via RemoteApp and located within the APP subnet.
WebApp: The application will reside on a RDS Session Host and will serve the app web content via the user’s Gates Desktop browser.
The Ports, Protocols, and Services Management (PPSM) will be identified in the above section for PPSM.

Implementation for transmission confidentiality/integrity, session disconnect, and authenticity will need to be evaluated and documented.

### 6.8 System and Information Integrity (SI)

Updates to the application will be installed via an automated fashion via PowerShell scripts.

**{remove this next sentence if no license server is needed}**
The license server will be updated via an automated fashion via PowerShell scripts.

Updates within the development environment will be tested prior to the automated installation packages being transferred to the production environment. Install files will be transferred to the production environment by authorized Data Transfer Agents (DTAs) via approved methods for the platform.

Prior to changes to an application, snapshots of the disks will be performed for reverting back in the event that an update causes a problem. After testing is performed, the snapshots will be removed. The maintenance log and Change Request (CR) must be updated and if required the software baseline adjusted to reflect new application versions.

Malicious Code protection will be implemented via McAfee Agent/ENS deployments managed by the environment provided McAfee server.

Input validation and Error Handling functionality associated with the applications will be inherited from each software vendor.

## 7.0 Customer App Administration
The steps needed to be an administrator for this app for customer responsibilities (add users, configured permissions inside the app, etc).

1. Add Users: 
1. Remove user:
1. Give user permissions to ___:

## 8.0 Sustainment 

|Vendor Name|Vendor Phone|Vendor Email|
|--|--|--|
|<app vendor> Sales|+1 000-000-0000||
|<app vendor> Support|+1 000-000-0000||

### 8.1 License
1. License Support and Maintainability summary
1. Concurrent Licenses available: **{y/n}**
1. License server needed: **{y/n; if yes, include explanation}**
### 8.2 Trial License Info
1. Available: **{Yes/No}**
1. Duration: **{usually in days}**
1. How to Request: **{web url, vendor POC, etc}**
1. Can a prod license be reused in dev? **{"Vendor said <yes/no> in an email to ____ dated ____"}**
1. Trial License Requirements: VM must be deployed first to get {MAC address, hostname} or application must be installed first to {generate UID from app, etc}
### 8.3 Maintenance (Op Sec)
1. Safe Shutdown Procedure: **{Ex: Login to app VMs and select shutdown from the start menu. Consider reviewing the running services for that app to help return to a known state.}**

### 8.4 App Patching/Updates
1. Update Frequency: **{Ex: Cameo has yearly major releases and approx quarterly updates. MatLab has Major Releases twice a year}**
1. How are updates retrieved offline: **{publicly available downloads, website with credentials, etc}**

### 8.5 App Infra Upgrade
**{This section is a placeholder pending more conversations with MIO/Ops.}**
1. OS/infra Upgrades


## 9.0 Trouble Shooting Steps (App Specific)

Please include scenarios that can be used for trouble shooting. This may be steps you performed to overcome installation blockers (service won't start, app won't open, etc).

1. **{service won't start, LDAP won't work, users don't show up inside app, etc}**

## 10.0 Azure Consumption Revenue (ACR) Estimates Methodology
The estimated Azure cost of this app base on the required resources. Cost for shared resources like SQL Server are included in the SQL Server ConOps ACR.
**{Please summarize the ACR for the Azure resources required by this app architecture. Use the Azure Calculator for Commercial and add multiplier for Fairfax and RX/EX as shown. Pricing Calculator: https://azure.microsoft.com/en-us/pricing/calculator/}**

### Per user resources:
User type: **{this should match the VM size justification in section 4}**
**{Examples:
User type: Low (Connected but nearly dormant)
User type: Medium (vendor recommended `minimum`): -vCores: 2 -RAM: 8GB
User type: High (power user): -vCores: 4 -RAM: 16GB}**

Calculation:

10 total connected users **{this should match the user count in section 1.1}**
| | vCPU | RAM |
|--|--|--|
|8 concurrent Medium users|8 * 2 = 16 vCPU|8 * 8 = 64 GB RAM|
|2 concurrent High users| 2 * 4 = 8 vCPU| 2 * 16 = 32 GB RAM|
|Total Needed|24 vCPU| 96 GB RAM|
|Options|||
|Standard_D16_v5|16|64|
|Standard_D32_v5| 32|128|
Recommended Initial VM Size: D32_v5 **{this should match the VM sku/size in section 4}**


### ACR Table
| **App Component** | **Resource Requirements** | **Azure Resource Mapping** | **Monthly ACR Estimate** |
|--|--|--|--|
|App Client VM|32 vCores; 128 GB RAM; 256 GB SSD|Azure VM Size: D32_v5; 256 GB SSD Temp Disk|Global Cloud: ~$2,383|
|SQL Server|See|SQL Server|ConOps ACR|
|**Total:**|||Global Cloud: ~$2,383; Government (+25%): ~$2,979; Government High (+75%): ~$5,212|

## Version History for APP **{this should be for app documentation updates no template updates}**
| Version |     Date    |      Author       |           Description         |
|---|---|---|---|
|   1.0   |  **{date}** |  **{Your Name}**   |      **{add description}**         |
||||