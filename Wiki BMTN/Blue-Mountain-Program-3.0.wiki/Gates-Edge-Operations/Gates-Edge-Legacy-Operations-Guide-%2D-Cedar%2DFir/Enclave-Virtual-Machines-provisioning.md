[[_TOC_]]

##Create Bastion Host Virtual Machine
1. 	Create Virtual Machine in the enclave based on Windows Server standard marketplace image.
Open _rg-enclave-mission2_ Resource Group, click Add, search for _Windows 2019 Server_, choose “Windows Server 2019 Datacenter - BYOL use only” and click **Create**.
![6_Virtual_Machine_1.png](/.attachments/6_Virtual_Machine_1-2fdb69be-bae4-481e-8ba5-c9a321190716.png)

2.	Populate required properties, similar to information below, ensure VM is provisioned with a **public IP** and leave the remaining settings to its default values. Make sure inbound RDP port 3389 is left open:
a.	Name: vm-bh-mission2
b.	Size: DS2_v2
c.	Subnet: missionenclave
d.	Administrator account: 
	-	Username: bastionadmin
	-	Password: choose a strong password like v#r6Str0ngPwd

3.	Click **Review + Create**, then click **Create**.
![6_Virtual_Machine_2.png](/.attachments/6_Virtual_Machine_2-6cb6f778-b676-4e9e-bfbd-50d0fd2c1ce3.png)

##Create Active Directory Domain Controllers Virtual Machines
Create Virtual Machine to host **primary domain controller** in a new forest in the enclave based on Windows Server standard marketplace image.

1.	Open _rg-enclave-mission2_ Resource Group, click Add, search for _Windows 2019 Server_, choose “Windows Server 2019 Datacenter - BYOL use only”, click Create.
![6_Virtual_Machine_3.png](/.attachments/6_Virtual_Machine_3-dbd82c5e-1492-41ab-9527-7fa114dd871e.png)

2.	Populate required properties, identified below, make sure no public IP gets provisioned with VM and keep all other default values. Click the **Review + Create** button, verify all configurations are correct and click the **Create** button.
a.	Name: _vm-ad-mission2-01_
b.	Size: DS2_v2
c.	Subnet: _missionenclave_ - as created earlier in this document
d.	Administrator account: 
	-	Username: for example: _adadminmission2_
	-	Password: choose a strong password: Example: v#r6Str0ngPwd

3.	Create Virtual Machine to host the **second domain controller** in the enclave based on Windows Server standard marketplace image. Open the same rg-enclave-mission2 Resource Group, click Add, search for _Windows 2019 Server_, choose “Windows Server 2019 Datacenter - BYOL use only”, click the **Create** button.
![6_Virtual_Machine_4.png](/.attachments/6_Virtual_Machine_4-38299b08-f7a6-49d1-baff-b19d2565354d.png)

4.	Create Virtual Machine to host the second domain controller in the enclave based on Windows Server standard marketplace image. Open the same rg-enclave-mission2 Resource Group, click Add, search for Windows 2019 Server, choose “Windows Server 2019 Datacenter - BYOL use only”, click the Create button.
a.	Name: _vm-ad-mission2-02_
b.	Size: DS2_v2
c.	Subnet: _missionenclave_
d.	Administrator account: 
	-	Username: for example: _adadminmission2_ (this example is using the same account as for first DC VM)
	-	Password: choose a strong password like v#r6Str0ngPwd
![6_Virtual_Machine_5.png](/.attachments/6_Virtual_Machine_5-4e17e67b-4dcb-4450-af7b-2a754672819f.png)

5.	Click **Review + Create**, then **Create**.

6.	Once virtual machines are provisioned, review the resulting resources in Subscription resources view.
