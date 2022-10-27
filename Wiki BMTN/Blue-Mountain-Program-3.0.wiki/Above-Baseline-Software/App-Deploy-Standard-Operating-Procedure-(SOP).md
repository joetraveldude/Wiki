# Standard Operating Procedure (SOP) for Deploying Above Baseline Software

## License

```plaintext
    Contractor Name: Novetta, Inc.
    Contractor Address: 7921 Jones Branch Drive, McLean, VA 22102
```

[[_TOC_]]

## Deploying the Application in Pre-Production

### Creating the Infrastructure

#### Creating a Resource Group

1. Open a web browser.
2. Navigate to the Azure Stack Hub Portal of the environment the application will be deployed to.
3. If you are not logged in, login with a tier 0 account.
4. Select "Create a resource" in the Azure Stack Hub Portal.
5. Search for "Resource group" in the Marketplace.
6. Select "Create".
7. Name your resource group with the standard naming convention. The standard naming convention is (resource abbreviation)-gates-(manufacturer name)-(application name)-(project abbreviation)-(stack hub location). For example : rg-gates-deltek-cobra-(project abbreviation)-(stack hub location).
8. Select "Review + create".

#### Creating a Network Security Group

1. Open a web browser.
2. Navigate to the Azure Stack Hub Portal of the environment the application will be deployed to.
3. If you are not logged in, login with a tier 0 account.
4. Select "Create a resource" in the Azure Stack Hub Portal.
5. Search for "Network security group" in the Marketplace.
6. Select "Create".
7. Enter the name of the "Resource group" created for your application.
8. Name your network security group with the standard naming convention. The standard naming convention is (resource abbreviation)-gates-(architected resource VNet)-(project abbreviation)-(stack hub location)-(architected resource subnet). For example : nsg-gates-collaboration-(Project)-(Location)-deltek.
9. Select "Review + create".
10. Search for your Network Security Group name in the Azure Stack Hub Portal search bar.
11. Locate the "Settings" section from your Network Security Group Overview.
12. Select the "Inbound security rules" setting.
13. Use "Add" to configure your "Inbound security rules".
14. Add a rule to allow inbound administration via TCP as follows:

Setting                         | Value
:-----------------------------: | :------------------------------------------------------------------------------------------------------------------:
Source                          | IP Addresses
Source IP addresses/CIDR ranges | 172.16.2.6, 172.16.2.121, 10.11.53.0/24, 10.128.0.116, 10.11.2.65, 172.16.2.4, 172.16.2.5, 10.10.21.116, 172.16.32.4
Source port ranges              | *
Destination                     | Any
Destination port ranges         | 22, 135, 443, 445, 3389, 5985-5986, 49152-65535
Protocol                        | TCP
Action                          | Allow
Priority                        | 100
Name                            | AllowInboundAdministrationTCP
Description                     | Default Environment Rules

15. Add a rule to  allow inbound Assured Compliance Assessment Solution (ACAS) scan access via TCP and UDP as follows:

Setting                         | Value
:-----------------------------: | :---------------------------------------------------:
Source                          | IP Addresses
Source IP addresses/CIDR ranges | 172.16.2.111
Source port ranges              | *
Destination                     | Any
Destination port ranges         | *
Protocol                        | *
Action                          | Allow
Priority                        | 110
Name                            | AllowInboundAcasTCPandUDP
Description                     | Allow Acas scans from Acas. Default Environment Rules

16. Add a rule to allow inbound Host Based Security System (HBSS) agent wakeup and remote install via TCP as follows:

Setting                         | Value
:-----------------------------: | :----------------------------------------------------------------:
Source                          | IP Addresses
Source IP addresses/CIDR ranges | 172.16.2.131
Source port ranges              | *
Destination                     | Any
Destination port ranges         | 22, 445, 8081
Protocol                        | TCP
Action                          | Allow
Priority                        | 120
Name                            | AllowInboundHbssTCP
Description                     | Remote install / agent wakeup from HBSS. Default Environment Rules

17. Add a rule to allow inbound Host Based Security System (HBSS) communication via UDP as follows:

Setting                         | Value
:-----------------------------: | :-----------------------:
Source                          | IP Addresses
Source IP addresses/CIDR ranges | 172.16.2.131
Source port ranges              | *
Destination                     | Any
Destination port ranges         | 8082
Protocol                        | UCP
Action                          | Allow
Priority                        | 130
Name                            | AllowInboundHbssUDP
Description                     | Default Environment Rules

18. Add a rule to allow inbound Windows Server Update Services (WSUS) traffic via TCP as follows:

Setting                         | Value
:-----------------------------: | :--------------------------------------:
Source                          | IP Addresses
Source IP addresses/CIDR ranges | 10.11.2.78, 172.16.2.21
Source port ranges              | *
Destination                     | Any
Destination port ranges         | 8530-8531
Protocol                        | TCP
Action                          | Allow
Priority                        | 140
Name                            | AllowWSUSPatching
Description                     | WSUS Patching. Default Environment Rules

19. Add a rule to deny inbound communications not whitelisted above via TCP and UDP as follows:

Setting                         | Value
:-----------------------------: | :-----------------------------------------:
Source                          | IP Addresses
Source IP addresses/CIDR ranges | *
Source port ranges              | *
Destination                     | Any
Destination port ranges         | *
Protocol                        | *
Action                          | Deny
Priority                        | 4096
Name                            | DenyAllInbound
Description                     | Deny All Inbound. Default Environment Rules

20. Select the "Outbound security rules" setting.
21. Use "Add" to configure your "Outbound security rules".
22. Add a rule to allow outbound communication to the environment via TCP and UDP as follows:

Setting                         | Value
:-----------------------------: | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:
Source                          | IP Addresses
Source IP addresses/CIDR ranges | *
Source port ranges              | *
Destination                     | 10.11.1.4/31, 172.20.0.0/24, 172.16.2.0/24, 172.16.3.0/27, 172.16.3.32/27, 172.16.1.0/24, 172.16.20.0/23, 172.16.18.0/23, 172.16.22.0/23, 172.16.32.0/23, 172.16.16.0/23, 10.128.0.0/23, 10.11.2.0/23, 10.11.53.0/24, 10.10.16.0/22, 10.10.21.116/32
Destination port ranges         | *
Protocol                        | *
Action                          | Allow
Priority                        | 100
Name                            | AllowAllOutboundToSithTCPandUDP
Description                     | Allow all outbound to sith TCP and UDP. Default Environment Rules

23. Add a rule to deny outbound communications not whitelisted above via TCP and UDP as follows:

