[[_TOC_]]

#Background
Azure Virtual Enclaves service is envisioned as an enterprise Software as a Service (SaaS) offering that enables mission owners to rapidly deploy secure IT systems, applications, and services up to the DoD TS/SCI/SAR classification level. Its architecture and implementation should meet and exceed the security capabilities of the DoD FENCES capability, a mature solution that has been vetted and authorized by the Department of Defense (DoD) and Intelligence Community (IC) for its robust ability to isolate Special Access Program (SAP) customers and workloads from the broader community that the Commercial Cloud Services (C2S) platform serves. However, Azure Virtual Enclaves is also designed for the Defense Industrial Base at Impact Level 6, as well as for broader commercial applicability for highly regulated industries such as financial services and healthcare. 

The purpose of this document is to describe the design and architecture of Mission Application Platform – the subsystem of Azure Virtual Enclaves service managing application deployment and publishing in customer enclaves.

Azure Virtual Enclaves
Azure Virtual Enclaves is a combination of cloud-hosted and tenant-agnostic management and connectivity services, tenant-specific Platform as a Service offerings that support both tenant workloads and tenant-accessible SaaS offerings: 
-	Virtual Enclaves Management Environment / Control Plane: The Virtual Enclaves control plane is hosted and managed by Microsoft as a service provider and accessible via standard customer facing APIs.
o	Secure Connectivity: Combination of SCCA-VDSS capability plus client network connectivity services and user endpoints.
o	Common IT and Cyber Services: IT and cyber services available to all customers. No customer data stored. 
-	Virtual Enclaves Tenant Core / Data Plane:
o	Core Services: tenant-specific IT and cyber services in a PaaS pattern. Core Services are leveraged by AVD, Collab services, and tenant Mission Workloads.  Management is shared between Microsoft and the tenant.
o	Virtual Desktop and Collaboration suite: tenant-specific PL-3 (ABAC) virtual desktop with PL-3 collaboration services (Exchange, SharePoint, Skype, & NFS).  Management is shared between Microsoft and the tenant.
-	Virtual Enclaves Tenant Workloads / Mission Plane: tenant-specific subscriptions that are securely connected the tenant’s Virtual Enclaves Core PaaS. Workloads are stamped with IaC and inherently are cybersecurity compliant and are pre-configured with IaM policies. Data can only ingress and egress by pre-defined secure network configurations.

![VEArch.png](/.attachments/VEArch-9d290d51-8897-42e9-b37a-bdcbf39cc44c.png)



# Mission Application Platform
Mission Application Platform is a subsystem of Azure Virtual Enclaves service managing application deployment and publishing in customer enclaves. It allows customers to simply and securely publish mission apps within the Azure Virtual Enclaves framework.
It offers managed app deployment and publishing service to publish, secure, and monitor mission software within customer enclaves.
Mission Application Platform is to be comprise of the following major components: 
-	ARM Resource Provider for application deployment, publishing and cyber oversight
-	Software Catalog to maintain application metadata, references to applications artifacts including source code / scripts, virtual machine images, containers images.
-	A set of Reference factories to publish well-known applications leveraged by mission customers

# Application Hosting Model 
Mission Application Platform is designed to support a wide range of applications. Given the diverse spectrum of application architecture options, the following application classification taxonomy has been introduced: 

## Applications by Origin
-	Commercial off-the-shelf (COTS) Applications: can be comprised of the following publishing artifacts: 
o	Installation binaries
o	Virtual Machine images
o	Container images
-	Government off-the-shelf (GOTS) Applications:
o	Installation binaries
o	Virtual Machine images
o	Container images
-	Custom applications developed by customer in-house:
o	Source code organized as filesystem folder structure 
o	Installation binaries
o	Container images

