# Technical Service Guide for Data Transfer Portal (DTP)

[[_TOC_]]

Change Record

|Action|Name|Job Title|Version|Date|
| :- | :- | :- | :- | :- |
|Submitted By|<p>Charles Fausz</p><p>Kat Hopkins</p>||Initial Draft||
|Endorsed By|||||
|Approved By|||||


# **Table of Contents**
[Intended Audience	5](#_Toc115868505)

[Document Scope	5](#_Toc115868506)

[Out of Scope	5](#_Toc115868507)

[Assumptions	5](#_Toc115868508)

[Pre-Requisites	5](#_Toc115868509)

[Define Azure Resource Names	5](#_Toc115868510)

[Scripted Installation (Recommended)	7](#_Toc115868511)

[Manual Installation - Overview	7](#_Toc115868512)

[Use this as a secondary option when unable to execute the automated option.  All steps are performed in the Azure Portal. Installation Order	7](#_Toc115868513)

[Manual Deployment – Tier 0 (Global Admin)	8](#_Toc115868514)

[Create a Resource Group	8](#_Toc115868515)

[Create Virtual Network	9](#_Toc115868516)

[Create Network Security Group	11](#_Toc115868517)

[Network Security Group – Add Subnets	12](#_Toc115868518)

[Create Key Vault	12](#_Toc115868519)

[Create Storage Account	13](#_Toc115868520)

[Create Audit Storage Account	15](#_Toc115868521)

[Create App Service (Web App)	16](#_Toc115868522)

[Create Function App	18](#_Toc115868523)

[Function App – Add VNet Integration	18](#_Toc115868524)

[Create SQL Database	19](#_Toc115868525)

[SQL – Create Table	20](#_Toc115868526)

[Private Endpoints	20](#_Toc115868527)

[Private Endpoints – Storage Account - Queue	20](#_Toc115868528)

[Private Endpoints – Storage Account - Blob	21](#_Toc115868529)

[Private Endpoints – Storage Account - File	21](#_Toc115868530)

[Private Endpoints – Storage Account - Table	22](#_Toc115868531)

[Private Endpoints – Audit Storage Account - Blob	22](#_Toc115868532)

[Private Endpoints - Key Vault	**Error! Bookmark not defined.**](#_Toc115868533)

[Function App - Add Private Endpoints	26](#_Toc115868534)

[Private DNS Zones	28](#_Toc115868535)

[Additional Cleanup	35](#_Toc115868536)

[Customer Managed Keys (CMK)	**Error! Bookmark not defined.**](#_Toc115868537)

[CMK - Audit Storage Account	**Error! Bookmark not defined.**](#_Toc115868538)

[CMK - Storage Account	**Error! Bookmark not defined.**](#_Toc115868539)

[Final Cleanup	**Error! Bookmark not defined.**](#_Toc115868540)

[Key Vault – Update Selected Networks	27](#_Toc115868541)

[Storage Account – Add File Shares	28](#_Toc115868542)

[Function App Private Endpoint	27](#_Toc115868543)

[Function App – Update Configuration	**Error! Bookmark not defined.**](#_Toc115868544)

[Key Vault – Establish Access	32](#_Toc115868545)

[SQL Server - Setup Transparent Data Encryption	33](#_Toc115868546)

[SQL Server Private Endpoint	34](#_Toc115868547)

[Key Vault – Remove IP Addresses	35](#_Toc115868548)

[Storage Account Cleanup	62](#_Toc115868549)

[Function App	63](#_Toc115868550)

[Audit Storage Account Cleanup	64](#_Toc115868551)

[Role Assignment	64](#_Toc115868552)

[Additional Tasks	67](#_Toc115868553)

[Subscription – Role	67](#_Toc115868554)

[Manual Deployment – Tier 1 (Contributor)	35](#_Toc115868555)

[Azure Active Directory (AAD) – Add App Registrations (Tier 1 Support)	39](#_Toc115868556)

[App Registration – Function App	39](#_Toc115868557)

[App Registration – Client Web App	43](#_Toc115868558)

[Enterprise Applications - Client Web App	45](#_Toc115868559)

[Enterprise Applications - Function App	45](#_Toc115868560)

[Manual Deployment – Tier 2 (Helpdesk/Support)	47](#_Toc115868561)

[Tier 0 Support	**Error! Bookmark not defined.**](#_Toc115868562)

[Function App - Authentication	47](#_Toc115868563)

[Function App - Configuration	47](#_Toc115868564)

[Deploy Functions	49](#_Toc115868565)

[Deploy Client App	56](#_Toc115868566)

[Deployment Validation	65](#_Toc115868567)

[Troubleshooting	68](#_Toc115868568)

[Function App	68](#_Toc115868569)

[Publishing Related Errors	68](#_Toc115868570)

[Acronyms and Abbreviations	70](#_Toc115868571)

[Glossary	70](#_Toc115868572)



# **Intended Audience** 
This document is intended for administrators, IT Planners, and managers who are responsible for establishing and reviewing overall deployments and oversee operational practices. The document should be shared with staff that are also responsible for establishing overall security policies.
# **Document Scope** 
The scope of this document is to provide the reader the instructions for setting up the Data Transfer Portal.  
# **Out of Scope** 
The scope of this document addresses deploying DTP. Any additional resources outside of the defined solution will be considered out of scope.
# **Assumptions** 
The author of this document has assumed that the reader has a basic understanding of the Microsoft Azure Portal, Azure Infrastructure as Service (IaaS), Azure Platform as a Service (PaaS), Windows PowerShell, networking concepts, and deploying code.
# **Pre-Requisites**
The following requirements are required for a successful deployment of the solution. 

- An active Azure subscription 
- The proper privileges to create resources within the target subscription
  - Recommended Azure account should be an Owner on the Azure subscription. The minimum requirement is the Contributor role.

The Azure account must have permission to manage applications in Azure Active Directory (Azure AD). Any of the following Azure AD roles include the required permissions:

- Application administrator
- Application developer
- Cloud application administrator
# **Define Azure Resource Names**
- It is suggested that a naming convention for resources be established before starting the deployment to have a consistent pattern when creating resources.
- Resources for naming conventions:
  - [Define your naming convention - Cloud Adoption Framework | Microsoft Docs](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming)
  - [Recommended abbreviations for Azure resource types - Cloud Adoption Framework | Microsoft Docs](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)

Use this table to identify Azure resources and recommend naming convention in advance:

SolutionName = '\<AppName>-\<EnvironmentType>'

For example:

${AppName} = ‘dtp’

${ EnvironmentType } = ‘prod’

SolutionName = 'dtp-prod'


|**Resource**|**Organization Name for Resource**|
| :-: | :-: |
|App Registration (Client)|With manual deployment the user can name the app registration as they find it fit. With the automatic app registration, the app registration will be the same as the AppName specified at the time of deployment. i.e. if the app name is ‘dtp’, the client app registration’s name  will also be ‘dtp’|
|App Registration (Function)|With manual deployment the user can name the app registration as they find it fit. With the automatic app registration, the app registration will be in the format [AppName][api].  If the app name is ‘dtp’, the function app registration’s name  will be ‘dtpapi’|
|Azure Subscription Name|Automated script prompts for values if more than 1 subscription is present. It will also prompt for environment (AzureCloud, AzureUSGovernment) and will log in to proper environment|
|Site Name|<p>Automated deployment: The site name is the https://[ AppName].azurewebsites.us.</p><p>This is the name of the app service</p>|
|Azure Environment|dev \| test \| prod|
|Resource Group|rg-\<AppName>-\<EnvironmentType><###>|
|App Service (website)|Currently the host header for the website (i.e. the name of the app service will be the host header for the website.  If app service is created with name ‘dtp’, the website will be https://dtp.azurewebsites.us|
|App Service Plan|asp-\<AppName>-\<EnvironmentType>|
|Function App (API)|func-api-\<AppName>-\<EnvironmentType>|
|Key Vault|kv-\<AppName>-\<EnvironmentType>|
|Managed Identity|id-\<AppName>-\<EnvironmentType>|
|Network Interface|*System Created*|
|Network Security Group|nsg-\<AppName>-\<EnvironmentType>|
|Private DNS Zones|*System Created*|
|Private Endpoint – Function|pep-func-\<AppName>-\<EnvironmentType>|
|Private Endpoint – SQL DB|pep-sqldb-\<AppName>-\<EnvironmentType>|
|Private Endpoint – Storage – Audit|pep-st-audit-\<AppName>-\<EnvironmentType>|
|Private Endpoint – Storage – Audit Blob|pep-st-auditblob-\<AppName>-\<EnvironmentType>|
|Private Endpoint – Storage – Audit File|pep-st-auditfile-\<AppName>-\<EnvironmentType>|
|Private Endpoint – Storage – Blob|pep-st-blob-\<AppName>-\<EnvironmentType>|
|Private Endpoint – Storage – File|pep-st-file-\<AppName>-\<EnvironmentType>|
|Private Endpoint – Storage – Queue|pep-st-queue-\<AppName>-\<EnvironmentType>|
|Private Endpoint – Storage – Table|pep-st-table-\<AppName>-\<EnvironmentType>|


# **Scripted Installation (Recommended)**
The scripted installation is the preferred method for deploying the solution.  This approach will provide   a consistent deployment method eliminating human error. 

The following requirements are needed on the system executing the deployment:

- PowerShell version 7.0
- PowerShellGet version2.2.5
- Azure PowerShell version 5.6 and above
- Microsoft Graph Module 
- Bicep CLI version 0.4.1008
- Optional Microsoft Visual Studio Code with the following extensions: Bicep and PowerShell installed on the desktop or laptop executing the solution

ToDo: Add guidance on how to run automated scripts
# **Manual Installation – Overview of Resources to be Installed**
## ***Use this as a secondary option when unable to execute the automated option.  All steps are performed in the Azure Portal. Installation Order***
1. Start by creating the proper Resource Group
1. Virtual Network
   1. Identify the network address prefix. The recommended virtual network should have a CIDR range of /24 (255.255.255.0)
   1. Add the Subnets to the VNET which align to the solution requirements. The subnet size should have a CIDR range of /27 for the workloads, /26 for an Azure Bastion Host subnet, and a /28 for private endpoints.  
   1. Add Bastion (only if required for deployment, not common in production environments. This will require a /26 subnet)
1. Network Security Group
1. Key Vault
1. Storage Account V2 Premium
1. Audit Storage Account 
1. App Service (Web Client) 
1. Function App Premium is required for Virtual Network Integration
1. SQL Server (PaaS service)
1. SQL Database (PaaS )
1. Managed Identity
1. App Service Plan 
1. Cleanup (ToDo: Need better wording here)
   1. Update Storage
   1. Deploy web Client
   1. Deploy function app
   1. Network Interface

![](DTP%20Technical%20Service%20Guide.002.png)
> *Note:* 
>
> *1) Each of the following steps must be performed within the appropriate Resource Group.* 
>
> *2) When creating resources, some values may be pre-populated. Verify that each one is accurate as they are not consistently populated with the expected value.*
>
> *3) The images below utilize “XYZ” in place of “DTP”*

# **Manual Deployment – Tier 0 (Global Admin)**

## ***Subscription-Level Tasks***
### ***Subscription – Role***
*Note: This is a one-time subscription-level effort. If DTP has already been deployed on this subscription, this step will already be completed.*

1. Open the appropriate subscription
1. On the menu, select Access Control (IAM)
1. Select the Roles tab
1. Search for “DTP” to limit the number of roles showing

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.002.png)

5. Click on the view link for details
1. TODO: Describe how to create the two Microsoft.Storage Actions
1. TODO: Describe how to create the two Microsoft.Storage DataActions

## ***Step 1: Log in to Azure Subcription***
1. Navigate to your Azure Portal ie: Azure.Portal.com or Azure.Portal.US
1. Log in to your subscrition using the proper credentials 
## ***Step 2: Create a Resource Group***
1. Select Resource Group in the left hand menu or Log in to the Azure Portal and type “Resource Group” in the global search box.

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.003.png)

2. Select Create
1. Select the correct Subscription
1. Enter a Resource Group name, prefix with “rg-“. For example, “rg-dtp-dev-lt” signifies:
   1. rg: Resource Group
   1. dtp: Application
   1. dev/test/prod: Environment
1. Select the correct Region
1. Select the Next: Tags button
   1. Add tags if applicable
1. Select the Next: Review + Create button
1. Click Create

## ***Step 3: Create Virtual Network***
1. Navigate to the appropriate Resource Group created in step 2.
1. Click the Create button at the top of the resource group page.
1. Search for “Virtual Network” in the search bar. 

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.004.png)

4. Select the “Create” button, then “Virtual Network”
   1. Subscription: Select appropriate subscription
   1. Resource Group: Select appropriate Resource Group
   1. Name: Enter name for VNET, begin with “vnet-“, for example “vnet-dtp-dev-lt-001”
   1. Region: Select the appropriate region
1. Select the *Next: IP Address* button
   1. IPv4 Address Space: take the default but change the netmask to /24. For example, if presented with 10.3.0.0/16, change it to 10.3.0.0/24

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.005.png)

6. Add the following subnets, the first three octets are derived from the IPv4 address

|Subnet Name|Subnet Address Range|NAT Gateway|Services|
| :- | :- | :- | :- |
|default|###.###.###.0/27|None|0 Selected|
|webapp|###.###.###.32/27|None|0 Selected|
|function|###.###.###.64/27|None|0 Selected|
|storage|###.###.###.96/27|None|0 Selected|
|functionintegration|###.###.###.128/27|None|0 Selected|
|keyvault|###.###.###.160/27|None|0 Selected|

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.006.png)

7. Select the *Next: Security* button
   1. BastionHost: 
      1. When Bastion is not necessary (Some Production Environments)
         1. Set to “Disable”
      1. When Bastion is necessary 
         1. Set to “Enable”
         1. Bastion Name: prefix with “bast-“, for example “bast-dtp-dev-lt-001”
         1. AzureBastionSubnet Address: Use the first three octets of your IPv4 address and replace the fourth octet with “192”, then set the netmask to “/26”
            1. For example: ###.###.###.192/26
         1. Public IP Address: Select Create New
            1. Name: Enter a name, for example “pip-bast-dtp-dev-lt-001”
            1. Click OK

                ![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.007.png)

            1. DDoS Protection Standard: Set to “Disable”
            1. Firewall: Set to “Disable”

1. Select the *Next: Tags* button
   1. Create tags if appropriate
1. Select the *Next: Review + Create* button
1. Select the *Create* button

## ***Step 4: Create Network Security Group***
1. Navigate to the appropriate Resource Group created in step 2.
1. Click the Create button
1. In the search bar within Create a Resource, type “Network Security Group”

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.008.png)

4. Click on the Create dropdown, then select Network Security Group
   1. Subscription: Select appropriate subscription
   1. Resource Group: Select appropriate Resource Group
   1. Name: Enter name, it should start with “nsg-“, for example “nsg-dtp-dev-lt-001”
   1. Region: Select appropriate region
1. Select the *Next: Tags* button
   1. Enter tags if appropriate
1. Select the *Next: Review + Create* button
1. Select the *Create* button
### ***Associate Network Security Group to the Subnets that were created in the previous step.***
1. Open the Network Security Group you just created within your resource group
1. Open your Network Security Group
1. On the left menu, under Settings, select Subnets
1. Select the + Associate button and enter your Virtual Network for each of the following subnets:
   1. default
   1. webapp
   1. function
   1. storage
   1. functionintegration
   1. keyvault

\*\* Note:  AzureBastionSubnet will not have an NSG associated to it.


## ***Step 5: Create Key Vault***
1. Navigate to the appropriate Resource Group created in step 2.
1. Click the Create button
1. In the search bar within Create a Resource, type key Vault

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.009.png)

4. Select the Create button
   1. Subscription: Select the appropriate option, it may already be selected
   1. Resource Group: Select the appropriate option, it also may already be selected
   1. Key Vault Name: Enter name, prefix with “kv-“, for example “kv-dtp-dev-lt-001”
   1. Region: Select the appropriate region
   1. Pricing Tier: ”Premium”
   1. Soft Delete: Enabled
   1. Days to retain deleted vaults: “7”
   1. Purge Protection: “Enable Purge Protection”
1. Select the *Next* button -> Access Policy
   1. Azure Virtual Machines for deployment: unchecked/false
   1. Azure Resource Manager for template deployment: unchecked/false
   1. Azure Disk Encryption for volume encryption: unchecked/false
   1. Permission Model: “Vault access policy”
1. Select the Next button -> Networking
   1. Connectivity method: “All Networks”, this will be updated in later steps
1. Select the Next button -> Tag
   1. Add tags if appropriate
1. Select the Next button
1. Select the Create button

## ***Step 6: Create Storage Account***
1. Navigate to the appropriate Resource Group created in step 2.
1. Click the Create button
1. Select Create under Storage Account – or enter “Storage Account” in the search bar

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.010.png)

1. Subscription: Select appropriate subscription
1. Resource Group: Select the Resource Group you created earlier
1. Storage Account Name: Enter name, should start with “st“, for example “stdtpdevlt001”
1. Region: Select appropriate region
1. Performance: “Standard”
1. Redundancy: “Locally redundant storage (LRS)” 
1. Select the *Next: Advanced* button
   1. Security
      1. Require secure transfer for REST API operations: Checked\true
      1. Enable blob public access: Checked\true
      1. Enable storage account key access: Checked\true
      1. Minimum TLS version: Select Version 1.2
      1. Permitted scope for copy operations: “From any storage account”
         1. Note: this is currently a preview feature and not available in other environments at this point.
   1. Data Lake Storage Gen2
      1. Enable hierarchical namespace: Unchecked\false
   1. Blob Storage
      1. Enable SFTP: Unchecked\false
      1. Enable network file system v3: Unchecked\false
      1. Access Tier: Select “Hot”
   1. Azure Files
      1. Enable large file shares: Unchecked\false
1. Select the *Next: Networking* button
1. Network Connectivity
   1. Network Access: “Enable public access from all networks”
1. Click the Next: Data Protection button
1. Recovery
   1. Enable Point-in-time restore for containers: Unchecked\false
   1. Enable soft delete for blobs: Checked\true, 7 days
   1. Enable soft delete for containers: Checked\true, 7 days
   1. Enable soft delete for file shares: Checked\true, 7 days
1. Tracking
   1. Enable versioning for blobs: Checked\true
   1. Enable blob change feed: Checked\true
   1. Keep all logs: Selected.
      1. Access control
   1. Enable version-level immutability support: Unchecked
1. Select the *Next: Encryption* button
   1. Encryption Type: Select “Microsoft-managed keys (MMK)”. This will be modified later.
   1. Enable support for customer-managed keys: “All service types (blobs, files, tables, and queues)”
   1. Enable infrastructure encryption: Selected\true

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.011.png)

1. Select the *Next: Tags* button
   1. Add tags if necessary
1. Select the *Next: Review + Create* button
1. Select the Create button

## ***Step 7: Create Audit Storage Account***
1. Navigate to the appropriate Resource Group created in step 2., and click the Create button
1. Select Create under Storage Account – or enter “Storage Account” in the search bar

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.012.png)

1. Subscription: Select appropriate subscription
1. Resource Group: Select the Resource Group you created earlier 
1. Storage Account Name: Enter name, prefix with “st“, for example “stdtpdevlt001audit”
1. Region: Select appropriate region
1. Performance: Standard
1. Redundancy: Select option “Locally redundant storage (LRS)”
1. Select the Next: Advanced button
   1. Security
      1. Require secure transfer for REST API operations: Checked\true
      1. Enable blob public access: Checked\true
      1. Enable storage account key access: Checked\true
      1. Minimum TLS version: Select Version 1.2
      1. Permitted scope for copy operations: From any storage account
  \*\*Note: this is currently a preview feature and not available in other environments at this point.
   1. Data Lake Storage Gen2
      1. Enable hierarchical namespace: Unchecked\false
   1. Blob Storage
      1. Enable SFTP: Unchecked\false
      1. Enable network file system v3: Unchecked\false
      1. Allow cross-tenant replication: Selected\true
      1. Access Tier: Select “Hot”
   1. Azure Files
      1. Enable large file shares: Unchecked\false
1. Select the *Next: Networking* button
   1. Network Access: “Enable public access from all networks”, this will be modified later
1. Click the Next: Data Protection button
1. Recovery:
   1. Enable Point-in-time restore for containers: Unchecked\false
   1. Enable soft delete for blobs: Checked\true, 7 days
   1. Enable soft delete for containers: Checked\true, 7 days
   1. Enable soft delete for file shares: Checked\true, 7 days
      1. Tracking:
   1. Enable versioning for blobs: Checked\true
   1. Enable blob change feed: Checked\true
   1. Keep all logs: Selected\true
      1. Access control:
   1. Enable version-level immutability support: UnChecked/false
1. Select the *Next: Encryption* button
   1. Encryption Type: Select Microsoft-managed keys (MMK). This will be modified in later steps.
   1. Enable support for customer-managed keys: All service types (blobs, files, tables, and queues)
   1. Enable infrastructure encryption: Selected\true

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.013.png)

1. Select the *Next: Tags* button
   1. Add tags if necessary
1. Select the *Next: Review + Create* button
1. Select the Create button

## ***Step 8: Create App Service (Web App)***
1. Within your Resource Group, select Create
1. Search for “Web App”
1. Under Web App, select Create, then Web App.

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.014.png)

1. Project Details
   1. Subscription: Select appropriate subscription
   1. Resource Group: Select appropriate Resource Group
1. Instance Details
   1. Name: Enter Web App Name
      1. Option 1: “datatransfer”
      1. Option 2: Should start with “app-“, for example “app-dtp-dev-lt-001”
   1. Publish: “Code”
   1. Runtime Stack: .NET 6 (LTS)
   1. Operating System: Windows
   1. Region: Select the appropriate Region
1. App Service Plan
   1. Windows Plan: Select the Create New link
      1. Name: prefix with “asp-“, for example “asp-dtp-dev-lt-001”

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.015.png)

1. SKU and Size: 
   1. For Production Environment: P2V2
   1. For Dev or Test Environment: Standard S1

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.016.png)

1. Click the *Next: Networking* button
   1. Enable Network Injection: “Off”
1. Click the *Next: Monitoring* button
   1. Enable Application Insights: Yes
   1. Application Insights: Select the Create New link
      1. Name: prefix with “appi-“, for example “appi-dtp-dev-lt-001”
      1. Click OK
1. Click the *Next: Tags* button
   1. Add tags if necessary
1. Click the *Next: Review + Create* button
1. Click the *Create* button

## ***Create Function App***
1. Within your Resource Group, select Create
1. Search for “Function App”

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.017.png)

3. Select Create
   1. Subscription: Select appropriate subscription
   1. Resource Group: Select appropriate Resource Group
   1. Name: Enter Function App Name
      1. Option 1: “datatransferapi”
      1. Option 2: Should start with “func-“, for example “func-dtp-dev-lt-001”
   1. ` `Publish: Code
   1. Runtime Stack: .NET
   1. Version: 6
   1. Region: Select the appropriate Region
   1. Operating System: Windows
   1. Plan Type: App Service Plan
   1. Windows Plan: Select the existing plan from your Resource Group. It should be prefixed with “asp-“, but you can always confirm its name before selecting.
   1. SKU and Size: Standard S1
1. Click the *Next: Hosting* button
   1. Storage Account: Select the existing non-audit storage account
1. Click the *Next: Networking* button
   1. Enable Network Injection: “Off”
1. Click the *Next: Monitoring* button
   1. Enable Application Insights: Yes
   1. Application Insights: Select the application insights you’ve already created. The proper naming convention for this is a prefix of “appi-“
1. Click the *Next: Tags* button
   1. Add tags if necessary
1. Click the *Next: Review + Create* button
1. Click the *Create* button
### ***Function App – Add VNet Integration***
1. Open the function app in your resource group
1. In the menu under Settings, select Networking
1. In the Outbound Traffic pane, select the VNet Integration link
1. Add VNet
   1. Subscription: Select the appropriate subscription
   1. Virtual Network: Select the virtual network in your resource group
   1. Subnet: “Select Existing”, then choose “Function Integration”
   1. Click the OK button

## ***Create SQL Database***
1. Ensure you are on the Resource Group page
1. Click the Create button
1. In the search bar within Create a Resource, type “SQL Database”

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.018.png)

41. Select Create, then SQL Database
   1. Subscription: Select appropriate subscription
   1. Resource Group: Select appropriate Resource Group
   1. Database Name: Prefix with “sqldb-“, for example “sqldb-dtp-dev-lt-001“
   1. Server: Select the Create New link
      1. Server Name: prefix with “sql-“, for example “sql-dtp-dev-lt-001”
      1. Location: Select the appropriate location
      1. Authentication Method: “Use SQL Authentication”
      1. Server Admin Login: Enter administrator name (*you will need to record/remember this*)
      1. Password/Confirm Password: Enter password (*you will need to record/remember this*)
      1. Click OK
   1. SQL Elastic Pool: “No”
   1. Compute + Storage: “General Purpose” (Gen 5, 2 vCores, 32GB storage)
   1. Backup Storage Redundancy: “Locally-redundant backup storage”
1. Click the *Next: Networking* button
   1. Network Connectivity
      1. Connectivity Method: “No access”. This will be modified later to use private endpoints.
   1. Connection Policy
      1. Connection Policy: “Default”
   1. Encrypted Connections
      1. Minimum TLS Version: “TLS 1.2”
1. Select the *Next: Security* button
   1. Microsoft Defender: Do not enable, choose “Not Now”
   1. Ledger: “Not Configured”
   1. Identity: “Not Enabled”
   1. Transparent Data Encryption: “Service-managed key selected”
1. Select the *Next: Additional Settings* button
   1. Use Existing Data: Select “None”
   1. Collation: “SQL\_Latin1\_General\_CP1\_CI\_AS”, which should be the default
   1. Maintenance Window: System default (5pm to 8am)
1. Select the *Next: Tags* button
   1. Create tags if necessary
1. Select the *Next: Review + Create* button
1. Select the *Create* button
### ***SQL – Create Table***
1. Within your resource group, open the SQL Server resource
1. On the menu, under Security, select Networking
1. Under Firewall Rules, click on "Add your client IPv4 address"
   1. Just below the firewall rules, under Exceptions, click "Allow Azure services and resources to access this server"
1. Click Save
1. Open the SQL Database associated with your resource group
1. On the menu, select Query Editor
1. Log in with the authentication information you set up earlier
1. You will be prompted for a certificate
1. Get the script from GitHub (https://github.com/microsoft/dtp)
   1. In the code tab, go to Deploy/SQL and select "Create-Audit-Table.sql"
   1. Copy the script components
1. Paste the SQL script contents into the Query Editor in Azure
1. Select "Run"
1. This runs very quick and you should expect to receive a "Query Received" message below. You can also verify the table was created by selecting "Tables" to the left of the editor. You should see the Audit table
1. Now we undo the special settings that allowed us to perform this change
   1. Go back to your SQL Server resource (not SQL Database). You will receive a warning about unsaved edits being discarded. Select OK, our script has already created the table. 
   1. Select Networking from the menu, just under the Security heading
      1. Uncheck “Allow Azure services and resources to access this server” 
      1. Delete the Firewall rule
      1. Above, under Public Network Access, select "Disable” 
      1. Click Save



## ***Private Endpoints***
### ***Private Endpoints – Storage Account - Queue***
1. Navigate to the storage account page by clicking on the resource in the left menu panel under Security + Networking, then select Networking
1. Select Private Endpoint Connections
1. Perform the following steps to create Private Endpoints for a queue.
1. Select + Private endpoint to create a private endpoint
   1. Subscription: Select the appropriate Subscription
   1. Resource Group: Select the appropriate Resource Group
   1. Name: Enter name, prefix with “pep-“, for example “pep-st-queue-dtp-dev-001”
   1. Region: Select the appropriate region
1. Select the Next: Resource button
   1. Target Sub-Resource: “Queue”
1. Select the Next: Virtual Network button
   1. Virtual Network: Select the appropriate VNET associated with your Resource Group
   1. Subnet: Select the Storage Subnet you created
   1. Private IP Configuration: Dynamically allocate IP address
   1. Integrate with Private DNS Zone: Yes
   1. Subscription: Select the appropriate Subscription
   1. Resource Group: Select the appropriate Resource Group
   1. Private DNS Zone: The default is fine, just confirm this is a government address
1. Select the Next: Tags button
1. Add tags if applicable
1. Select the Next: Review + Create button
1. Select the Create button
### ***Private Endpoints – Storage Account - Blob***
1. Navigate to the storage account page by clicking on the resource in the left menu panel under Security + Networking, then select Networking
1. Select Private Endpoint Connections
1. Perform the following steps to create Private Endpoints for a blob.
   1. Select + Private endpoint to create a private endpoint
      1. Subscription: Select the appropriate Subscription
      1. Resource Group: Select the appropriate Resource Group
      1. Name: Enter name, prefix with “pep-“, for example “pep-st-blob-dtp-dev-lt-001”
      1. Region: Select the appropriate region
   1. Select the Next: Resource button
      1. Target Sub-Resource: “blob”
   1. Select the Next: Virtual Network button
      1. Virtual Network: Select the appropriate VNET associated with your Resource Group
      1. Subnet: Select the Storage Subnet that you created
      1. Private IP Configuration: “Dynamically allocate IP address”
      1. Integrate with Private DNS Zone: Yes
      1. Subscription: Select the appropriate Subscription
      1. Resource Group: Select the appropriate Resource Group
      1. Private DNS Zone: The default is fine, just confirm this is a government address
   1. Select the Next: Tags button
   1. Add tags if applicable
   1. Select the Next: Review + Create button
   1. Select the Create button
### ***Private Endpoints – Storage Account - File***
1. From the storage account page, on the left menu, under Security + Networking, select Networking
1. Select Private Endpoint Connections
1. Perform the following steps to create Private Endpoints for a file.
   1. Select + Private endpoint to create a private endpoint
      1. Subscription: Select the appropriate Subscription
      1. Resource Group: Select the appropriate Resource Group
      1. Name: Enter name, prefix with “pep-“, for example “pep-st-file-dtp-dev-lt-001”
      1. Region: Select the appropriate region
   1. Select the Next: Resource button
      1. Target Sub-Resource: “File”
   1. Select the Next: Virtual Network button
      1. Virtual Network: Select the appropriate VNET associated with your Resource Group
      1. Subnet: Select the subnet associated with Storage
      1. Private IP Configuration: Dynamically allocate IP address
      1. Integrate with Private DNS Zone: Yes
      1. Configuration Name: The default is fine
      1. Subscription: Select the appropriate Subscription
      1. Resource Group: Select the appropriate Resource Group
      1. Private DNS Zone: The default is fine, just confirm this is a government address
   1. Select the Next: Tags button
      1. Add tags if applicable
   1. Select the Next: Review + Create button
   1. Select the Create button
### ***Private Endpoints – Storage Account - Table***
1. From the storage account page, on the left menu, under Security + Networking, select Networking
1. Select Private Endpoint Connections
1. Perform the following steps to create Private Endpoints for a table.
   1. Select + Private endpoint to create a private endpoint
      1. Subscription: Select the appropriate Subscription
      1. Resource Group: Select the appropriate Resource Group
      1. Name: Enter name, prefix with “pep-st-table-dtp-dev-lt-001”
      1. Region: Select the appropriate region
   1. Select the Next: Resource button
      1. Target Sub-Resource: “Table”
   1. Select the Next: Virtual Network button
      1. Virtual Network: Select the appropriate VNET associated with your Resource Group
      1. Subnet: Select the subnet associated with Storage
      1. Private IP Configuration: Dynamically allocate IP address
      1. Integrate with Private DNS Zone: Yes
      1. Configuration Name: The default is fine
      1. Subscription: Select the appropriate Subscription
      1. Resource Group: Select the appropriate Resource Group
      1. Private DNS Zone: The default is fine, just confirm this is a government address
   1. Select the Next: Tags button
      1. Add tags if applicable
   1. Select the Next: Review + Create button
   1. Select the Create button
### ***Private Endpoints – Audit Storage Account - Blob***
1. From the audit storage account page, on the left menu, under Security + Networking, select Networking
1. Select Private Endpoint Connections
1. Perform the following steps to create Private Endpoints for a blob.
   1. Select + Private endpoint to create a private endpoint
      1. Subscription: Select the appropriate Subscription
      1. Resource Group: Select the appropriate Resource Group
      1. Name: Enter name, prefix with “pep-“, for example “pep-staudit-blob-dtp-dev-lt-001”
      1. Region: Select the appropriate region
   1. Select the Next: Resource button
      1. Target Sub-Resource: “blob”
   1. Select the Next: Virtual Network button
      1. Virtual Network: Select the appropriate VNET associated with your Resource Group
      1. Subnet: Select Storage
      1. Private IP Configuration: “Dynamically allocate IP address”
      1. Integrate with Private DNS Zone: Yes
      1. Subscription: Select the appropriate Subscription
      1. Resource Group: Select the appropriate Resource Group
      1. Private DNS Zone: The default is fine, just confirm this is a government address
   1. Select the Next: Tags button
   1. Add tags if applicable
   1. Select the Next: Review + Create button
   1. Select the Create button

## ***Customer Managed Keys (CMK)***
### ***CMK - Audit Storage Account***
1. From the audit storage account page, on the left menu, under Security + Networking, select Encryption
1. Encryption Selection
   1. Encryption Type: “Customer Managed Keys”
1. Key Selection – Select Change Key link at the bottom of the page.
   1. Encryption Key: Select the link: “Select from key vault”
   1. Key Vault and Key: Select the “Select a key vault and key” link
      1. Subscription: Select the appropriate subscription
      1. Key Vault: Select the Key Vault created in your Resource Group
      1. Key: Select the “Create new key” link
         1. Options: “Generate”
         1. Name: Prefix with “cmk-“, for example “cmk-staudit-dtp-dev-lt-001”
         1. Key Type: “RSA”
         1. RSA Key Size: “2048”
         1. Set Activation Date: Unchecked/false
         1. Set Expiration Date: Unchecked/false
         1. Enabled: Yes/true
         1. Tags: 
         1. Set key rotation policy: “Not Configured”
         1. Select the Create button
      1. Click the Select button

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.019.png)

4. Select the Save button
1. Optional: Verify the access policy for the Key Vault has been setup
   1. Open the Key Vault associated with your resource group
   1. On the left menu, under Settings, select Access Policies
   1. You should now have a policy under Application listing your Audit Storage Account like this image below

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.020.png)

### ***CMK - Storage Account***
1. From the storage account page, on the left menu, under Security + Networking, select Encryption
1. Encryption Type: “Customer Managed Keys”
1. Encryption Key: Select the link: “Select a key vault and key”
   1. Subscription: Select the appropriate subscription
   1. Key Vault: Select the Key Vault created in your Resource Group
   1. Key: Select the “Create new key” link
      1. Options: “Generate”
      1. Name: Prefix with “cmk-“, for example “cmk-st-dtp-dev-lt-001”
      1. Key Type: “RSA”
      1. RSA Key Size: “2048”
      1. Set Activation Date: Unchecked/false
      1. Set Expiration Date: Unchecked/false
      1. Enabled: Yes/true
      1. Tags: 
      1. Set key rotation policy: “Not Configured”
      1. Select the Create button
1. Click the Select button
1. Identity Type: “System-Assigned”
1. Select the Save button
1. Optional: Verify the access policy for the Key Vault has been setup
   1. Open the Key Vault associated with your resource group
   1. On the left menu, under Settings, select Access Policies
   1. You should now have a policy under Application listing your Storage Account similar to this image below

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.021.png)

## ***Private Endpoints – Part Two***

### ***Key Vault – Add Private Endpoints***
1. Open the Key Vault associated with your resource group
1. On the left menu under Settings, select Networking
1. Select Private Endpoint Connections
1. Select Create
   1. Subscription: Select the appropriate subscription
   1. Resource Group: Select the appropriate resource group
   1. Name: Prefix with “pep-“, for example “pep-kv-dtp-dev-lt-001”
1. Select the Next: Resource button
   1. Connection Method: “Connect to an Azure resource in my directory”
   1. Subscription: Select the appropriate subscription
   1. Resource Type: Search for “vaults”, then select “Microsoft.KeyVault/vaults”
   1. Resource: Select the Key Vault associated with your resource group
   1. Target Sub-Resource: “Vault”
1. Select the Next: Virtual Network button
   1. Virtual Network: Select the appropriate virtual network
   1. Subnet: Select “KeyVault”
   1. Private IP Configuration: "Dynamically allocate IP Address”
   1. Integrate with Private DNS Zone: “Yes”
   1. Subscription: Select the appropriate subscription
   1. Resource Group: Select the appropriate resource group
1. Select the Next: Tags button
   1. Enter tags if appropriate
1. Select the Next: Review + Create button
1. Select the Create button
### ***Function App - Add Private Endpoints***
1. Open the function app associated with your resource group
1. In the menu under Settings, select Networking
1. In the Inbound Traffic pane, select the Private Endpoints link
1. Click Add
   1. Name: Prefix with “pep-“, for example “pep-func-dtp-dev-lt-001”
   1. Subscription: Select the appropriate subscription
   1. Virtual Network: Select the virtual network associated with your resource group
   1. Subnet: “webapp”
   1. Integrate with Private DNS Zone: “Yes”
   1. Click the OK button

### ***Key Vault – Update Selected Networks***
1. Open the Key Vault associated with your resource group
1. On the menu under Settings, select Networking
1. Allow Access From: “Selected Networks”
1. Click Save

### ***Function App – Verify Private Endpoint***
1. Open the Private Endpoint for the function app within your resource group
1. At the bottom under FQDN, you should see the two web addresses for your client and function apps

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.022.png)

### ***Storage Account – Add File Shares***
1. Open the storage account in your resource group
1. On the menu under Data Storage, select File Shares
1. Select + File Share
   1. Name: “dtpapi” (this must be exact)
   1. Tier: “Transaction Optimized”
   1. Click the Create button

### ***Storage Account – Enable CORS*** 
1. Open the storage account in your resource group 
1. On the menu under Settings, select Resource Sharing (CORS) 
1. Add a CORS record for Blob Service 
   1. Allowed Origins: This is the URL of your client application 
   1. Allowed Methods: GET, POST, PUT 
   1. Allowed Headers: * 
   1. Exposed Headers: * 
   1. Max Age: 30 
   1. Click on Save

### ***Storage Account – Add Data Storage***
Ensure the following exist and add if they do not. Navigate to the appropriate resource group you created in step 2.
 
## Queues
1. Under Data Storage, select Queues 
1. The following queues must exist exactly as described below 
   1. deployarmtemplates 
   1. status 
   1. transfers 
## Tables 
1. Under Data Storage, select Tables 
1. The following table must exist exactly as described below 
   1. transfers 

### ***Function App – Enable CORS***
1. Open the function app in your resource group
1. On the menu under API Tools, select CORS
1. Enable Access-Control-Allow-Credentials: Checked
1. Allowed Origins: 
   1. Remove any existing URL
   1. Add the client web app URL, for example “https://datatransfer.azurewebsites.us”
      1. To get this value:
         1. Open the App Service in your Resource Group
         1. Copy URL
   1. Click Save

### ***App Service - Add Private Endpoints***
1. Open the App Service associated with your resource group
1. In the menu under Settings, select Networking
1. In the Inbound Traffic pane, select the Private Endpoints link
1. Click Add
   1. Name: Prefix with “pep-“, for example “pep-app-dtp-dev-lt-001”
   1. Subscription: Select the appropriate subscription
   1. Virtual Network: Select the virtual network associated with your resource group
   1. Subnet: “Function”
   1. Integrate with Private DNS Zone: “Yes”
   1. Click the OK button


## ***Function App – Configure Application Settings***
You can individually add new application settings by clicking “New Application Settings” or by clicking “Advanced Edit”. The latter option presents you with JSON text (JavaScript Object Notational), which is a lightweight format for storing and transporting data. Instructions below are for updating the JSON file. Review the template for guidance on where to find the information to fill each parameters value.

1. On the menu under Settings, select Configuration
1. Click on Advanced edit and you will be presented with JSON text. Ensure you don’t modify this original text, it’s good to go as is and we’ll be adding to it.
   1. This should include:
      1. APPINSIGHTS\_INSTRUMENTATIONKEY
      1. APPLICATIONINSIGHTS\_CONNECTION\_STRING
      1. AzureWebJobsStorage
      1. FUNCTIONS\_EXTENSION\_VERSION
      1. FUNCTIONS\_WORKER\_RUNTIME

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.023.png)

3. Open a code editor (Visual Studio Code works well) that supports JSON
1. Paste the contents of the template file named FunctionAppConfigurationTemplate1of2.json into a new json file.
   1. Note: this template contains comments – lines preceded with “//”. 
      1. These comments are steps on how to get the information to populate the value. Once you input the value, remove the corresponding comment lines.
      1. All comments must be removed from the file before pasting into Azure, comments are invalid in JSON text.
   1. Using the instructions provided in the template, update the appropriate parameters (looks like “value”: “”)
1. Ensure all commented lines have been deleted
1. Copy JSON text and paste back into Azure Function App Configuration App Settings.
   1. All changes will need to be added between the last ending curly bracket “}” and square bracket “]”, continuing the format of “{…some text…},{…some text}] – therefor, do not copy the square brackets.

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.024.png)

7. Next, paste the contents of the template, placing the comma after the last curly bracket that was pasted in from Azure. There should not be any blank lines.
1. After your template is filled out, and you do not have any errors (denoted by a red squiggly line under the text causing the error). In the example below, we’ve left a commented line. 
   1. Common Errors:
      1. Comments (“//”) – delete these lines
      1. Missing comma between sets of curly brackets {}
      1. Invalid square brackets [] or curly brackets {}. The file begins and ends with square brackets, and each set of parameters begin and end with curly brackets.

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.025.png)

9. After text has been copied, select the OK button
1. Click on the Save button, and then the Continue button
1. Confirm this was successful by opening the storage account within your resource group???
   1. On the menu under Data Storage, select File Shares
   1. Click on “dtpapi”

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.026.png)

1. You should now see the following file shares

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.027.png)

## ***Key Vault – Establish Access***
1. Open the Key Vault within your resource group
1. On the menu under Settings, select Networking
1. Open another browser/tab and go to <https://www.whatismyip.com>
1. Copy the IPv4 address

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.028.png)

5. Go back to your tab/browser showing Key Vault Networking
1. Under IP Address or CIDR, paste your IP address you just copied

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.029.png)

7. Click Save
1. Verify this by selecting Keys under the Settings menu. If you see keys, this was successful.
## ***SQL Server***
### ***SQL Server - Setup Transparent Data Encryption***
1. Open the SQL Server resource within your resource group
1. On the menu under Security, select Transparent Data Encryption
   1. Transparent Data Encryption: “Customer Managed Key”
   1. Key Selection Method: “Select a key”
   1. Key: Select the “Change Key” link
      1. Subscription: Select the appropriate subscription
      1. Key Vault: Select the Key Vault associated with your resource group
      1. Key: Select “Create New Key"
         1. Options: “Generate”
         1. Name: Prefix with “cmk-“, for example “cmk-sql-dtp-dev-lt-001”
         1. Key Type: “RSA”
         1. RSA Key Size: “2048”
         1. Set Activation Date: Unchecked/false
         1. Set Expiration Date: Unchecked/false
         1. Enabled: “Yes”
         1. Tags: Add as appropriate
         1. Set Key Rotation policy: Not configured
         1. Select the Create button
   1. Select the Select button
   1. Select Save
   1. Validate
      1. Open the Key Vault within your resource group
         1. On the menu under Settings, select Access Policies
         1. An access policy should now be visible under the Application group. This may take a moment and may require a refresh.

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.030.png)

1. On the SQL Server blade, on the menu under Security, select Identity
1. Under System assigned managed identity, Status should equal “On”

![DTP Tech Service Guide Image](/.attachments/DTP%20Technical%20Service%20Guide.031.png)

### ***SQL Server Private Endpoint***
1. Open the SQL Server resource in your resource group
1. On the menu under Security, select Networking
1. Click on the Private Access tab
1. Select Create a private endpoint
   1. Subscription: Select the appropriate subscription
   1. Resource Group: Select the appropriate resource group
   1. Name: Prefix with “pep-“, for example “pep-sql-dtp-dev-lt-001”
   1. Region: Select the appropriate region
1. Select the Next: Resource button
   1. Target Sub-Resource: “sqlServer”
1. Select the Next: Virtual Network button
   1. Virtual Network: Select the virtual network in your resource group
   1. Subnet: “Storage”
   1. Private IP Configuration: “Dynamically allocate IP Address”
   1. Integrate with DNS Zone: “Yes”
   1. Subscription: Select the appropriate subscription
   1. Resource Group: Select the appropriate resource group
1. Select the Next: Tags button
   1. Add tags if appropriate
1. Select the Next: Review + Create button
1. Select the Create button

## ***Tier 0 – Cleanup & Verify***
1. Open your Resource Group and locate your Private DNS Zones

   ![DTP Tech Service Guide Image](/.attachments/DTP1.002.png)

1. Select “privatelink.azurewebsites.us”

   ![DTP Tech Service Guide Image](/.attachments/DTP1.003.png)

1. Settings: You should have one for each of the records in the image above
   1. Name: datatransfer.scm or datatransferapi.scm or datatransfer or datatransferapi
   1. Type: “A”
   1. TTL: “10”
   1. TTL Unit: “Seconds”
   1. IP Address: Get the IP Address from the Private Endpoint for your function app, or your client web app as appropriate
1. Required records
   1. datatransfer.scm
   1. datatransferapi.scm
   1. datatransfer
   1. datatransferapi

## ***Verify Private Links***
### ***privatelink.blob.core.usgovcloudapi.net***
1. blob: Contains two records, one for each storage account

   ![DTP Tech Service Guide Image](/.attachments/DTP1.004.png)

1. Name: Use the name for the storage account you are referencing
1. Type: “A”
1. TTL: “10”
1. TTL Unit: “Seconds”
1. IP Address: Get the IP Address from the Private Endpoint for the storage account

### ***privatelink.database.core.usgovcloudapi.net***
1. database: Contains a single record, settings as follows:

   ![DTP Tech Service Guide Image](/.attachments/DTP1.005.png)

1. Name: Prefix with “sql-“, for example “sql-dtp-dev-lt-001”
1. Type: “A”
1. TTL: “10”
1. TTL Unit: “Seconds”
1. IP Address: Get the IP Address from the Private Endpoint for SQL

### ***privatelink.file.core.usgovcloudapi.net***
1. File: Contains a single record, settings as follows:

   ![DTP Tech Service Guide Image](/.attachments/DTP1.006.png)

1. Name: Use the name for your non-audit storage account
1. Type: “A”
1. TTL: “10”
1. TTL Unit: “Seconds”
1. IP Address: Get the IP Address from the Private Endpoint for the storage account

### ***privatelink.queue.core.usgovcloudapi.net***
1. queue: Contains a single record, settings as follows:

   ![DTP Tech Service Guide Image](/.attachments/DTP1.007.png)

1. Name: Use the name for your non-audit storage account
1. Type: “A”
1. TTL: “10”
1. TTL Unit: “Seconds”
1. IP Address: Get the IP Address from the Private Endpoint for the storage account

### ***privatelink.table.core.usgovcloudapi.net***
1. table: Contains a single record, settings as follows:

   ![DTP Tech Service Guide Image](/.attachments/DTP1.008.png)

1. Name: Use the name for your non-audit storage account
1. Type: “A”
1. TTL: “10”
1. TTL Unit: “Seconds”
1. IP Address: Get the IP Address from the Private Endpoint for the storage account

### ***privatelink.vaultcore.usgovcloudapi.net***
1. vaultcore: Contains a single record, settings as follows:

   ![DTP Tech Service Guide Image](/.attachments/DTP1.009.png)

1. Name: Use the name for your Key Vault
1. Type: “A”
1. TTL: “10”
1. TTL Unit: “Seconds”
1. IP Address: Get the IP Address from the Private Endpoint for the Key Vault


# **Manual Deployment – Tier 1 (Contributor)**
## ***Azure Active Directory (AAD) – Add App Registrations (Tier 1 Support)***

### ***App Registration – Function App***
1. In the Azure Active Directory (AAD) blade, click Add, followed by App Registration

   ![DTP Tech Service Guide Image](/.attachments/DTP1.010.png)

1. Name: Use the same name as the Function App in your resource group
1. Who can use this application or access this API: “Accounts in this organization directory only”
1. Redirect URI: 
   1. Single-page Application (SPA)
   1. Paste the Function App URL
1. Select the Register button
1. When registration completes, select the Authentication menu option under Manage
1. Under Grant Types, it must specify “Your Redirect URI is eligible for the Authorization Code Flow with PKCE”.

   ![DTP Tech Service Guide Image](/.attachments/DTP1.011.png)

1. Note: In development environments, you may want to add the URL <http://localhost:3000> but it is not required.
#### ***Function App – Certificates & Secrets***
1. On the App Registration blade for the function app (API)
1. On the menu under Manage, select Certificates & Secrets
1. Select Client Secrets
1. Select New Client Secret
   1. Description: Add description, for example “DataTransferAPI”
   1. Expires “24 months”
1. Click the Add button
1. **IMPORTANT**: You must copy the value of the client secret you just created. Once you refresh, it will never again be visible. If the key is lost or expired, simply create a new one.
#### ***Function App – API Permissions***
1. On the App Registration blade for the function app (API)
1. On the menu under Manage, select API Permissions
1. Under Configured Permissions, select Add a Permission
   1. Select the Azure Storage pane

      ![DTP Tech Service Guide Image](/.attachments/DTP1.012.png)

1. Select the Delegated Permissions pane
1. Check the “user\_impersonation” checkbox

   ![DTP Tech Service Guide Image](/.attachments/DTP1.013.png)

1. Click on the Add Permissions button
1. Select Grant Admin consent for… and then select the Yes button that pops up

   ![DTP Tech Service Guide Image](/.attachments/DTP1.014.png)

#### ***Function App – Expose an API***
1. On the App Registration blade for the function app (API)
1. On the menu under Manage, select Expose an API
1. Click the Set link next to Application ID URI

   ![DTP Tech Service Guide Image](/.attachments/DTP1.015.png)

1. Click the Save button
1. The Application ID URI is now set

   ![DTP Tech Service Guide Image](/.attachments/DTP1.016.png)

1. Select Add a Scope
   1. Scope Name: There is no standard, and this is not referenced in code. An example is “DTP.Standard.Use”
   1. Who can consent: “Admins and Users”
   1. Admin consent display name: Suggested text “Permits use of DTP via front-end app”
   1. Admin consent description: Suggested text “Permits use of DTP via front-end app”
   1. User consent display name: Suggested text " Permits use of DTP via front-end app”
   1. User consent description: “Permits use of DTP via front-end app”
   1. State: “Enabled”
   1. Select the Add Scope button

#### ***Function App – App Roles***
1. On the App Registration blade for the function app (API)
1. On the menu under Manage, select App Roles
1. Select Create App Role to create the User Role
   1. Display Name: Suggested text “DTP API Users”
   1. Allowed member types: “Users/Groups”
   1. Value: Suggested text “DTPAPI.Users”
   1. Description: Suggested text “Permitted to use DTP”
   1. Do you want to enable this app role: Checked/true
   1. Click the Apply button
1. Select Create App Role to create the Admin Role
   1. Display Name: Suggested text “DTP API Admins”
   1. Allowed member types: “Users/Groups”
   1. Value: Suggested text “DTPAPI.Admins”
   1. Description: Suggested text “Permits admin use of DTP”
   1. Do you want to enable this app role: Checked/true
   1. Click the Apply button

#### ***Function App – Owners***
1. On the App Registration blade for the function app (API)
1. On the menu under Manage, select Owners
1. Permission should be granted to users who need to maintain/update the App Registration

#### ***Function App – Manifest***
1. On the App Registration blade for the function app (API)
1. On the menu under Manage, select Manifest
1. In the JSON text, look for this line of text "accessTokenAcceptedVersion": null,
1. Replace “null” with “2”

   |Before |After|
   | :-: | :-: |
   |![DTP Tech Service Guide Image](/.attachments/DTP1.017.png)|![DTP Tech Service Guide Image](/.attachments/DTP1.018.png)|
1. Click Save
   1. Note: If the Save button is disabled, you have introduced a problem with the JSON. There should be a comma after the 2, as in “2,”. If that doesn’t fix it, discard your changes, and try again.

### ***App Registration – Client Web App***
1. In the Azure Active Directory (AAD) blade, click Add, followed by App Registration

   ![DTP Tech Service Guide Image](/.attachments/DTP1.010.png)

1. Name: Use the same name as the App Service in your resource group
1. Who can use this application or access this API: “Accounts in this organization directory only”
1. Redirect URI: 
   1. Single-page Application (SPA)
   1. Paste the App Service (web app) URL

      ![DTP Tech Service Guide Image](/.attachments/DTP1.019.png)

1. Select the Register button

#### ***Client Web App – API Permissions***
1. On the App Registration blade for the client web app
1. On the menu under Manage, select API Permissions
1. Under Configured Permissions, select Add a Permission
   1. Select My APIs
      1. Select the permission just created for the API
         1. Select the Delegated Permission block
         1. Select the checkbox for the available permission
         1. Click the Add Permissions button
1. Click on Grant Admin Consent
   
   ![DTP Tech Service Guide Image](/.attachments/DTP2.002.png)

#### ***Client Web App – App Roles***
1. On the App Registration blade for the client web app
1. On the menu under Manage, select App Roles
1. Select Create App Role to create the User Role
   1. Display Name: Suggested text “DTP Users”
   1. Allowed member types: “Users/Groups”
   1. Value: Suggested text “DTP.Users”
   1. Description: Suggested text “Allows standard use of DTP”
   1. Do you want to enable this app role: Checked/true
   1. Click the Apply button
1. Select Create App Role to create the Admin Role
   1. Display Name: Suggested text “DTP Admins”
   1. Allowed member types: “Users/Groups”
   1. Value: Suggested text “DTP.Admins”
   1. Description: Suggested text “Allows admin use of DTP”
   1. Do you want to enable this app role: Checked/true
   1. Click the Apply button

#### ***Client Web App – Owners***
1. On the App Registration blade for the client web app
1. On the menu under Manage, select Owners
1. Permission should be granted to users who need to maintain/update this App Registration

#### ***Client Web App – Manifest***
1. On the App Registration blade for the client web app
1. On the menu under Manage, select Manifest
1. In the JSON text, look for this line of text "accessTokenAcceptedVersion": null,”
1. Replace “null” with “2”

   |Before|After|
   | :-: | :-: |
   |![DTP Tech Service Guide Image](/.attachments/DTP2.003.png)|![DTP Tech Service Guide Image](/.attachments/DTP2.004.png)|
1. Click Save

Note: If the Save button is disabled, you have introduced a problem with the JSON. There should be a comma after the 2, as in “2,”. If that doesn’t fix it, discard your changes, and try again.

### ***Enterprise Applications - Client Web App***
*Note: This step will be replaced by assigning users to security groups, but the current development environment is limiting user assignment to the following steps.*
#### ***Client Web App – Assign Permissions***
1. Open Azure Active Directory
1. On the menu, under Manage, select Enterprise Applications
1. Locate and open your client web application
1. On the menu under Manage, select Users and Groups
1. Select Add User/Group
   1. Click on the None Selected link under Users
      1. Add one or more users
      1. Click the Select button
   1. Click on the None Selected link under Select a role
      1. Select the role (DTP Admins or DTP Users)
      1. Click on the Select button
1. Click the Assign button
1. Repeat the process for additional roles. Assignment of User roles and Admin roles are two separate steps.

### ***Enterprise Applications - Function App***
#### ***Function App – Properties***
1. Open Azure Active Directory
1. On the menu, under Manage, select Enterprise Applications
1. Locate and open your function application
1. On the menu under Manage, select Properties
   1. Assignment Required: “Yes”
   1. Click Save

*Note: The steps will be replaced by assigning users to security groups, but the current development environment is limiting user assignment to the following steps.*
#### ***Function App – Assign Permissions***
1. Open Azure Active Directory
1. On the menu, under Manage, select Enterprise Applications
1. Locate and open your function application
1. On the menu under Manage, select Users and Groups
1. Remove any users with “Default Access” by selecting those users and then clicking on Remove

   ![DTP Tech Service Guide Image](/.attachments/DTP2.005.png)

1. Select Add User/Group
   1. Click on the None Selected link under Users
      1. Add one or more users
      1. Click the Select button
   1. Click on the None Selected link under Select a role
      1. Select the role (DTP Admins or DTP Users)
      1. Click on the Select button
1. Click the Assign button
1. Repeat the process for additional roles. Assignment of User roles and Admin roles are two separate steps.


# **Manual Deployment – Tier 0 (Global Admin)**
## ***Function App***
### ***Function App - Authentication***
1. Open the Resource Group you’ve been working in
1. Select the Function App within that resource group
1. On the menu, under Settings, select Authentication

   ![DTP Tech Service Guide Image](/.attachments/DTP2.006.png)

1. Select the Add Identity Provider button
   1. Identity Provider: “Microsoft”
   1. App Registration
      1. App Registration Type: “Pick an existing app registration in this directory”
      1. Name or App ID: Select the API previously created, for example “DataTransfer API”
      1. Issuer URL: this will be filled in and it may take a few moments to take effect.
         1. Note: In RX/EX, this value cannot begin with <https://sts.windows.net>
   1. App Service Authentication Settings
      1. Restrict Access: “Require Authentication”
      1. Unauthenticated Requests: “HTTP 401 Unauthorized: recommended for APIs”
      1. Token Store: Checked/true
   1. No additional steps required, select the Add button

### ***Function App - Configuration***
1. Open the Resource Group you’ve been working in
1. Select the Function App within that resource group
1. On the menu, under Settings, select Configuration
1. Open the FunctionAppConfigurationTemplate2of2.json template file in a code editor
1. Update the values according to the guidance in the template file
1. Delete the commented lines
1. Go back to Azure, in the Function App blade we should already have open
1. On the Configuration page, select Advanced Edit
1. Paste the text you copied from the JSON file
1. Click the OK button
1. Click Save
1. The following application settings should have been added:
   1. clientID
   1. clientSecret
   1. sentinelTimer

### ***Role Assignment***
1. Open your Resource Group and select Access Control (IAM) in the menu
   1) Select the Role Assignments tab

      ![DTP Tech Service Guide Image](/.attachments/DTP2.007.png)

   1. Select Add, then Add Role Assignment
   1. Role
      1. Search for “DTP”
      1. Select “Add, then Add Role Assignment”
   1. Members
      1. Assign Access To: “User, Group, or Principal”
      1. Members: Select the “+ Select Members” link
         1. Select the appropriate personnel
         1. Click the Select button
         1. Click the Review + Assign button
1) Contributor (Allows you to modify access and modify resources, allows you to deploy)
   1. Role
      1. Select “Contributor”, then click the Next button
   1. Members
      1. Assign Access To: “User, Group, or Principal”
      1. Members: Select the “+ Select Members” link
         1. Select your API, for example “DataTransfer API”, then click the Select button
         1. Click the Review + Assign button, then again one more time
1) User Access Administrator (Allows you to modify permissions)
   1. Role
      1. Select " User Access Administrator ", then click the Next button
   1. Members
      1. Assign Access To: “User, Group, or Principal”
      1. Members: Select the “+ Select Members” link
         1. Select your API, for example “DataTransfer API”, then click the Select button
         1. Click the Review + Assign button, then again one more time
### ***Client App Registration – Add Role to access Storage Account*** 
1. Open App Registrations 
1. Select your client application 
1. On the menu under Manage, select API Permissions 
1. Add a permission 
      1. Select Azure Storage 
      1. Select Delegated Permissions 
      1. Select the checkbox for “user_impersonation” 


      ![DTP Tech Service Guide Image](/.attachments/DTP_Image_a.png)

      4. Click Add Permissions 
5. Grant Admin consent 
      1. Select “Grant Admin consent for…”  





# **Manual Deployment – Tier 2 (Helpdesk/Support)**

### **Deploy Functions**
Note: The following effort is partially performed as part of the client deployment in an effort to deploy all project files, but it does not deploy the physical API files as these specific instructions do.

1. Open Visual Studio Code with the latest code
1. In the Terminal, change the directory to ..\dtp\API\dtpapi
1. Type “dotnet build” and execute. Expect 0 errors.

   ![DTP Tech Service Guide Image](/.attachments/DTP2.008.png)

1. Next, type “dotnet publish” and execute. This normally takes 30-60 seconds. Ending at the command prompt signifies it completed.

   ![DTP Tech Service Guide Image](/.attachments/DTP2.009.png)

1. Change directory to the folder ending in “\publish\”.

   ![DTP Tech Service Guide Image](/.attachments/DTP2.010.png)

1. Create a folder where we can store the deployment files. For example, “C:\Users\Sam\DeployDTP” or “C:\Deploy”
1. Type and run the following. Be sure to use the folder structure you just created and name the zip file.
   1. “Compress-Archive -Path \* -DestinationPath \<Deployment Path>\<Zip File Name>.zip”
   1. The image below shows the results of using a deployment path that does not exist, followed by running the correct path.

      ![DTP Tech Service Guide Image](/.attachments/DTP2.011.png)

1. Verify you have a zip file in the deployment folder you specified
1. Open Microsoft Azure Storage Explorer
   1. If you already have a connection established to your subscription, skip this group of steps
   1. Open the Account Management Page
   1. Click on Add an Account

       ![DTP Tech Service Guide Image](/.attachments/DTP2.012.png)

1. Click on Subscription

   ![DTP Tech Service Guide Image](/.attachments/DTP2.013.png)

1. Select the appropriate Azure Environment and click the next button
1. When prompted, select your Azure account

   ![DTP Tech Service Guide Image](/.attachments/DTP3.002.png)

1. You can close the browser/tab expressing you have been authenticated.
1. Back in Azure Storage Explorer, you should see your account has been connected
1. Open the Explorer page

   ![DTP Tech Service Guide Image](/.attachments/DTP3.003.png)

1. Expand your subscription, denoted with the key icon
1. Expand Storage Accounts. This will list the storage accounts within your subscription. Note the two which have not been redacted – these are the two which were created earlier.
1. Expand your non-audit storage account
1. Expand Blob Containers, it should look something like the image below

   ![DTP Tech Service Guide Image](/.attachments/DTP3.004.png)


1. Let’s add a folder to import our deployment files. Right-click on Blob Containers and select “Create Blob Container”
1. Enter a lowercase name, for example “import”. Blob container names can only contain lowercase letters, numbers, and hyphens. They also must begin with a letter or number and cannot contain two consecutive hyphens.
1. Select the container you just created and then click the dropdown arrow next to Upload. Select Upload Files…

   ![DTP Tech Service Guide Image](/.attachments/DTP3.005.png)

1. Select the ellipsis at the right of Selected Files, then navigate and select the deployment zip file to be uploaded. Select the Upload button.

   ![DTP Tech Service Guide Image](/.attachments/DTP3.006.png)


1. After the upload completes, right-click on the line containing your file and select “Get Shared-Access Signature”. Then click the Create button.

   ![DTP Tech Service Guide Image](/.attachments/DTP3.007.png)

   ![DTP Tech Service Guide Image](/.attachments/DTP3.008.png)

1. Copy the URL. You’ll be pasting this into the VM shortly.

   ![DTP Tech Service Guide Image](/.attachments/DTP3.009.png)

1. The following commands need to be run from within the virtual network hosting private endpoints for DTP. You will need to use a virtual machine to access via a bastion host or through a connected network. From within the virtual network, perform the following steps
   1. Log into your VM using Bastion
      1. Verify Edge is your default browser, if it is not, make that change.
   1. Select the URL you copied earlier and open the Edge browser. Paste the Storage URL into the browser so that it starts to download our zip file
   1. Install required software, as described on the project Wiki, under Prerequisites ([04a Prerequisites · microsoft/dtp Wiki (github.com)](https://github.com/microsoft/dtp/wiki/04a-Prerequisites))
   1. Open PowerShell
      1. Change to the folder where you put the zip file
      1. Connect to Azure: “Connect-AzAccount -EnvironmentName AzureUSGovernment”
         1. Note: This is the reason for defaulting the browser to Edge – IE may not allow authentication.
      1. Once authenticated, PowerShell will report some information pertaining to your account and subscription
      1. Get your function app with the following command. Change out:
         1. “$funcApp = Get-AzWebApp -Name \<Function API Name> -ResourceGroupName \<Resource Group>”. See the example below.

            ![DTP Tech Service Guide Image](/.attachments/DTP3.010.png)

1. Type “$funcApp” and execute to see what funcApp contains and to verify success.
1. Next, type the following after changing your file path: “Publish-AzWebApp -WebApp $funcApp -ArchivePath \<Zip File Path>”
1. This may take some time, 30-60 seconds, possibly longer. 
   1. When successfully complete, you’ll get close to 50 rows of data returned, followed by the command prompt
   1. Errors are returned in red text and reasonably obvious. When this happens, check the logs in Azure using the following steps
1. Open the Function App in your Resource Group
1. Select Deployment Center from the menu under Deployment
1. Select the Logs tab and if you had errors, a slightly-more detailed message will appear here. If successful, it should resemble the following image

   ![DTP Tech Service Guide Image](/.attachments/DTP3.011.png)

### ***Deploy Client App***
1. Open the latest code in a code editor. These instructions are described for using Visual Studio Code
1. Be sure you’re using the latest code base
1. Update the two .env files in the following locations.
   1. DTP: ..\dtp\Sites\packages\dtp\.env 
   1. DPP: ..\dtp\Sites\packages\dpp\.env 
   ![DTP Tech Service Guide Image](/.attachments/DTP3.012.png)

   ![DTP Tech Service Guide Image](/.attachments/DTP3.013.png)

   ![DTP Tech Service Guide Image](/.attachments/DTP3.014.png)

   ![DTP Tech Service Guide Image](/.attachments/DTP3.015.png)

   ![DTP Tech Service Guide Image](/.attachments/DTP4.002.png)

   ![DTP Tech Service Guide Image](/.attachments/DTP4.003.png)

1. In VS Code, open the Sites\developerSetup.md file. A helpful suggestion is to view the developerSetup file in Preview.
   1. Work through the “Building the DTP distributables” sectionNavigate to the /dtp/sites folder
   1. Run “npm run hydrateNodeModules” which will either update existing files or will install them if they are missing.
      1. Verify by looking in the /dtp/sites/node\_modules/@dts/common – it should be populated with data similar to the following image.

      ![DTP Tech Service Guide Image](/.attachments/DTP4.004.png)

1. The following steps are specifically for prepping for deployment to an on-premises site with no internet available
   1. Change directory to the \dtp\api\dtpapi folder
   1. Run “dotnet publish”
   1. Change directory to the publish folder, \dtp\api\dtpapi\bin\debug\net6.0\publish folder
   1. Run the following “Compress-Archive -Path \* -DestinationPath <YourDeploymentFolder>” – taking care to replace <…> with your deployment folder, including zip file
      1. If the file already exists, then you can delete it to prevent errors or you can add the -Update command.

         ![DTP Tech Service Guide Image](/.attachments/DTP4.005.png)

1. Change directory to \dtp\api\dpp and we’ll do the same for the DPP project
1. Run “dotnet publish”
1. Change directory to the publish folder, \dtp\API\DPP\bin\debug\net6.0\publish folder
1. Run the following “Compress-Archive -Path \* -DestinationPath <YourDeploymentFolder>” – taking care to replace <…> with your deployment folder, including zip file

      ![DTP Tech Service Guide Image](/.attachments/DTP4.006.png)

1. Open Windows Explorer to the root folder of your project, \dtp
1. Zip the following folders and files
   1. Folders
      1. Build
      1. Deploy
      1. Docs
      1. Sites
      1. wiki
   1. Files
      1. .env
      1. .gitignore
      1. .gitmodules
      1. CODEOWNERS
      1. README.md
      1. SECURITY.md
      1. DeployDTP.zip
      1. DeployDPP.zip
      1. DeployClient.zip

         ![DTP Tech Service Guide Image](/.attachments/DTP4.007.png)


1. Paste the zip into your deployment folder
1. Name this zip “DeployDTS.zip”
1. Zip the contents of your deployment folder. If deploying on-premises, be sure to include all deployment, config, and executable files necessary.
1. Upload this zip file containing DTP, DPP, and Client zips into Azure Storage Explorer. Should be called DeployDTS.zip
1. Connect to your VM via Bastion
1. Once connected to your VM, go back to Azure Storage Explorer, and get your Shared Access Signature
1. Back in the VM, paste your Shared Access Signature you copied from the Azure Storage Explorer into a modern browser. This will download the zip file you uploaded to Azure Storage Explorer
1. Go to the downloaded zip file and extract the contents
1. Extract the Client zip file – this may take a while, so continue with the steps below
1. Open PowerShell as Admin
1. Run “npm –version” to check the version of NPM
   1. Install it if missing, it can be found at <https://nodejs.org/en/download/>
   1. This typically requires a reboot when complete
1. Once NPM is installed and you’ve checked the version, change the root Sites folder
1. Run “npm run build:dtp”, and expect output that resembles the following image

      ![DTP Tech Service Guide Image](/.attachments/DTP4.008.png)

1. If the build was successful, change directory to \sites\packages\dtp\build
1. Run “Compress-Archive -Path \* -DestinationPath \<Deployment Path and zip file>”, should be like “\DeployDTS\DeployDTS\_Client\DeployDTP.zip
1. Run “Connect-AzAccount -EnvironmentName AzureUSGovernment” and perform authentication to connect to Azure
1. After getting connected, run “Get-AzWebApp -Name \<ClientWebAppName> -ResourceGroupName \<ResourceGroup>”, replacing <..> with the proper value
1. If you get data back instead of an error or just a command prompt, then bring that command up again (up-arrow) and this time, prefix it with “$dtpClient = “. This will put the information into a variable which we’re about to use. Be sure to hit enter to run this command.
1. Next, run “Publish-AzWebApp -WebApp $dtpClient -ArchivePath .\DeployDTP.zip”
1. 15:06 – Jason Grants Admin Consent in API Enterprise Application in Permissions page
   1. Open Enterprise Applications
   1. Select your DataTransfer Application
   1. Under the Security section of the menu, select Permissions
   1. Select the Grant Admin Consent for … button
   1. Back to Enterprise Applications, select your API application
   1. Under the Security section of the menu, select Permissions
   1. Select the Grant Admin Consent for … button


# **Tier 0 – Final Cleanup**
### **App Registrations – Admin Consent**
#### ***Client Application***
1. Open App Registrations
   1. Select your client application
   1. Select API Permissions from the Manage section of the menu
   1. Verify you have two permissions and that they are granted admin consent. If they do not exist, select “Grant Admin Consent for…”, similar to the following image

      ![DTP Tech Service Guide Image](/.attachments/DTP4.009.png)

#### ***API Application***
1. Open App Registrations
   1. Select your API application
   1. Select API Permissions from the Manage section of the menu
   1. Verify you have two permissions and that they are granted admin consent. If they do not exist, select “Grant Admin Consent for…”

### ***Key Vault – Remove IP Addresses***
1. Open the resource group you’re working in
1. Open the Key Vault
1. In the menu under Settings, select Networking
1. Remove any IP Addresses that exist. Be sure to save.

### **Storage Account Cleanup**
1. Open the Storage Account
   1. On the menu, under Security + Networking, select Networking
      1. Under the Firewalls and Virtual Networks tab
         1. Public Network Access: “Disabled”
         1. Save
   1. On the menu under Monitoring, select Diagnostic Settings
      1. Click on Blob
      1. Click on the + Add Diagnostic Settings link
      1. Diagnostic Setting Name: Prefix with “diag-“, for example “diag-st-dtp-dev-lt-001”
      1. Select
         1. StorageRead
         1. StorageWrite
         1. StorageDelete
         1. Transaction
         1. Archive to a Storage Account
            1. Subscription: Select the appropriate subscription
            1. Storage Account: Select the Audit Storage Account in your resource group
      1. Save

### **Function App**
1. Configuration Settings
   1. Add the following
      1. AzureWebJobsSecretStorageType
         1. Name: “AzureWebJobsSecretStorageType”
         1. Value: “Blob”
         1. Click the OK button, then Save
      1. SqlConnectionString
         1. The default password is set as “Password={your\_password};”. Update this value with the appropriate password.
         1. Click the OK button, then Save

            ![DTP Tech Service Guide Image](/.attachments/DTP4.010.png)

### **Audit Storage Account Cleanup**
1. Open the Audit Storage Account
1. On the menu under Security + Networking, select Networking
1. Under the Firewalls and Virtual Networks tab
   1. Public Network Access: “Disabled”
   1. Save

# **Deployment Validation**
After performing all the steps to deploy the application, follow these instructions to validate the deployment.

1. Log into your VM, connecting through Bastion
1. Open a browser and enter the URL for your client application (App Service)
1. Open developer tools (F12) and select the Network tab
   1. In the app, select Transfer Request History
   1. You should see calls in the developer tools area to GetUserTransferRequests with a status of 200
1. Go back to the home page of the app and select Online Transfer Request
   1. Fill in a unique container name, the other fields are optional
   1. Click the Create Transfer button
   1. If successful, you should see something like the first record, it’s showing as “In Progress”. When it finishes, it should look like the next record
   1. This process can take a while, 2-3 minutes to appear and 10 minutes to complete

      ![DTP Tech Service Guide Image](/.attachments/DTP5.002.png)

1. Verify Admin vs non-admin – define what permissions are required for the “Access Control and Auditing” menu option to display and test with and without it to ensure it displays for admins and not for others. 


      ![DTP Tech Service Guide Image](/.attachments/DTP5.003.png)


# **Troubleshooting**
## ***Azure Storage Explorer***
- If you are unable to download your code from Storage Explorer, verify your VM Virtual Network/Subnet (on Overview of VM) is associated with the VNET in the resource group you’re working in.
## ***Function App***
- [Troubleshoot error: Azure Functions Runtime is unreachable | Microsoft Learn](https://learn.microsoft.com/en-us/azure/azure-functions/functions-recover-storage-account)

### Publishing Related Errors
- After publishing, in the Function Apps blade, select Deployment Center from under the Deployment section of the menu 
- Select the Logs tab

#### ***Error regarding*** API ***SCM missing:***
1. Open the Private DNS Zone named “privatelink.azurewebsites.us” located in your resource group.
1. Add a record set
   1. Name: Add the SCM record for your API, for example “datatransferapi.scm”
   1. Type: “A”
   1. TTL: “10”
   1. TTL Unit: "Seconds”
   1. Address: Use the same address as the Private Endpoint for your function app
   1. Click the OK button
1. Add another record set
   1. Name: Add the non-SCM record for your API, for example “datatransferapi”
   1. Type: “A”
   1. TTL: “10”
   1. TTL Unit: "Seconds”
   1. Address: Use the same address as the Private Endpoint for your function app
   1. Click the OK button

#### ***Timeout Errors***
1. If you experience timeout errors while deploying, verify the configuration settings in your function app application settings. At the very least, be sure they’re pointed to resources within your resource group.
#### ***Application Errors***
1. Function host is not running
   1. When creating an Online Transfer Request and you receive the “Function host is not running” error, try this

      ![DTP Tech Service Guide Image](/.attachments/DTP5.004.png)

1. Open your Function App and select Application Insights from the menu, below Settings. Ensure your connected to the Application Insights residing in your Resource Group.
1. Container name unspecified or already in use

   ![DTP Tech Service Guide Image](/.attachments/DTP5.005.png)

1. One possible cause could be associated with two parameters in the Function App configuration
   1. ClientID: Get this value from the App Registration for the API Function app Overview blade. The value is stored as the Application (Client) ID.

      ![DTP Tech Service Guide Image](/.attachments/DTP5.006.png)

1. clientSecret
   1. In the App Registration for the API Function app, under Certificates & Secrets
      1. If you have an existing secret and know the value, you can use that. Otherwise, you will need to create a new secret
      1. The name isn’t important. Immediately after creating secret, save that secret into this parameter, and consider saving in Key Vault.
      1. It’s important to understand that the clientID and clientSecret parameters are a unit, they go hand-in-hand and must be in sync with one another.

         ![DTP Tech Service Guide Image](/.attachments/DTP5.007.png)

3. Blank website after deployment 
      1. Verify your “.env” environment file includes the period preceding “env”. If not, rename the file, rebuild, then redeploy. This file is found in the sites\packages\dtp and sites\packages\dpp folders. If these files are named correctly, continue. 
      1. Use the browser developer tools (F12), be sure to refresh 
      1. Open the Network tab 
         1. If you do not see a status of 200 for your URL as well as a JavaScript file with the same status of 200 then continue 
         1. This could be the wrong files have been deployed. To verify, add “.SCM” in the URL between your site name and “azurewebsites.us”.
             1. Example: “https://datatransfer” + “.scm.” + “azurewebsites.us” 
         1. From the Debug Console menu, select PowerShell 
         1. Select the Site folder 
         1. Select the wwwroot folder 
         1. Expect to see something like the following image. If you have something altogether different, then you will need to verify what files you were deploying and try again. You should be deploying files in the Sites\packages\dtp\build folder. 

         ![DTP Tech Service Guide Image](/.attachments/DTP_Image_b.png)
 
4. Transfer Request Error

     ![DTP Tech Service Guide Image](/.attachments/DTP_Image_c.png)

   1. Use the browser developer tools (F12) 
      1. Network: You should see calls to GetUserTransferRequests.
      1. This confirms that this method in the API app is functioning properly. 
         1. Console: Check here for errors. Ignore the following: 
         1. 404 in reference to the manifest file 
         1. 409 

# **Acronyms and Abbreviations**

|**Acronym/Abbreviation**|**Meaning**|
| :- | :- |
|**AAD**|Azure Active Directory|
|**DTP**|Data Transfer Portal|
|**RG**|Azure Resource Group|
|**TTP**|Tactics, Techniques, and Procedures|

# **Glossary**

|**Term**|**Meaning**|
| :- | :- |
|||

