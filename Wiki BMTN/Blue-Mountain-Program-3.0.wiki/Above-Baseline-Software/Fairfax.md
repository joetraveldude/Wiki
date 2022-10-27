[[_TOC_]]
# Accounts:
- Requests: Requesting AzEnclaves Mission Enclave Admin Account - [MPA Request Form](https://forms.office.com/r/gqAqMFF7Yi). The source for this form is [here](https://msazure.visualstudio.com/Project%20Honeycomb/_wiki/wikis/Project-Honeycomb.wiki/257018/Requesting-AzEnclaves-Mission-Enclave)
 For completeness this is the form for privileged accounts but this is not recommended for the ABS team: [Form](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwvCsv4rHatEtvLCM_6bcC1UNkQ4Sk1JREJFOFhONkVXU1c5V1dZR0JJVi4u) 
- Data Plane: <first.last>@phc-msft.us
- Mission Plane: <alias>-mpa@phc-msft.us

* If you are requesting an account, you can ask to have your account added to the Bravo "ABS Team" group to get access to the organizations that ABS is supporting. The intent is that the ABS Team has the same permissions.

# Access environment:
1. Open browser from (your corp laptop desktop) and go to https://portal.azure.us
2. For Mission plane access (app installation)
  a. Enter your <alias>-mpa@phc-msft.us creds
3. For data plane access (AD, etc)
  a. Enter your <first.last>@phc-msft.us creds
  b. Go to the subscription with "DEV1" or "DEV2" in the title depending on the tasks
  c. AD access:
&nbsp;&nbsp;i. In "AG_MP_AGES_PHC_DP_DEV1_SDUBEY" subscription go to "dev-dp-identity" resource group
&nbsp;&nbsp;ii. Connect to any DC VM by bastion and domain creds
* You can verify your access by looking in the portal at the IAM for the resource group or subscription. Click on the <org name> Administrators (e.g. Bravo Administrators) group to check the membership.

# evPAW Access
evPAW is an Enclave Virtual Privileged Access Workstation
## Via Remote Desktop app:
1. In upper right corner click on three dots and click "Subscribe with URL"
1. Enter this URL: https://rdweb.wvd.azure.us/api/arm/feeddiscovery
1. See Login section below

## Via RDWeb
1. In your browser, go to: https://rdweb.wvd.azure.us/arm/webclient/index.html
1. See Login section below

## Login
1. Sign in with your mpa account
1. You should see a Workspace bar with evpaw/PAW in the name
1. Click on the Session Desktop to access the PAW. You may need to login again with your MPA account creds

# Customer Enclave:
1. ***Note you may need to uncheck "Show only subscriptions selected in the global subscriptions filter" to see the subscription
2. Bravo Access: Subscription named "SMT-ME_MP_AGES_PHC_MISSION_DEV3_JALLEN"
>>a. Resource group with 3 in the name is where VMs for the apps are deployed
3. Delta/Wallaby/etc: Subscription named "SMT-ME_MP_AGES_PHC_MISSION_DEV2_JALLEN"
>>a. *** Important do not make changes that would impact the other MSFT groups working in other resource groups in this subscription.
b. Resource group for apps is "dev-mp-rg_3-avs". The RG "dev-mp-rg_2-avs" is for the storage container and key vault.
c. Domain joining VMs should use the DS service account located in the Resource Group "dev-mp-rg_2-avs" key vault. The key vault can only be accessed from a VM within the vnet, because of this a Bicep script to deploy the VM is needed.

If you get the following error. Ask to have your password reset. ![Hyperscale Expired Password.png](/.attachments/Hyperscale%20Expired%20Password-aad39427-4eb2-4658-996a-a9a62acee46e.png) 

## VM setup details:
See this wiki for subscription and resource group by customer enclave: https://dev.azure.com/BlueMountainAGMP/Blue%20Mountain%20Program%203.0/_wiki/wikis/Blue-Mountain-Program-3.0.wiki/156/ABS-Applications
This is the example for the Delta customer:
- Subscription = SMT-ME_MP_AGES_PHC_MISSION_DEV2_JALLEN
- Resource Group = dev-mp-rg_3-avs
- Subnet = dev-mp-avs-vnet/client
- Public IP Address = none

Note: the vnet for the enclave can be found in the resource group that contains "..._1..." so in the Delta example above "dev-mp-rg_1-avs". The Key Vault used to store all VM passwords can be found in the resource group that contains "..._2..." so in the Delta example above "dev-mp-rg_2-avs".

**File upload**: add files to the "mpducttape" storage account and then move the files to the enclave storage account. The "mpducttape" can be found here  https://portal.azure.us/#@phc-msft.us/resource/subscriptions/132cbd46-682d-4664-b63a-9de84b40f96e/resourceGroups/bm-ducttape/providers/Microsoft.Storage/storageAccounts/mpducttape/overview. The enclave storage account should be located in the same resource group as the applications and the name will vary by enclave.
	
## Domain Join
1. Request that a computer account be added to AD under the "services" OU for the organization that the application/VM is for. It needs to match the Computer Name of the VM and it is critical to change the users or groups who can domain join to the Domain Joining account name found in the enclave key vault or the Enclave Admins group.
1. After the deployment of a VM, login to the VM using the local admin creds provided during deployment.
1. In the VM start menu, type "workgroup" and select "change workgroup".
1. Select the "Change" button.
1. Select "Member of Domain".
1. Click "Ok" and enter domain joining creds (stored in key vault for the enclave). (** The computer account created in AD in step 1 must be created before continuing.) Click OK.
1. You should see a successful message and a prompt to restart.
1. Restart VM.

## Hardening Instructions (In process/may change)
1. Install McAfee Client
    a. Download the client from: 
		https://phcsoftware.blob.core.usgovcloudapi.net/deltapackages/New System Agent Install/FF_DA_SecurityAgents.zip  
    b. run the file  McAfeeAgent_FF_DP.exe as administrator, click Ok when “McAfee Agent Setup completed successfully”.
2. Install Tenable Nessus Client
Product Group instructions if you have access: https://dev.azure.com/msazure/Project%20Honeycomb/_wiki/wikis/Project-Honeycomb.wiki/340498/TSG-Nessus-Agent-Installation otherwise:
>>a. Download the client from 
>>>https://phcsoftware.blob.core.usgovcloudapi.net/deltapackages/New System Agent Install/FF_DA_SecurityAgents.zip
 
>>b. Run the file NessusAgent-10.1.3-x64.msi (or latest version) as **administrator** 
>>>At the Welcome screen click Next. 
At the License screen select “I accept the terms…” and then click Next. 
At the Destination screen click Next.  
At the Setup Type screen select Typical and then click Next. 
At the Configuration Option screen enter the following then click Next. 

>>>>Key = e8eed0cad64a25b316ff5745297de8944270996c903bd176768d859f2c39afe8
Server = nessus.phc-msft.us (or 172.16.26.108 for FF)
port = 8834 
Groups =  "Mission Plane, All Deployed Agents"
3. Logging client - TBD

# Troubleshooting

Opening tickets with Service Desk & Service Operations (unclass). **Tag IcM tickets with "ABS"**
- Service Desk: https://aka.ms/AzDO-SD
- Service Ops Control Plane: https://aka.ms/Azdo-Ops
- Service Ops Data Plane: https://aka.ms/AzDO-Ops-DataPlane

# Recordings for Hyperscale FF: 

• Session 1 (XMLspy deployment test): https://microsoft-my.sharepoint.com/:v:/p/asqueenm/ER3vwDAePZJHqDLsfE9KzN8B9JfYFesJULnfw4OVKt0bIQ
• Session 2: https://microsoft-my.sharepoint.com/:v:/p/lihernan/EVHiLHZ_SIdMqLaSMJNRrJYBJwSMxbU0hC_rPRACW5SCig
