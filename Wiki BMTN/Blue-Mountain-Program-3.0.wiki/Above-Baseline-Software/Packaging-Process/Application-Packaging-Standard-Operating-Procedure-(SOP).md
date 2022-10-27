# Standard Operating Procedure (SOP) for Application Packaging Above Baseline Software

[[_TOC_]]

# Overview
[Visuals](/Above-Baseline-Software/ABS-Lifecycle-Model)
## Principles
- Design for offline an secure application use
- Reuse for different environments: Gates hyperscale & Gates edge (ASH)
- Push deployment and installation scripts to ABS repo
- Repeatable engineering, testing, and deployment model
- Engage with customer SME as much as possible
- Automated deployment and maintenance of apps
- Reduce need for operations staff engagement
- App publishing framework must enable customers to self-serve as early as possible
- Utilize Git LFS. Future state: Common installer and dependency storage account


## User Onboarding

* **Permission Access:**
  * ADO Onboarding
  * MacGyver Team Chat Access
  * Internal 3PS Team Access
  * Account Setup:
    * Gates Edge: T0 Account, MSCPEC
    * Hyperscale: <alias>-mpa@phc-msft.us
    * Leverage ADO to capture App development status: [ADO ABS Dashboard](https://dev.azure.com/BlueMountainAGMP/Blue%20Mountain%20Program%203.0/_dashboards/dashboard/21b2303c-90d8-4c2c-b82c-79e6916cdcdc)

* **Meetings:**

  * Daily Scrum Call 
@ 11 AM EST (M,W,TH,F) AND @ 10:30 AM EST (T)

  * MacGyver Weekly Touchpoint
@ 1 PM EST (TH)

## Naming Conventions
Generally lowercase is preferred for the names.
1. App VMs: vm-<customer org>-<app name>
 Ex: vm-delta-stk, vm-delta-stk-license
1. Hostnames: <app name>-<purpose>
 Ex: stk-license, jira-sql
 Since hostnames are character limited and the computer object should be in the enclave OU I assume we don't need to include the enclave/organization name (e.g. delta) in the hostname to save some characters
1. App URL: <app name>.<enclave name>.domain
 Ex: "jira.delta.phc-msft.us"
1. AD naming conventions
Link to Powerpoint file with guidance: [Naming Guidance](https://microsoft.sharepoint.com/:p:/t/BlueMountain/EcVVgjzm9JBKufA2_obhZb8BM7JoIBJPVRbYH7AGe3STvg?e=EBomTd)

# Application Development Process

## Application Assignments
Application will be assigned by Lily Gomez.

## ABS Development Setup
1. VS Code is recommended with the following extensions: `Azure CLI Tools`, `PowerShell`, and `Bicep` all published by MSFT. Git also needs to be installed to push updates to ADO. To create Architecture Diagrams, install the "PlantUML" VS Code extension and review this overview [video](https://microsoft.sharepoint.com/:v:/t/BlueMountain/EfZuDWFRvUdIsLLpkPmnpOsBFpLCC9f14Dro8MwJpgEhjQ?e=fRKCCo)
1. Download and install: Git - [Downloads](https://git-scm.com) then install LFS with `git lfs install`. 
a. "git config http.version HTTP/1.1" may be needed for large files (git issue)
b. "git config --global core.longpaths true" may help when cloning the repo
1. In [ADO](https://dev.azure.com/BlueMountainAGMP/Blue%20Mountain%20Program%203.0/_git/ABS), create a branch in the ABS repo using the naming convention of
`<appname>-<prod environment>` like `MatLab-EX`
1. If your new branch is in sync with main, copy the `appTemplateFolder` from your branch back into your branch but rename the folder to your app name (e.g. Jira). Please ask the ABS Team if there is a more current, stable version of the `infra` folder in another branch.
1. In ADO, create a Pull Request (PR) to merge your branch into `main`. This PR will track changes pushed to your branch but will only be approved after the app CR is approved.
1. The folder structure for your app should look like the following. The license server folder (and children folders) can be removed or replaced depending on the app installation needs. `licenseServer` could be deleted for an app that only has a client or it could be replaced by another app dependency (e.g. java installer).
&nbsp;&nbsp;<appname>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;code
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;infra
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;app-<appName>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AppDeployToolkit
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Files
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;licenseServer
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AppDeployToolkit
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Files
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;docs
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Markdown
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Assets
1. Find the parameters files for your customer enclave (delta, wallaby, etc) from `<appname>\code\infra\parameters\` (e.g. template.parameters.fairfax.hamster.json). Make a copy into the `<appname>\code\` folder and rename the prefix "template" to the app name. This allows the `infra` folder to be replaced when an update is available within our team.
1. Find the appropriate installer for your app and download it to your branch under `Files` (e.g. `<appname>\code\<app> Client\Files\`. 

## Application Development Process
Developer POC for the app will own the app package development including Research, Development, and Testing/Validation. If any requirements information is missing or there are questions for the customer, please reach out to Andy Serfass or Lily Gomez.
1. Research the app requirements and complete the ConOps using the [template](/Above-Baseline-Software/ConOps-Template). When the CONOPs has been completed schedule time with Junaid Paracha for the final review
**Validate if a license is required ASAP.** If a license is required for the application, please inform Tiara Hilliard to initiate the procurement. The process could be lengthy so please ensure this step is verified as soon as possible to not cause delays in production deployment.
_Open source applications_ do not require a license but require a separate security review so they should be noted in the field at the top of the ConOps for Open Source.
1. Deploy the resources needed by the application according to the research performed to complete the ConOps. Deploy these resources in the dev environment (currently Fairfax) using the latest [infra](/Above-Baseline-Software/How-to-Bicep-+-DSC) folder of bicep deployment scripts. Fill out the app specific parameter file according to the app needs.
   - If some initial testing is needed, the app can be deployed locally like on a laptop HyperV environment but this will not have the same security controls so the value of this approach has limits. Use this method only if you understand the limitations.
1. Install the application using PowerShell (PS) App Deployment Toolkit (ADT) described [here](/Above-Baseline-Software/Packaging-Process/App-Packaging-with-PS-ADT). Iterate installation until the application installs successfully. **While installing the application** fill out the Install Guide for this app using the [template](/Above-Baseline-Software/Install-Guide-Template).
1. Install [McAfee](/Above-Baseline-Software/TSGs/TSG-%2D-McAfee-Agent-Install) and [Nessus](/Above-Baseline-Software/TSGs/TSG-%2D-Nessus) Agents using these linked TSGs as guides until the agents can be installed automatically.
1. Once the application installs successfully, it is recommended to redeploy the VMs needed and reinstall the application.
1. Create a Test Plan using this [guidance](/Above-Baseline-Software/Packaging-Process/Test-Plan-Process). The test plan needs to be executed and `pass` **before** the app CR is approved.
1. Move AD Computer object to `Services OU so the OS (e.g. Windows) can be hardened with GPO [STIGs](https://public.cyber.mil/stigs/) as the VM would be in production. This may break the app (e.g. prevent app services from starting/running) until a fix is developed (e.g. giving the service account permissions to logon via RDS/Terminal Services).
1. Run Windows Update on the app VM(s).
1. Request Nessus and Defender for Cloud Scans in Fairfax from Andy Serfass or Charles "Chuck" Hedrick. The VMs must be in the Services OU and must have current Windows updates. Save these scans in your app branch in the ABS repo.
1. Request a Change Request (CR) from Junaid Paracha using the information in the CR table within the ConOps as inputs. The CR is the process to receive approval to deploy into the production environment.
1. Publish app as a Remote App via AVD using this [guide](/Above-Baseline-Software/Packaging-Process/Publish-Remote-App).
1. Once CR is approved, complete Pull Request (PR), create a zip of the app folder from the 'main' branch (after PR merge).
1. Upload the file to the DTA/AFT portal or use the temporary transfer method. Ask during the ABS Team stand up meeting if you have questions. Ensure the files for transfer include the app package (app folder zip from the 'main' branch), the app license file if procured, and any dependency files since it is time consuming to move files.
1. Notify Mission Initiatives Operations that the DTA/AFT was initiated.

------------------------
# Submit Change Request 
   - Dev to submit Pull Request
   - Create Change Request: [here](https://microsoft.sharepoint.com/teams/BlueMtnCollaboration-EXTERNAL/Lists/Change%20Request/AllItems.aspx?xsdata=MDV8MDF8fDIzYTNhZjk5MjM4OTQ5ZGUwZTlkMDhkYTNkYmFhOTllfDcyZjk4OGJmODZmMTQxYWY5MWFiMmQ3Y2QwMTFkYjQ3fDB8MHw2Mzc4OTAxNjkxNDUyNjU4MjF8R29vZHxWR1ZoYlhOVFpXTjFjbWwwZVZObGNuWnBZMlY4ZXlKV0lqb2lNQzR3TGpBd01EQWlMQ0pRSWpvaVYybHVNeklpTENKQlRpSTZJazkwYUdWeUlpd2lWMVFpT2pFeGZRPT18MXxNVGs2YldWbGRHbHVaMTlOVkZKcldsUkpNVnBxVFhST1JGRXlXWGt3TUU1WFRUTk1WR3N4VFRKTmRFNUhSbWxhYW1Nd1drUmpNRTlFU21wQWRHaHlaV0ZrTG5ZeXx8&sdata=QVFSZlh3RWhKSEdBdmVoWUpoT2h2OHdhUHNFaUhEeW9WejIrUFlaQUoycz0%3D&ovuser=72f988bf%2D86f1%2D41af%2D91ab%2D2d7cd011db47%2Clihernan%40microsoft%2Ecom&OR=Teams%2DHL&CT=1653420187264&clickparams=eyJBcHBOYW1lIjoiVGVhbXMtRGVza3RvcCIsIkFwcFZlcnNpb24iOiIyNy8yMjA1MTYwMDQwMCIsIkhhc0ZlZGVyYXRlZFVzZXIiOmZhbHNlfQ%3D%3D)
   - Send email to security team in email format
     -  _Include links to CONOPs, Installation Guide, Test plans and Scans of Fir env. for Prod approval_
   - For more information : 
[How to Submit and Track a Change Request](https://nam06.safelinks.protection.outlook.com/ap/w-59584e83/?url=https%3A%2F%2Fmicrosoft.sharepoint.com%2F%3Aw%3A%2Ft%2FBlueMountain%2FEZeqUqEL4blFpN94kiErEAIB9rpOXzb-ybd0oiktsWabOw%3Fe%3DHGcCvE&data=04%7C01%7Caserfass%40microsoft.com%7Cfa3338f9a9294465283a08da118df80a%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637841598737987269%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000&sdata=u%2BXYyHxMj7dIWdyfqlCAkWdDa%2FnknynCW1JGEk2ot00%3D&reserved=0)
[BlueMtn-MVP Change Management Plan](https://nam06.safelinks.protection.outlook.com/ap/w-59584e83/?url=https%3A%2F%2Fmicrosoft.sharepoint.com%2F%3Aw%3A%2Ft%2FBlueMountain%2FEfuieVz6LodEjwB9F5rwKMQBmHNTDMgVmuEjHZdxicne2A%3Fe%3DdxVVR7&data=04%7C01%7Caserfass%40microsoft.com%7Cfa3338f9a9294465283a08da118df80a%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637841598737937279%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000&sdata=HX8S%2BiYaoaNxCoKHaAb2ahxCmnweuLlRCF7z0qei2ZQ%3D&reserved=0)

# Troubleshooting
## H Drive
Start command prompt and run this command
`net use h: \\127.0.0.1\c$\users\%username%`

To remove the workaround
start command prompt and run this command
`net use h: /d`
NOTE : Use the CMD window not Power Shell (it is the old black console)
If your app has a hard time with this workaround, you can try editing the registry key but this has risks:
Go to Start > Search > and then type Regedit.
Navigate to the following folder: HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders
Change value to this with your <username> inserted in here: \\127.0.0.1\c$\users\<username>\Documents