## Applications by Archetype 
The following application archetypes have been identified for deployment / publishing support within customer mission enclaves:
-	Desktop applications: deployed on session host virtual machines exposed to end users via RemoteApp session
-	Web applications deployed on Virtual Machine(s) in customer mission enclaves with front end web browser exposed to end users via RemoteApp session
-	Multi-tier applications deployed on Virtual Machine(s) in customer mission enclaves with exposed to end users via Desktop Application or Web Browser running on session host published via RemoteApp session


# Mission Application Platform Software Catalog

Mission Application Platform Software Catalog is designed to maintain application metadata, references to applications artifacts including source code / scripts, virtual machine images, container images and it consists of the major components as described below.

## Catalog as Metadata Repository 
Metadata Repository in Software Catalog is exposed to Mission Enclave Administrators and other authorized roles and represents a list of onboarded applications accessible for deployment in a specific customer mission enclave. It contains applications metadata including properties applicable to deployment process along with pointers to applications artifacts like source code / scripts, virtual machine images and container images
Application Artifacts Hosting
Depending on the application origin and archetype, application deployment package can be comprised of artifacts as listed below

### Code / Scrips Artifacts 
-	The combination of source code and script files (bicep, PowerShell, CLI, shell scripts) compatible with IoC automatic deployment on target virtual machines by publishing service provider
### Virtual Machine Images 
-	The Virtual Machine image supplied by COTS/GOTS entity via Azure Marketplace or out-of-band publishing mechanism for customer deployment in mission enclave
### Container Images 
-	The container image supplied by COTS/GOTS entity or enclave customer for deployment in mission enclave using docker containers or leveraging Azure container PaaS offerings like Azure Kubernetes Service (AKS)


## Installer Binaries Out-of-Band Procurement Considerations
Due to licensing and software distribution requirements by 3rd-party product owners, it may be expected that the enclave customer brings over software install binaries for a particular COTS application to be included into the deployment package per Mission Application Platform requirements.

## Catalog and Cloud Environments
Initially, the Software Catalog is expected to be deployed in Azure Government (FX/Fairfax) with application artifacts downloaded and transferred via DTA process to air-gapped clouds for deployment as needed. Eventually, Software Catalog should be deployed in additional cloud environments, including Azure Global (Commercial), and air-gapped clouds (RX/EX). 

# Mission Applications Platform Application Lifecycle
Application Lifecycle in Mission Applications Platform is envisioned to follow the steps per below:
1.	Application onboarding into Mission Applications Platform Software Catalog.
2.	Application assignment for visibility in customer mission enclave after onboarding.
3.	Application deployment into customer mission enclave.
4.	Application publishing for end users after deployment into customer mission enclave.

## Application Onboarding to Mission Applications Platform Software Catalog
The first step for application to be accessible for deployment and publishing to enclave users is its onboarding to Mission Applications Platform Software Catalog. Application package metadata is populated into Software Catalog along with a pointer to either a known Azure Marketplace product or repository containing customer-onboarded COTS/GOTS application in form of preliminary deployment package. The deployment package includes provisioning and post-provisioning configuration scripts along with documentation describing application mission enclave installation manual and automated procedure. The installation media is either supplied in the pack along with the scripts or a link is provided to ISV site download location.

## Application Deployment in Mission Applications Platform
The Application Software Package is prepared for deployment to customer mission enclave as follows:
Initially (when Software Catalog is only deployed in FX environment as noted earlier in the document), if target mission enclave is located in air-gapped cloud (RX/EX), the package is downloaded from Software Catalog and moved to RX/EX via DTA process (or Azure CDS service once available). If application is owned by 3rd-party ISV, the installation media may either be downloaded / transferred via DTA process separately or maintained only within air-gapped environment- in that case the deployment packaging script is executed to create the complete deployment package that includes provisioning, configuration scripts and installation media. 
During the deployment, the deployment packaging script is executed to provision Azure resources in virtual enclave within target deployment environment and apply VM-level product configuration.