Setting                         | Value
:-----------------------------: | :------------------------------------------:
Source                          | IP Addresses
Source IP addresses/CIDR ranges | *
Source port ranges              | *
Destination                     | Any
Destination port ranges         | *
Protocol                        | *
Action                          | Deny
Priority                        | 4096
Name                            | DenyAllOutbound
Description                     | Deny All Outbound. Default Environment Rules

#### Creating a Subnet in the Collaboration VNet

1. Open a web browser.
2. Navigate to the Azure Stack Hub Portal of the environment the application will be deployed to.
3. If you are not logged in, login with a tier 0 account.
4. Search for "Virtual Networks" in the Azure Stack Hub Portal.
5. Filter by name for your collaboration VNet.
6. Select your collaboration VNet.
7. Select the "Subnets" setting.
8. Select "+ Subnet" to add a subnet.
9. Name your subnet with the name of your software. For example "Matlab"
10. Assign the "Subnet address range" a /28 CIDR range in the 172.16.16.0/20 address space. It can not have a IPv4 range that conflicts with the other subnets, so review the existing subnets to prevent a conflict.
11. Assign the "Network security group" to the network security group you created for the application.
12. Select "Save".

#### Creating a Virtual Machine

1. Open a web browser.
2. Navigate to the Azure Stack Hub Portal of the environment the application will be deployed to.
3. If you are not logged in, login with a tier 0 account.
4. Select "Create a resource" in the Azure Stack Hub Portal.
5. Search for "Windows Server 2019 Datacenter - BYOL use only".
6. Select "Create".
7. In the Resource group field, select the resource group created for the application.
8. In the Virtual machine name field, name your virtual machine with the standard naming convention. The Standard naming convention is (resource abbreviation)-gates-(manufacturer name)-(application name)-(project abbreviation)-(stack hub location)-<client/app/licensing + 01/02/03>. For example : vm-gates-deltek-cobra-(project abbreviation)-(stack hub location)-app01.
9. In the Region field, set the Region to the name of the region for the Azure Stack Hub in which the app is being deployed.
10. In the Size field, set the size as "Standard A1 v2". The VM size will change later to fit the applications needs.
11. Select "Select".
12. In the Administrator account field, enter a "Username" You will need it for the first access the device.
13. In the Administrator account field, enter a "Password". You will need it for the first access the device.
14. In the Public inbound ports field, select None.
15. Change your tab to Disks by selecting "Next : > Disks".
16. In the OS disk type field, select "Standard HDD". The disk type and size will change later to fit the applications needs.
17. In the Data Disks field, select "Create and attach a new disk".
18. In the Size field, select "change size".
19. Set the Disk SKU to "Standard HDD".
20. Set the Custom disk size (GiB) to 256.
21. Select OK at the bottom of the portal.
22. Select OK again at the bottom of the "Create a new disk" page.
23. Change your tab to Networking by selecting "Next : Networking >".
24. In the Virtual network field, assign it to the VNet for collaboration.
25. In the Subnet field, select the subnet created for the application.
26. In the Public IP field, select None.
27. Change your tab to Management by selecting "Next : Management >".
28. In the Boot diagnostics field, select Off.
29. Change your tab to Review + Create by selecting "Review + Create".
30. Observe the top of the screen to see if the validation passes. If the validation passes, go to step 32. Otherwise, go to step 31.
31. If the prompt states "Validation Failed: Required information is missing or not valid." click on the tab showing a red dot, and respond to the error text. Below are solutions to common errors.

Error                                                                                                                                                 | Solution
:---------------------------------------------------------------------------------------------------------------------------------------------------- | :-----------------------------------------------------
Azure resource names cannot contain special characters \\ / " " [ ] : \| < > + = ; , ? * @ & , whitespace, or begin with '_' or end with '.' or '-'". | Rename the Resource generating the error
This virtual machine's size 'Standard A1 v2' isn't compatible with the disk type you selected. Change the virtual machine size to use premium disks.  | Change from Premium SSD to Standard HDD
The value must not be empty                                                                                                                           | Provide a value
The value must be between X and XX characters long                                                                                                    | Work to fit the value within the character requirement
The error isn't listed in this table                                                                                                                  | Contact Ops

32. Select Create.

### Adding the Virtual Machine to the Domain
Hyperscale: When a VM is domain joined the local admin account (used during VM deployment) no longer works. If a VM is domain joined, by policy, only MPA accounts can access the VM. If the MPA account stops working for VM access, the local admin account may work and then the VM needs to be rejoined to the domain. 

#### Connecting to the Environment IA Bastion Host to Manage a Virtual Machine

1. If you are interfacing with a connected environment, go to step 2. Otherwise, go to step 16.
2. Open a web browser on your computer.
3. Use your address bar to navigate to the Azure Virtual Desktop web client.
4. When you are prompted to "Sign In", select the "Email Address" field.
5. Enter the email address of your Microsoft credentials.
6. When you are prompted to "Enter password", select the "Password" field.
7. Enter the password of your Microsoft credentials.
8. After being presented virtual desktop icons, click the icon of the virtual desktop you want to connect to.
9. When you are prompted to "Allow the remote computer to access the following resources on my computer" click "Allow".
10. After you are prompted to "Enter your credentials", select the "Email Address" field.
11. Enter the email address of your Microsoft credentials.
12. Select the "Password" field.
13. Enter the password of your Microsoft credentials.
14. Click "Submit".
15. Go to step 17.
16. Follow the local procedures that allow you to interact with the environment.
    - Fir: [Fir Environment Access](/Fir-Environment-Access)
    - Fairfax: [Fairfax](/Above-Baseline-Software/Fairfax)
17. Click the windows icon on the taskbar of your virtual desktop.
18. Type "Remote Desktop Connection".
19. Click on the "Desktop app" icon.
20. In the "Remote Desktop Connection" window, select the "Computer" field.
21. In the Computer field, enter the IP address of the "Cloud Services Router", colon, and the port listening for the "Remote Desktop Protocol". For example, "192.168.1.1:3390".
22. Select "Connect".
23. In the "Windows Security" window, select the "User name" field.
24. Enter your tier 0 account username with the full domain name.
25. Select the "Password" field.
26. Enter your tier 0 account password.
27. Select "OK".
28. After being prompted "The identity of the remote computer cannot be verified. Do you want to connect anyway?" click "Yes".
29. In the "Remote Desktop Connection" window, click the maximize icon in the upper right.
30. In your "Remote Desktop Connection" window, select OK after being presented any environment consent statements.

#### Adding the Virtual Machine Object to Active Directory

