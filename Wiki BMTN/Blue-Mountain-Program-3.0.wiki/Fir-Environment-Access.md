[[_TOC_]]
## Prerequisites:
•	An MSCPEC account with user name and password (email assetlabteam@microsoft.com to request an account and CC Andy Serfass aserfass@microsoft.com)
•	A Fir account on the Fir domain (email Andy Serfass aserfass@microsoft.com or another T0 admin and CC Andy Serfass)
## Login to Fir "Jumpbox":
1.	Open a private browser window (i.e. Edge)
2.	Connect to the jumpbox, in a private browser window:
- Go to this link: https://rdweb.wvd.microsoft.com/arm/webclient/index.html 
- Go to this link to access the BlueMountain jumpbox when admin privileges are needed: https://rdgw.mscpec.com/RDWeb/
-	The private window is helpful if you are logged into a @microsoft.com account in your (non-private) browser already so rdweb does not grab the wrong account.
3.	Enter your MSCPEC username and password
4.	Click on the computer icon (e.g. ASL-WVD...)
5.	Review Selections and click allow.
6.	Enter your MSCPEC username and password
7.	A desktop should open on your virtual desktop (ASL-WVD...). This is a virtual desktop with network access to the Fir RCA (Stack Hub) for portal access, PEP access, or RDP access.
Portal - Access the stack hub portals:
Portal with the Gates deployment or the admin portal for stack hub management.
1.	Open the Edge browser and go to this address: https://portal.tca.tca.contosolabs.com/ 
o	the admin portal can be accessed via https://adminportal.tca.tca.contosolabs.com/ 
2.	Enter your sith credentials (sith\<username> or <username>@sith.ts)
o	enter your specific username instead of <username> (i.e. sith\JoeSmith)

## RDP - Access a Gates VM
1.	Click the virtual desktop Windows start menu.
2.	Type: RDP
3.	Click Remote Desktop Connection
4.	Enter the IP address and port number for the IA Bastion; currently 10.10.16.36:3390; click connect
5.	Enter your sith credentials (sith\<username> or <username>@sith.ts)
o	enter your specific username instead of <username>
6.	You can now RDP into other Gates VMs by following steps 1-5 while replacing step 4 with the desired IP address of the VM.

## Gates Portal - to access the user Gates portal
1.	Open the Edge browser and go to this address: https://portal.sith.ts 
2.	Enter your sith credentials (sith\<username> or <username>@sith.ts)
o	enter your specific username instead of <username> (i.e. sith\JoeSmith)

## PEP - Launch a PEP session:
1.	Access the adminportal using the instructions above
2.	Click on region management from the adminportal dashboard, then click properties, you will see the PEP IPs listed there.
3.	Follow these instructions: https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-privileged-endpoint?view=azs-2102 
o	Use the "tca\cloudadmin" username and associated password when authenticating in powershell

## Password Change
1.	Access the IA Bastion
o	Click the virtual desktop Windows start menu.
o	Type: RDP
o	Click Remote Desktop Connection
o	Enter the IP address and port number for the IA Bastion; currently 10.10.16.36:3390
o	Enter your sith credentials (sith\<username> or <username>@sith.ts)
2.	Click the desktop start menu, type "active", then click "Active Directory Users and Computers"
3.	To change your T0 password, go to the Tier 0 group
![image.png](/.attachments/image-84f5dcc3-0567-4886-953d-a08a57a5b4e0.png)
o	For other user passwords find the appropriate group (T0-Accounts, T1-Accounts, T2-Accounts). For unprivileged users, goto sith.ts -> User Accounts -> Enabled Users.

## Large File Transfers into Fir VMs
### Move files to Fir jumpbox
1. Upload file(s) to Azure Commercial Storage account (assuming the information is allowed on Azure Commercial)
1. Generate a SAS URL for each file that needs to be transferred
1. Login to the MSCPEC jumpbox (see Login to Fir "Jumpbox" above)
1. Open Edge (or a browser) and enter the SAS URL(S) to download each file to your jumpbox hard drive
### Connect to VM
1. Using the "RDP - Access a Gates VM" instructions above access the VM that the files need to be moved to (usually starting with the IA Bastion VM) with the modification in the next step.
1. **Before** you click connect in the Remote Desktop Connection window above, at the bottom of that window click "Show Options", click the "local resources" tab, click "more", click "Drive", and click the checkbox for the "C" drive.
1. Finishing connecting to the VM (e.g. click "connect" on the Remote Desktop Connection window)
-- Now the local jumpbox C drive will be available in the RDP session VM.
1. The jumpbox C drive will be available at the bottom of the VM local File Explorer "This PC" window
1. Copy files from the jumpbox to the local VM C drive
1. Repeat steps 5-9 to move the files to additional VMs (VM Inception)