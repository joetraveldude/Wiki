[[_TOC_]]

# Deploy Resources
## Automation Needed
Deploy Workspace (for enclave)
Deploy App Group (for App)
Deploy Host Pool (for App)

## Pre-requisites
Complete the deployment of the app VM (becomes the session host in the host pool). App installation is not required but allows the RemoteApp publishing process to be completed end-to-end with the exception of assigning permissions (which requires a Service Desk ticket)

## RemoteApp Azure Resource Descriptions
Publishing a RemoteApp requires a host pool, application group, and workspace be deployed.

### Session Host/VM
The app Session Host or VM should contain the app that will be published but built-in apps (paint, notepad, etc) can be used for testing.  The Session Host is "registered" with the host pool by installing the RD agent and bootloader (the host pool needs to be deployed before the agents can be installed). The current app publishing model usually has one app per session host (and host pool). This is so 1) multiple apps don't need to share VM resources and can be sized appropriately and 2) app access can be assigned for individual apps. The exception is if two apps need to "talk" to each other and they can't do that from different VMs (i.e. can't communicate over the network).

### Host Pool
Deploy one host pool per app since the Security Group that is given access to the app will have access to all apps published from the Session Host. So the users authorized to access the Host Pool App Group will have access to all apps published from that Session Host.
**Note** the host pool needs to be created before the Session Host can be added to the host pool via registration.
Naming convention: hp-<appName>

## Application Group
Naming convention: hp-<appName>-RAG
## Workspace
Naming convention: ws-<enclaveName> (i.e. delta)
# Configure
## Session Host/VM Registration
[Register](https://docs.microsoft.com/en-us/azure/virtual-desktop/create-host-pools-powershell?WT.mc_id=Portal-Microsoft_Azure_WVD&tabs=azure-powershell#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool) VM by installing agent and bootloader. Use the registration key provided from the Host Pool portal. Wait a few minutes and then refresh the Host Pool page to see the VM count increases to include the new registered VM.
### Install the agents
Retrieve a SAS URL from the ABS storage account currently this is the mpducttape SA. Find the agent and bootloader in the containers for this SA under `abs/tools`. The versions may change but the files should be similar to these:
![image.png](/.attachments/image-390f0b79-63c8-43b4-af29-c2e7c68f92ef.png)

# RemoteApp Publishing Steps
## Assumptions
1. App installation done on the VM ("appVM")

## Steps
### Deploy Resources
Host Pool, App Group, Workspace
1. In the Azure Portal, navigate to the target resource group (the RG with the appVM).
1. Click "Create" new resource
1. In the search box type "host pool" and press enter
1. Select "Host Pool" published by Microsoft, and select "Create" on the next screen
1. Verify the Subscription and RG then add the host pool name in the format hp-<appName>
1. Select "Preferred App Group Type": `Remote App`
1. Select "Host Pool Type": `Pooled` and "Balancing Algorithm": `Breadth`
1. Select "Max Session Limit": equal to the number of users expected for the VM (a buffer can be added)
1. Select "Next" (next tab to VMs), keep default of `No` (we will add our VM in a later step)
1. Select "Next" (next tab to Workspace)
1. Select "Register Desktop App Group": `Yes`
1. In the dropdown for "To This Workspace": find the resource group and nested workspace for the customer enclave. It should be in the format ws-<enclaveName> (preferred) or workspace-<enclaveName>. If no prior workspace was created (no RemoteApps published for this customer yet: Click out of the dropdown menu and select `Create New` and enter a name in the format: ws-<enclaveName>.
1. After a workspace is selected, select "Next" (next tab to Advanced)
1. Click "Enable Diagnostic Settings", and `Send to Log Analytics Workspace`, confirm the subscription, and select the Log Analytics workspace for the enclave (usually listed _under_ the RG with a name in the format "...core_<enclaveName>_iaas". Remember to select the logA resource.
1. Select "Review + Create" and then "Create" after validation is passed.
1. Wait for resource to be created and click "Go to Resource" once complete.

### Register VM as Session Host
1. In the Host Pool resource, click "Registration Key", and click the copy icon in the lower right corner of the new window.
1. Login to the VM with the app being published.
1. Install the RD Agent and RD Agent Bootloader either via the files in the app package zip path "<appName>\code\infra\tools\" or a storage account (like mpducttape in Fairfax). For the RD Agent install, click `yes` to accept terms, click `next`, then paste the Host Pool Registration Key into the box overwriting the "INVALID_TOKEN" default text, click `install`, then `finish` when done. For the RD Agent Bootloader install, click `yes` to accept terms, click `install`, then `finish` when done.
1. Go back to the Host Pool tab, close the Registration Key window and refresh the Host Pool page. You should see the VM `Total Machines` count increase (to 1). If it does not, you may need to restart the VM.

### Add Application Group
Follow the instructions below but [these](https://learn.microsoft.com/en-us/azure/virtual-desktop/manage-app-groups?WT.mc_id=Portal-Microsoft_Azure_WVD) may also be helpful
1. Scroll down and click on "Application Groups", click `add`, verify the defaults, add an app group name in the format hp-<appName>-RAG (RAG="RemoteAppGroup").
1. Click "Next" until the Workspace tab, click `yes` and confirm the default workspace.
1. After a workspace is selected, select "Next" (next tab to Advanced)
1. Click "Enable Diagnostic Settings", and `Send to Log Analytics Workspace`, confirm the subscription, and select the Log Analytics workspace for the enclave (usually listed _under_ the RG with a name in the format "...core_<enclaveName>_iaas". Remember to select the logA resource.
1. Select "Review + Create" and then "Create" after validation is passed.
1. Wait for resource to be created and click "Go to Resource" once complete.
1. Click `Applications`, click `Add`, "Application Source" should be `Start Menu` for most apps, use the "Application" drop down to select the app to be published.
1. Submit an IcM to add app user Security Group ("sg-g-...") to the assignments for the Application Group "Assignments". Ensure the Enclave Test User account is added to the Security Group.
1. Once the IcM is closed, check that the app can be access via the Remote Desktop client or web portal.

## Test App
Anyone with an MPA account should be able to access the remoteApps for testing. Use a private browser or the Remote Desktop app to add this workspace and login with your MPA account
![image.png](/.attachments/image-5127333a-030d-4bab-b81f-944a909f093e.png)
1. Sign into the Remote Desktop client with user creds
1. Refresh feed if a new app was added
1. Double click on the app