1. Complete the steps documented under the section [Connecting to the Environment IA Bastion Host to Manage a Virtual Machine](#Connecting-to-the-Environment-IA-Bastion-Host-to-Manage-a-Virtual-Machine).
2. In your "Remote Desktop Connection" window for the IA Bastion Host, click the windows icon on the taskbar.
3. Type "Active Directory Users and Computers".
4. Click on the "Desktop app" icon.
5. After being prompted "Do you want to allow this app to make changes to your device?" select "Yes".
6. In your "Active Directory Users and Computers" window, click once to select your environments domain controller object.
7. In your "Active Directory Users and Computers" window toolbar, select "Action".
8. Hover your cursor over "New >".
9. Select "Computer".
10. In the Computer name field, enter the name architected for the Virtual Machine.
11. Select "OK".
12. Right click on the newly created computer object.
13. Select "Move...".
14. Select new OU:
  a. For Cedar: "Tier 1 Servers" then Select "Staging"
  b. For Fairfax: Select the "Services" OU within the correct organization for the app you're deploying (e.g. Delta)
15. Select "OK".

#### Configuring the Virtual Machine

1. Complete the steps documented under the section [Connecting to the Environment IA Bastion Host to Manage a Virtual Machine](#Connecting-to-the-Environment-IA-Bastion-Host-to-Manage-a-Virtual-Machine).
2. In your "Remote Desktop Connection" window for the IA Bastion Host, click the windows icon on the taskbar.
3. Type "Remote Desktop Connection".
4. Click on the "Desktop app" icon.
5. In the "Remote Desktop Connection" window, select the "Computer" field.
6. In the Computer field, enter the IP address of the Virtual Machine.
7. Select "Connect".
8. In the "Windows Security" window, select the "User name" field.
9. Enter the username defined during virtual machine creation.
10. Select the "Password" field.
11. Enter the password defined during virtual machine creation.
12. Select "OK".
13. In your "Remote Desktop Connection" window for the Virtual Machine, click the windows icon on the taskbar.
14. Type "Computer Information".
15. Click on the "System" Control panel icon.
16. In the "System" window, in the "Computer name, domain, and workgroup settings" section, select "Change settings".
17. When prompted "Do you want to allow this app to make changes to your device?" click "Yes".
18. In the System Properties window, select "Change...".
19. In the Computer name field, enter the name architected for the Virtual Machine.
20. In the Workgroup field, delete the string "WORKGROUP".
21. In the Member of section, toggle the setting to "Domain".
22. In the "Domain" field, and enter the FQDN for your domain, e.g. sith.ts.
23. Select "OK".
24. In the "Windows Security" window, select the "User name" field.
25. Enter your tier 0 account username with the full domain name.
26. Select the "Password" field.
27. Enter your tier 0 account password.
28. Select "OK".
29. When prompted with "Welcome to X domain" click "OK".
30. When prompted with "You must restart your computer to apply these changes" click "Ok".
31. Select "Close".
32. When prompted with "You must restart your computer to apply these changes" select "Restart Now".
33. Wait for the restart to complete.
34. If the Virtual Machine restarts successfully, go to step 36. Otherwise go to step 35.
35. Call Ops.
36. In your "Remote Desktop Connection" window for the IA Bastion Host, click the windows icon on the taskbar.
37. Type "Remote Desktop Connection".
38. Click on the "Desktop app" icon.
39. In the "Remote Desktop Connection" window, select the "Computer" field.
40. In the Computer field, enter the name architected for the Virtual Machine.
41. Select "Connect".
42. In the "Windows Security" window, select the "User name" field.
43. Enter your tier 1 account username with the full domain name.
44. Select the "Password" field.
45. Enter your tier 1 account password.
46. Select "OK".

### Providing the Developers Access to the Infrastructure


1. Complete the steps documented under the section [Connecting to the Environment IA Bastion Host to Manage a Virtual Machine](#Connecting-to-the-Environment-IA-Bastion-Host-to-Manage-a-Virtual-Machine).
2. In the "Remote Desktop Connection" window for the IA Bastion Host, click the windows icon on the taskbar.
3. Type "Remote Desktop Connection".
4. Click on the "Desktop app" icon.
5. In the "Remote Desktop Connection" window, select the "Computer" field.
6. In the Computer field, enter the name architected for the Virtual Machine.
7. Select "Connect".
8. In the "Windows Security" window, select the "User name" field.
9. Enter your tier 1 account username with the full domain name.
10. Select the "Password" field.
11. Enter your tier 1 account password.
12. Select "OK".

### Testing the Application in the Staging OU

1. Open a web browser.
2. Left click on the white space of the address bar.
3. Navigate to the Azure Dev Ops web app by typing its URL (<https://dev.azure.com/>).
4. When you are prompted to "Sign In", select the "Email Address" field.
5. Enter the email address of the account that is connected to the Microsoft organization.
6. When you are prompted to "Enter password", select the "Password" field.
7. Enter the password of the account that is connected to the Microsoft organization.
8. Click "Submit".
9. Select your project in ADO.
10. Expand the sidebar by selecting the ">>" symbol in the bottom left.
11. Hover over "Test Plans".
12. Select "Test plans".
13. Select "All".
14. Select the mission your application is assigned to.
15. Expand "Above Baseline Software" by selecting the ">" symbol next to it.
16. Select the name of the application you are testing.
17. Select the "Test Points" for the "Staging OU" by selecting the boxes next to the "Test Points" titles that contain "Staging OU".
18. Select "Run for web application".
19. In the "Runner - Test Plans" window complete the instructions provided.
20. If the "EXPECTED RESULT" is the same as what happened when you completed the instructions provided, go to step 21. Otherwise go to step 26.
21. Select "&#10004;".
22. If there are additional instructions, go to step 19. Otherwise go to step 23.
23. If the "next>" option is greyed out, go to step 33. Otherwise go to step 24.
24. Select "next>".
25. Go to step 19.
26. Select "X".
27. Select the "COMMENT" box.
28. Type what happened.
29. If there are additional instructions, go to step 19. Otherwise go to step 30.
30. If the "next>" option is greyed out, go to step 33. Otherwise go to step 31.
31. Select "next>".
32. Go to step 19.
33. Select "Save and close".
34. Notify the application deployment team the "Staging Test Plan" has been completed for the application.

### (Optional) Configuring Remote App to Host the App Client

#### (Optional) Configure Remote App NSG Rules

1. Open a web browser.
2. Navigate to the Azure Stack Hub Portal of the environment the application will be deployed to.
3. If you are not logged in, login with a tier 0 account.
4. Search for your application's Network Security Group name in the Azure Stack Hub Portal search bar. The standard naming convention is (resource abbreviation)-gates-(architected resource VNet)-(project abbreviation)-(stack hub location)-(architected resource subnet). For example : nsg-gates-collaboration-(Project)-(Location)-deltek.
5. Locate the "Settings" section from your Network Security Group overview.
6. Select the "Inbound security rules" setting.
7. Use "Add" to configure your "Inbound security rules".
8. Add a rule to allow inbound RDS1 all to your application via TCP as follows:

Setting                              | Value
:----------------------------------: | :-----------------------------------------------------------------------------------------:
Source                               | IP Addresses
Source IP addresses/CIDR ranges      | 172.20.0.0/24
Source port ranges                   | *
Destination                          | IP Addresses
Destination IP addresses/CIDR ranges | (IP address of your application VMs. For example : 172.16.30.4, 172.16.30.6)
Destination port ranges              | 135, 139, 443, 445, 3389, 5504, 5985, 49152-65535
Protocol                             | TCP
Action                               | Allow
Priority                             | 400
Name                                 | AllowInboundRSD1AllTo(application name)Host (For example : AllowInboundRSD1AllToDeltekHost)
Description                          | Default Remote App Rules

9. Select "Add".
10. Search for your virtual desktop infrastructure's Network Security Group name in the Azure Stack Hub Portal search bar. The standard naming convention is (resource abbreviation)-gates-vdi-(project abbreviation)-(stack hub location)-application. For example : nsg-gates-vdi-(Project)-(Location)-application.
11. Locate the "Settings" section from your Network Security Group overview.
12. Select the "Inbound security rules" setting.
13. Select the "AllowInboundRemoteAppHostsToRDS1ALL" rule.
14. Add a "," to the end of the string in the "Source IP addresses/CIDR ranges" field.
15. Add your application virtual machine's IP address to the end of the string in the "Source IP addresses/CIDR ranges" field.
16. Select "Save".

#### (Optional) Verify Application Health

1. Complete the steps documented under the section [Providing the Developers Access to the Infrastructure](#Providing-the-Developers-Access-to-the-Infrastructure).
2. In your "Remote Desktop Connection" window for the virtual machine, click the Windows icon on the taskbar.
3. Type the name of your application.
4. Select the "Desktop app" icon.
5. Verify the application launches successfully.

#### (Optional) Configure Remote App Users Active Directory Group

1. Complete the steps documented under the section [Connecting to the Environment IA Bastion Host to Manage a Virtual Machine](#Connecting-to-the-Environment-IA-Bastion-Host-to-Manage-a-Virtual-Machine).
2. In your "Remote Desktop Connection" window for the IA Bastion Host, click the Windows icon on the taskbar.
3. Type "Active Directory Users and Computers".
4. Click on the "Desktop app" icon.
5. After being prompted "Do you want to allow this app to make changes to your device?" select "Yes".
6. Click the small "+" box next to the environments domain controller to expand it.
7. Click the small "+" box next to the "Groups" organizational unit to expand it.
8. Click the small "+" box next to the "Security Groups" organizational unit to expand it.
9. Click the small "+" box next to the "Collaboration" organizational unit to expand it.
10. If you can find your application's organizational unit inside of the "Collaboration" organizational unit go to step 16. Otherwise, go to step 11.
11. Right click on the "Collaboration" organizational unit.
12. Hover your icon over "New >".
13. Select "Organizational Unit".
14. In the "Name" field, enter your application's name. For example "Cobra".
15. Select "OK".
16. Right click the OU with your application's name.
17. Hover your icon over "New >".
18. Select "Group".
19. In the "Group name" field, enter your group name with the standard naming convention. The standard naming convention is (object abbreviation)\_gs_(application name)_users. For example : "grp_gs_cobra_users".
20. Select "OK".
21. Right click the group you created.
22. Select "Properties".
23. Select the "Members" tab.
24. Select "Add...".
25. In the "Enter the object names to select" field, enter your user account name. For example: "sean.lapier.ctr".
26. Select "Check Names".
27. Select "OK".
28. Select "OK".

#### (Optional) Configure Remote App Access Admins Active Directory Group

1. Complete the steps documented under the section [Connecting to the Environment IA Bastion Host to Manage a Virtual Machine](#Connecting-to-the-Environment-IA-Bastion-Host-to-Manage-a-Virtual-Machine).
2. In your "Remote Desktop Connection" window for the IA Bastion Host, click the Windows icon on the taskbar.
3. Type "Active Directory Users and Computers".
4. Click on the "Desktop app" icon.
5. After being prompted "Do you want to allow this app to make changes to your device?" select "Yes".
6. Click the small "+" box next to the environments domain controller to expand it.
7. Click the small "+" box next to the "Groups" organizational unit to expand it.
8. Click the small "+" box next to the "Security Groups" organizational unit to expand it.
9. Click the small "+" box next to the "Collaboration" organizational unit to expand it.
10. Right click the OU with your application's name.
11. Hover your icon over "New >".
12. Select "Group".
13. In the "Group name" field, enter your group name with the standard naming convention. The standard naming convention is (object abbreviation)\_gs_(application name)_AccessAdmins. For example : "grp_gs_cobra_AccessAdmins".
14. Select "OK".
15. Right click the remote app users group.
16. Select "Properties".
17. Select the "Managed By" tab.
18. Select "Change...".
19. In the "Enter the object names to select" field, enter your access admins group name.
20. Select "Check Names".
21. Select "OK".
22. Select "OK".
23. Select "Apply".
24. Select "OK".

#### (Optional) Configure Group Policy Objects that Enable Remote App

1. Complete the steps documented under the section [Connecting to the Environment IA Bastion Host to Manage a Virtual Machine](#Connecting-to-the-Environment-IA-Bastion-Host-to-Manage-a-Virtual-Machine).
2. In your "Remote Desktop Connection" window for the IA Bastion Host, click the Windows icon on the taskbar.
3. Type "Group Policy Management".
4. Click on the "Desktop app" icon.
5. After being prompted "Do you want to allow this app to make changes to your device?" select "Yes".
6. Click the small "+" box next to the "Forest:XXXX.XX" object to expand it.
7. Click the small "+" box next to the "Domains" object to expand it.
8. Click the small "+" box next to the environments domain controller object to expand it.
9. Click the small "+" box next to the "Group Policy Objects" object to expand it.
10. Right click the "Custom - RDP and Restricted Groups".
11. Select "Copy".
12. Right click the "Group Policy Object" object.
13. Select "Paste".
14. Select "Preserve the existing permissions".
15. Select "OK".
16. Right click the "Copy of Custom - RDP and Restricted Groups" object.
17. Select "Rename".
18. Type your group policy object name with the standard naming convention. The standard naming convention is (application name) - RDP and Restricted Groups. For example : "wInsight - RDP and Restricted Groups".
19. Click the small "+" box next to the "Tier 1 Servers" object to expand it.
20. Click the small "+" box next to the "Collaboration" object to expand it.
21. Right click the object with your application's name.
22. Select "Link an Existing GPO...".
23. In the "Group Policy objects:" field, select "Gates - VDI Security Overrides".
24. Right click the object with your application's name.
25. Select "Link an Existing GPO...".
26. In the "Group Policy objects:" field, select the group policy object that you created.
27. Select "OK".
28. Select the object with your application's name.
29. Select the "Linked Group Policy Objects" tab.
30. Right click the the group policy object that you created.
31. Select "Edit".
32. In the Group Policy Management Editor window, click the small "+" box next to "Computer Configuration".
33. Click the small "+" box next to "Policies".
34. Click the small "+" box next to "Windows Settings".
35. Click the small "+" box next to "Security Settings".
36. Click the small "+" box next to "Local Policies".
37. Select the "User Rights Assignment" object.
38. Right click "Allow log on through Remote Desktop Services".
39. Select "Properties".
40. Select "XXXX\grp_gs_xmlspy_users".
41. Select "Remove".
42. Select "Add User or Group..."
43. Select "Browse...".
44. In the "Enter the object names to select" field, enter your remote app users group name. For example: "grp_gs_cobra_users".
45. Select "Check Names".
46. Select "OK".
47. Select "OK".
48. Select "Apply".
49. Select "OK".
50. Select the "Restricted Groups" object.
51. Right click "Remote Desktop Users".
52. Select "Properties".
53. In the "Members of this group:" field, select "XXXX\grp_gs_xmlspy_users".
54. Select "Remove".
55. Select "Add...".
56. Select "Browse...".
57. In the "Enter the object names to select" field, enter your remote app users group name. For example: "grp_gs_cobra_users".
58. Select "Check Names".
59. Select "OK".
60. Select "OK".
61. Select "Apply".
62. Select "OK".

#### (Optional) Establish the Application on the Remote App Server

1. Complete the steps documented under the section [Connecting to the Environment IA Bastion Host to Manage a Virtual Machine](#Connecting-to-the-Environment-IA-Bastion-Host-to-Manage-a-Virtual-Machine).
2. In the "Remote Desktop Connection" window for the IA Bastion Host, right click the Windows icon on the taskbar.
3. Select "Run".
4. Type "mstsc /admin".
5. In the "Remote Desktop Connection" window, select the "Computer" field.
6. In the Computer field, enter "RDS1ALL".
7. Select "Connect".
8. In the "Windows Security" window, select the "User name" field.
9. Enter your tier 2 account username with the full domain name.
10. Select the "Password" field.
11. Enter your tier 2 account password.
12. Select "OK".
13. After being prompted "Do you want to allow this app to make changes to your device?" select "Yes".
14. Select "Remote Desktop Services".
15. Select "Servers".
16. If you do not see RDS1ALL listed in the servers section, go to step 17. Otherwise, go to step 24.
17. Select "Manage".
18. Select "Add Servers".
19. In the "Name (CN):" field, enter "RDS1ALL".
20. Select "Find Now".
21. Select "rds1all".
22. Select the arrow.
23. Select "OK".
24. If you do not see your applications virtual machine computer name listed in the servers section, go to step 25. Otherwise, go to step 32.
25. Select "Manage".
26. Select "Add Servers".
27. In the "Name (CN):" field, enter your applications virtual machine computer name.
28. Select "Find Now".
29. Select your applications virtual machine computer name.
30. Select the arrow.
31. Select "OK".
32. Select "Overview".
33. In the "DEPLOYMENT SERVERS" section, select "TASKS".
34. Select "Add RD Session Host Servers".
35. Select your applications virtual machine computer name.
36. Select the arrow.
37. Select "Next >".
38. Select "Restart remote computers as needed".
39. Select "Add".
40. After the installation of the "RD Session Host role service", Select "Close".
41. Select "Collections".
42. In the "COLLECTIONS" section, select "TASKS".
43. Select "Create Session Collection".
44. Select "Next >".
45. Select "Name:".
46. Type your collection name with the standard naming convention. The standard naming convention is Gates (application name) For example : "Gates Visual Studio 2022".
47. Select "Next >".
48. Select your applications virtual machine computer name.
49. Select the arrow.
50. Select "Next >".
51. Select "Next >".
52. Select the box next to "Enable user profile disks" to uncheck the box and disable user profile disks.
53. Select "Next >".
54. Select "Create".
55. Under "Collections", Select "Gates (application name)". For example : "Gates Visual Studio 2022".
56. In the "REMOTEAPP PROGRAMS" section, select "TASKS".
57. Select "Publish RemoteApp Programs".
58. Click the box next to your application.
59. Select "Next >".
60. Select "Publish".
61. Select "Close".
62. In the "REMOTEAPP PROGRAMS" section, right click your application.
63. Select "Edit Properties".
64. Select "User Assignment".
65. Select "Only specified users and groups".
66. Select "Add...".
67. In the "Enter the object names to select" field, enter your remote app users group name. For example: "grp_gs_cobra_users".
68. Select "Check Names".
69. Select "OK".
70. Select "Apply".
71. Select "OK".

#### (Optional) Verify the Remote App Works for the Application

1. Open a web browser.
2. Navigate to <https://portal.xxxx.xx>.
3. Select "LOGIN WITH PASSWORD".
4. In the Username field, enter your user account username.
5. In the Password field, enter your user account password.
6. Select "PASSWORD LOGIN".
7. Select a "VDIXX" tile.
8. Select "OK" to the US Department of Defense Warning Statement.
9. In the Username field, enter your user account username.
10. In the Password field, enter your user account password.
11. Select the arrow.
12. In your "VDIXX" web browser remote session, select the Windows icon.
13. Type your application name.
14. If your application appears, go to step 27. Otherwise, go to step 15.
15. In your "VDIXX" web browser remote session, select the Windows icon.
16. Type "Control Panel".
17. Select "View by: Category".
18. Select "Small icons".
19. Select "RemoteApp and Desktop Connections".
20. In the "Work Resources" section, select "Remove".
21. In the "Work Resources (1)" section, select "Remove".
22. In your "VDIXX" web browser remote session, select the Windows icon.
23. Select the profile icon.
24. Select "Sign out".
25. Select "Reconnect".
26. Go to step 8.
27. Select the "Desktop app" icon.
28. Verify the app launches similarly to how it launched in [(Optional) Verify Application Health](#optional-verify-application-health).

### Hardening Systems

#### Moving the Virtual Machine to a Hardened Organizational Unit in Active Directory

1. Complete the steps documented under the section [Connecting to the Environment IA Bastion Host to Manage a Virtual Machine](#Connecting-to-the-Environment-IA-Bastion-Host-to-Manage-a-Virtual-Machine).
2. In your "Remote Desktop Connection" window for the IA Bastion Host, click the windows icon on the taskbar.
3. Type "Active Directory Users and Computers".
4. Click on the "Desktop app" icon.
5. After being prompted "Do you want to allow this app to make changes to your device?" select "Yes".
6. In your "Active Directory Users and Computers" window, click the small "+" box next to your environments domain controller object to expand it.
7. Click the small "+" box next to the "Tier 1 Servers" organizational unit to expand it.
8. Click the small "+" box next to the "Collaboration" organizational unit to expand it.
9. If you can find your application manufacturer organizational unit inside of the "Collaboration" organizational unit go to step 15. Otherwise, go to step 10.
10. Right click on the "Collaboration" organizational unit.
11. Hover your icon over "New >".
12. Select "Organizational Unit".
13. In the "Name" field, enter your application manufacturers name. For example "Deltek".
14. Select "OK".
15. Select the "Staging" OU.
16. Right click on the computer object created for your Virtual Machine.
17. Select "Move...".
18. Select the organizational unit with your application manufacturers name.
19. Select "OK".

#### Copying the Security Tools to your Applications Virtual Machine

1. Complete the steps documented under the section [Providing the Developers Access to the Infrastructure](#Providing-the-Developers-Access-to-the-Infrastructure).
2. In your "Remote Desktop Connection" window for the Virtual Machine, click the windows icon on the taskbar.
3. Type "File Explorer".
4. Select the "Desktop app" icon.
5. In the "File Explorer" window, left click the white space of the address bar.
6. Type the share location holding your "Security Tools" folder. For example, "\\\Share-Drive\C$".
7. To the right of the address bar, click on the "$\to$" symbol.
8. In the "Windows Security" window, select the "User name" field.
9. Enter your tier 0 account username with the full domain name.
10. Select the "Password" field.
11. Enter your tier 0 account password.
12. Select "OK".
13. Right click the "Security Tools" folder.
14. Select "Copy".
15. In the "File Explorer" window, select "This PC".
16. Select "Windows (C:)".
17. Right click the white space of the file explorer window.
18. Select "Paste".

#### Installing Anti-Virus Agent

1. Complete the steps documented under the section [Providing the Developers Access to the Infrastructure](#Providing-the-Developers-Access-to-the-Infrastructure).
2. In your "Remote Desktop Connection" window for the Virtual Machine, click the windows icon on the taskbar.
3. Type "File Explorer".
4. Select the "Desktop app" icon.
5. In the "File Explorer" window, left click the white space of the address bar.
6. Type the location of our Anti-Virus Agent folder. For example, "c:\Temp\Security Tools\McAfee".
7. To the right of the address bar, click on the "$\to$" symbol.
8. Run the executable "FramePkgXX.exe" by double clicking it. For example: the file name could be "FramePkg50.exe".
9. After being prompted "Do you want to allow this app from an unknown publisher to make changes to your device?" click "Yes".
10. Wait for the Windows Install to complete.
11. Select "OK".

#### Installing SIEM Agent

1. Complete the steps documented under the section [Providing the Developers Access to the Infrastructure](#Providing-the-Developers-Access-to-the-Infrastructure).
2. In your "Remote Desktop Connection" window for the Virtual Machine, click the windows icon on the taskbar.
3. Type "File Explorer".
4. Select the "Desktop app" icon.
5. In the "File Explorer" window, left click the white space of the address bar.
6. Type the location of our Security Tools folder. For example, "c:\Temp\Security Tools\".
7. To the right of the address bar, click on the "$\to$" symbol.
8. Run the executable "SplunkForwarder-X.X.X.msi" by double clicking it. For example: the file name could be "SplunkForwarder-8.0.0.msi".
9. Wait for the Windows Install to display a "Splunk" banner.
10. In the field asking you to "Accept the license agreement" select the box to accept it.
11. Select "Next".
12. In the "Username" field, enter the standard Splunk administrator account username.
13. In the "Password" field, enter the standard Splunk administrator account password.
14. In the "Confirm password" field, re-enter the standard Splunk administrator account password.
15. Select "Next".
16. In the "Hostname or IP" field, enter the IP of the deployment server.
17. In the field next to it, enter the default port of "8089".
18. Select "Next".
19. In the "Hostname or IP" field, enter the IP of the receiving indexer.
20. In the field next to it, enter the default port of "9997".
21. Select "Next".
22. Select "Install".
23. After the install completes, select "Finish".

#### Applying STIG Rules Not Implemented Through GPOs

1. Complete the steps documented under the section [Providing the Developers Access to the Infrastructure](#Providing-the-Developers-Access-to-the-Infrastructure).
2. In your "Remote Desktop Connection" window for the Virtual Machine, click the windows icon on the taskbar.
3. Type "File Explorer".
4. Select the "Desktop app" icon.
5. In the "File Explorer" window, left click the white space of the address bar.
6. Type the location of our Security Tools folder. For example, "c:\Temp\Security Tools\scc-5.4.2_Windows\".
7. To the right of the address bar, click on the "$\to$" symbol.
8. Run the executable "SCC_X.X.X_Windows_Setup.exe" by double clicking it. For example: the file name could be "SCC_1.0.0_Windows_Setup.exe".
9. When prompted "Do you want to allow this app from an unknown publisher to make changes to your device?" click "Yes".
10. Select "I accept the agreement".
11. Select "Next".
12. Select "Next".
13. Select "Next".
14. Select "Next".
15. Select "Create a desktop shortcut".
16. Select "Next".
17. Select "Install".
18. After the install completes, select "Finish".
19. In your "Remote Desktop Connection" window for the Virtual Machine, click the windows icon on the taskbar.
20. Type "SCAP".
21. Select the "Desktop app" icon.
22. When prompted "Do you want to allow this app from an unknown publisher to make changes to your device?" select "Yes".
23. Select the checkbox next to "Windows" to disable all SCAP checks.
24. Select the checkbox next to "Windows_Server_2019_STIG" to enable that checklist only.
25. Select "Start Scan".
26. Once the scan is completed, select "View Results".
27. Select "Non-Compliance".
28. Review the Score, if it's over 95% go to step 38. Otherwise go to step 29.
29. Review the failed check and determine if the "Fix text" can be configured without hindering the installed applications capability. If it can, go to step 31. Otherwise, go to step 30.
30. Log the Rule ID of the check in a temporary file at "c:\Temp\Security Tools\Impossible Rules.txt" and go to step 32.
31. Perform the Fix Text for the failed check.
32. If there are un-reviewed checks repeat Step 29 to 32. Otherwise, go to step 33.
33. Right click the top of the report window.
34. Select "Close".
35. Right click the top of the SCAP window.
36. Select "Close".
37. Repeat Steps 19 to 37.
38. Right click the top of the report window.
39. Select "Close".
40. Right click on "Non-Compliance".
41. Select "Show in Directory".
42. Right click the smaller sized file titled "COMPUTERNAME_APP VERSION_SCAN DATE_SCAN TIMESTAMP_Non-Compliance_Windows_Server_2019_STIG-STIG VERSION".
43. Select "Copy".
44. In your "Remote Desktop Connection" window for the Virtual Machine, click the windows icon on the taskbar.
45. Type "File Explorer".
46. Select the "Desktop app" icon.
47. In the "File Explorer" window, left click the white space of the address bar.
48. Type the share location holding your "Security Tools" folder. For example, "\\\Share-Drive\C$".
49. To the right of the address bar, click on the "$\to$" symbol.
50. In the "Windows Security" window, select the "User name" field.
51. Enter your tier 0 account username with the full domain name.
52. Select the "Password" field.
53. Enter your tier 0 account password.
54. Select "OK".
55. Left click the "App Security Reports" folder.
56. Right click the white space of the file explorer window.
57. Select "New >".
58. Select "Folder".
59. Name the folder "(manufacturer name) (application name) Security Report". For example "Deltek Cobra Security Report".
60. Double click the folder you created.
61. Right click the white space.
62. Select "Paste".
63. Review the contents of "c:\Temp\Security Tools\Impossible Rules.txt". If the file exists and there are Rule IDs logged, go to step 64. Otherwise, you have completed this portion of the guide.
64. Minimize your "Remote Desktop Connection" window for the Virtual Machine.
65. Minimize your "Remote Desktop Connection" window for the IA Bastion Host.
66. Compose a new email to the Windows Information Assurance Team.
67. Include the following recipients in the CC, Scrum Master of Software team, Program Manager for Azure Engineering, Ops Team.
68. In the Subject field, enter the subject formatted as "STIG Requirements we cant apply to (manufacturer name) (application name)". For example "STIG Requirements we cant apply to Deltek Cobra".
69. In the Body field, enter the body formatted like the code block below.

```text
We were unable to apply the following STIG checks to (manufacturer name) (application name) Virtual machines.
(Rule ID)
(Rule ID)
(Rule ID)

(Rule ID) is unable to be applied because (describe why it cant be applied).
This is supported in the softwares documentation here (Quote and cite the documentation).

(repeat for all rules)

Requesting assistance evaluating these rules and determining the next steps.

your email signature
```

70. Send the Email.
71. Work with the Microsoft Security team until they state that the application is as secured.
72. Include all configuration changes implemented with the Microsoft Security team in the softwares CONOPS.

#### Remediating Vulnerabilities Detected by ACAS

1. Complete the steps documented under the section [Connecting to the Environment IA Bastion Host to Manage a Virtual Machine](#Connecting-to-the-Environment-IA-Bastion-Host-to-Manage-a-Virtual-Machine).
2. Open a web browser on your desktop in your IA Bastion Host.
3. Use the address bar to search for your environment's ACAS server.
4. In the Username field, enter your ACAS username.
5. In the Password field, enter your ACAS password.
6. Select "Sign In".
7. Select "Assets".
8. Select "+ Add".
9. Select "Static IP List".
10. In the Name field, enter the name formatted as "(manufacturer name) (application name)". For example "Deltek Cobra".
11. In the IP Addresses field, enter the IP addresses of all of your Virtual Machines separated by a comma and space. For example: "10.0.0.1, 10.0.0.2, etc...".
12. Select "Submit".
13. Select "Scans".
14. Select "Active Scans".
15. Select "+ Add".
16. In the Name field, enter the name formatted as "(manufacturer name) (application name) Vulnerability Scan". For example "Deltek Cobra Vulnerability Scan".
17. In the Policy field, set it as "BM Domain Joined Vulnerability Scan".
18. Select "Settings".
19. Select "Import Repository".
20. Select "Collaboration Vnet Vulnerability Repository".
21. Select "Targets".
22. In the Target Type field, select "Assets".
23. In the Assets field, select the asset you made earlier.
24. Select "Credentials".
25. Select "Add Credential".
26. Select "Nothing Selected".
27. Select "Windows".
28. Select "No items selected".
29. Select "PJH - T1".
30. Select the check icon.
31. Select "Submit".
32. In the same row as your scan, select the play button.
33. Select "Scans".
34. Select "Scan Results".
35. Once your scan has the status of "Completed", select your scans name.
36. Select "Options".
37. Select "Export as CSV".
38. Select "Select All".
39. Select "Submit".
40. Click the windows icon on your desktop in your IA Bastion Host.
41. Type "File Explorer" and click on the desktop app icon.
42. Select "Downloads".
43. Right click the CSV you exported.
44. Select "Rename".
45. Name the file "(manufacturer name) (application name) ((If only one VM is scanned)Architected VM Name) ACAS Scan". For example: "Deltek Cobra DelCobHost01 ACAS Scan".
46. Left click the white space of the folder.
47. Right click the CSV you exported.
48. Select "Copy".
49. In the "File Explorer" window, left click the white space of the address bar.
50. Type the share location holding your "Security Tools" folder. For example, "\\\Share-Drive\C$".
51. To the right of the address bar, click on the "$\to$" symbol.
52. In the "Windows Security" window, select the "User name" field.
53. Enter your tier 0 account username with the full domain name.
54. Select the "Password" field.
55. Enter your tier 0 account password.
56. Select "OK".
57. Left click the "App Security Reports" folder.
58. Left click the "(manufacturer name) (application name) Security Report" folder. For example "Deltek Cobra Security Report".
59. Right click the white space of the file explorer window.
60. Right click the white space.
61. Select "Paste".

### Testing the Application after Hardening

1. Open a web browser.
2. Left click on the white space of the address bar.
3. Navigate to the Azure Dev Ops web app by typing its URL (<https://dev.azure.com/>).
4. When you are prompted to "Sign In", select the "Email Address" field.
5. Enter the email address of the account that is connected to the Microsoft organization.
6. When you are prompted to "Enter password", select the "Password" field.
7. Enter the password of the account that is connected to the Microsoft organization.
8. Click "Submit".
9. Select your project in ADO.
10. Expand the sidebar by selecting the ">>" symbol in the bottom left.
11. Hover over "Test Plans".
12. Select "Test plans".
13. Select "All".
14. Select the mission your application is assigned to.
15. Expand "Above Baseline Software" by selecting the ">" symbol next to it.
16. Select the name of the application you are testing.
17. Select the "Test Points" for the "Hardened OU" by selecting the boxes next to the "Test Points" titles that contain "Hardened OU".
18. Select "Run for web application".
19. In the "Runner - Test Plans" window complete the instructions provided.
20. If the "EXPECTED RESULT" is the same as what happened when you completed the instructions provided, go to step 21. Otherwise go to step 26.
21. Select "&#10004;".
22. If there are additional instructions, go to step 19. Otherwise go to step 23.
23. If the "next>" option is greyed out, go to step 33. Otherwise go to step 24.
24. Select "next>".
25. Go to step 19.
26. Select "X".
27. Select the "COMMENT" box.
28. Type what happened.
29. If there are additional instructions, go to step 19. Otherwise go to step 30.
30. If the "next>" option is greyed out, go to step 33. Otherwise go to step 31.
31. Select "next>".
32. Go to step 19.
33. Select "Save and close".
34. Notify the application deployment team the "Hardened OU Test Plan" has been completed for the application.

## Submitting Application to the Information Assurance Team for Review

1. Complete the steps documented under the section [Connecting to the Environment IA Bastion Host to Manage a Virtual Machine](#Connecting-to-the-Environment-IA-Bastion-Host-to-Manage-a-Virtual-Machine).
2. In your "Remote Desktop Connection" window for the IA Bastion Host, click the windows icon on the taskbar.
3. Type "File Explorer".
4. Select the "Desktop app" icon.
5. In the "File Explorer" window, left click the white space of the address bar.
6. Type the share location holding your "Security Tools" folder. For example, "\\\Share-Drive\C$".
7. To the right of the address bar, click on the "$\to$" symbol.
8. In the "Windows Security" window, select the "User name" field.
9. Enter your tier 0 account username with the full domain name.
10. Select the "Password" field.
11. Enter your tier 0 account password.
12. Select "OK".
13. Left click the "App Security Reports" folder.
14. Right click the folder named "(manufacturer name) (application name) Security Report". For example "Deltek Cobra Security Report".
15. Select "Copy".
16. Minimize your "Remote Desktop Connection" window for the IA Bastion Host.
17. Paste the folder to your local device.
18. Left click on the "(manufacturer name) (application name) Security Report" folder.
19. Left click to open the "COMPUTERNAME_APP VERSION_SCAN DATE_SCAN TIMESTAMP_Non-Compliance_Windows_Server_2019_STIG-STIG VERSION.html" file.
20. In your browser window, left click on the icon for additional options.
21. Select Print.
22. Set your destination as "Save as PDF".
23. Select Save.
24. Select Save or OK in your file managers window.
25. Right click on your "COMPUTERNAME_APP VERSION_SCAN DATE_SCAN TIMESTAMP_Non-Compliance_Windows_Server_2019_STIG-STIG VERSION.html" file.
26. Select "Move to trash" or "Delete".
27. Compose a new email to the App Developer and App Team Lead.
28. Include the following recipients in the CC, AFS Blue Mountain MCS Manager, Program Manager for Azure Engineering, Ops Team, Scrum Master of Software team.
29. Attach the contents of the "(manufacturer name) (application name) Security Report" folder.
30. In the Subject field, enter the subject formatted as "Security Report for (manufacturer name) (application name)". For example "Security Report for Deltek Cobra".
31. In the Body field, enter the body formatted like the code block below.

```text
McAfee Installed
Splunk Installed
SCAP Scan attached (95%)
Security Center scan attached

Please let me know if there’s anything else I can provide

your email signature
```

32. Send the Email.
33. Right click the folder "(manufacturer name) (application name) Security Report".
34. Select "Delete".
35. Monitor for communications from the team on any actions they may need you to perform to get the app approved by security.

## Replicating the Application in Production

### Obtaining the Application Deployment Files

1. Contact the development team on how to obtain the application deployment files.
2. Follow the instructions from the development team to obtain the application deployment files.
3. Move the files to a non-ephemeral location on your local computer.
4. Rename the folder holding the application deployment files to "YearMonthDayDate-DTA-Initials of Requestor". For example: "20220421-DTA-KKL".

### Uploading the Application Deployment Files to Production

1. Open a web browser.
2. Navigate to the platform preferred by the production environment's security team to upload your files. If that is Microsoft Teams, go to step 4, otherwise, go to step 3.
3. Request instruction from the production environment's security team on how to access their preferred platform.
4. Left click on the white space of the address bar.
5. Navigate to the Microsoft Teams web app by typing its URL (<https://teams.microsoft.com/>).
6. When you are prompted to "Sign In", select the "Email Address" field.
7. Enter the email address of the account that is connected to the Microsoft organization.
8. When you are prompted to "Enter password", select the "Password" field.
9. Enter the password of the account that is connected to the Microsoft organization.
10. Click "Submit".
11. Select "Teams".
12. Ensure that you are in the Blue Mountain team in Microsoft Teams. If you do not see the Blue Mountain team, go to step 13. Otherwise, go to step 14.
13. Contact your manager to grant you access to the Blue Mountain team in Microsoft Teams.
14. Select the "General" channel for your Blue Mountain team.
15. Select "Files" on the upper portion of the window.
16. Select "L2H DTA - Move Requests".
17. In the Microsoft Teams webpage, select "Upload".
18. Select "Folder".
19. Select the folder named "YearMonthDayDate-DTA-Initials of Requestor" on your local computer.
20. Select the "General" channel for your team.
21. Select "New conversation".
22. Type "@(name of data transfer agent) The (manufacturer name) (application name) install files are in the DTA folder under "YearMonthDayDate-DTA-Initials of Requestor". For example: "@(name of data transfer agent) The Deltek Cobra install files are in the DTA folder under "20220517-DTA-BRT".
23. Wait for notification the files have been moved to the production environment.

### Deploying the Application in Production

1. Coordinate with the production environment's administrators to have the folder "YearMonthDayDate-DTA-Initials of Requestor" copied to a location that you can access from the application virtual machines.
2. Open the "installation-guide.md" file located in the "YearMonthDayDate-DTA-Initials of Requestor" folder.
3. Execute the steps detailed in the "installation-guide.md" file provided for the application.
4. Open the "(app)TestPlan.md" file located in the "YearMonthDayDate-DTA-Initials of Requestor" folder.
5. Execute the steps detailed in the "(app)TestPlan.md" file provided for the application.
6. Instruct the production environment's administrators on how the users can interface with the application.