### Application Deployment Service Infrastructure
The application deployment process is designed to be supported by service Infrastructure components as identified below

#### Application Package Storage (Air-gapped Clouds)
The Application Package Storage in air-gapped clouds maintains packages transferred via DTA or (Azure CDS) for subsequent deployment process. The content is populated via sync process described further in the document as packages is get moved from the low side.

#### Application Package Sync Service (Air-gapped Clouds)
The Application Package Sync Service in air-gapped cloud environments is an application deployment service Infrastructure component that synchronizes / copies packages received via DTA or (Azure CDS) process with Application Package Storage.

#### Administrator Application Console (Air-gapped Clouds)
The Administrator Application Console in air-gapped cloud environments is the control plane management tool for application deployment / publishing activities. It supports the following functionality:
-	Initiating application deployment process based on application package stored in Application Package Storage repository
-	Initiating publishing process of applications deployed in customer mission enclaves
-	Manage access of client networks to deployed applications 
-	Maintain user-to-application assignments to manage end user access to published applications.
The diagram demonstrating the interactions of service infrastructure components is supplied below

![MAPARch.png](/.attachments/MAPARch-8bb9cc4c-c554-45d1-a381-a98594fe7c47.png)

### Application Package Format Definition 
The format of the Application Package prepared for deployment has been defined per below:
Zip File packaging shell
-	Metadata.json
-	Checksum 
-	RootDir / src
o	Installers (or pointer to installer)
o	Dependencies / License server
-	RootDir / scripts
1.	Deploy infra script
2.	Configure infra (staging->prod) script
3.	Deploy app/s script
4.	Configure app/s

### Phased Deployment Implementation Timeline 
Given the magnitude of the implementation effort, the deployment functionality is slated to be developed in phases as follows:

#### Phase I. Manual Deployment 
During the initial phase, the application deployment is executed manually by authorized role executing bicep / scripts from dedicated jumpbox virtual machine within the target customer enclave.

#### Phase II. Automated Deployment 
In subsequent phase, the Mission Application Platform Deployment Resource Provider initiates the application deployment by executing bicep to provision required Azure resources and runs auxiliary installation configuration scripts supplied with the deployment package. Once deployment process is complete, the application is instantiated in mission enclave.


# Application Publishing in Mission Applications Platform
Once deployed, the application is prepared for publishing where the following actions are required to be completed:
-	Application gets classified
-	The application type is set to desktop, web, client /server to be used by publishing tools
-	The application is marked as allowed to be accessed by specific client networks
-	Access to the application is granted for specific users.
-	The deployed application gets published to target client desktop networks, including optional Azure Virtual Enclaves Desktop
The application publishing status gets set to 'active' to make it available to client networks.:

## Phased Deployment Implementation Timeline 
Given the magnitude of the implementation effort, the publishing functionality is slated to be developed in phases as follows:

### Phase I. Manual Publishing 
During the initial phase, the application publishing is executed manually by authorized role updating application metadata via transitional user interface machine 

### Phase II. Automated Deployment 
In subsequent phase, the Mission Application Platform Publishing Resource Provider initiates the application publishing by updating application metadata per requirements above and runs any auxiliary configuration scripts to allow inbound access from source networks and assigning specific users to consume the deployment application. Once publishing process is complete, the end users are allowed to access the application instantiated in mission enclave.

# Mission Application Platform Final Architecture Summary
The goal of this section is to provide the overall coverage of solution architecture as it evolves during the design process.

# References
-	DoD Secure Cloud Computing Architecture (SCCA) Functional Requirements https://rmf.org/wp-content/uploads/2018/05/SCCA_FRD_v2-9.pdf 
-	Secure Azure Computing Architecture : https://docs.microsoft.com/en-us/azure/azure-government/compliance/secure-azure-computing-architecture 
-	Microsoft Azure Enterprise Promises: https://microsoft.sharepoint.com/teams/ep/SitePages/Introduction.aspx 

