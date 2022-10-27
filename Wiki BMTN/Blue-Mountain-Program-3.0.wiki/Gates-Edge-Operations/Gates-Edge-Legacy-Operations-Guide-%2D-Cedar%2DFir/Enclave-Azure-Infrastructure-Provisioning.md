[[_TOC_]]
A separate Azure subscription is created for a distinct Mission Enclave with virtual network and single subnet hosting Mission Enclave resources like Virtual Machines as depicted on the diagram above. As such, Gates Edge Operator needs to follow steps outlined below to provision all the required Azure components.

##Create Azure Subscription for Mission Enclave Resources
1.	From the Azure Stack Hub portal, open **Subscriptions** view, click Add and choose the available offering.
![1_Subscription_1.png](/.attachments/1_Subscription_1-b4a2c4b9-f18c-4a16-acdc-e462f953e73c.png)

2.	In the subscription name field enter the chosen name like _GatesEdgeEnclaveMission2_ and click the Create button.
![1_Subscription_2.png](/.attachments/1_Subscription_2-77c2f536-1d8e-47a3-b4da-9988cd869284.png)

##Create **Resource Group** to hold Mission Enclave resources
1.	Open the newly created subscription and click the Resource Groups link under the Settings section on the left.
![2_Resource_Group_1.png](/.attachments/2_Resource_Group_1-24805d79-801a-4eb9-85f5-59c192cf76e2.png)

2.	From the Resource Groups pane, select the Add link.
![2_Resource_Group_2.png](/.attachments/2_Resource_Group_2-7ea459cc-0924-452f-afcc-e90526766dc3.png)

3.	In the **Create a resource group** pane, select the **Resource Group** field and enter the name, for example, _rg-enclave-mission2_. Ensure that the current Stack Hub deployment region like tca is selected. Next, select the **Review + create** button.
![2_Resource_Group_3.png](/.attachments/2_Resource_Group_3-9a85248f-131d-4c91-b6d6-346517376b8b.png)

4.	Once the resource group is created, verify in the **Resource group** pane that _rg-enclave-mission2_ was properly created.
![2_Resource_Group_4.png](/.attachments/2_Resource_Group_4-c0c56fc6-1fcf-4475-b823-129d30d6b40f.png)

##Create Virtual Network to host Enclave Virtual Machines
1.	Open the newly created resource group rg-enclave-mission2 and search for virtual network in the Marketplace and select create.
![3_Virtual_Machines_1.png](/.attachments/3_Virtual_Machines_1-c5c34cde-ec1d-4322-bd88-882ca25baa0a.png)

2.	In the **Create virtual network** pane, type the chosen name like _vnet-enclave-mission2_ in the virtual network name field and ensure that correct Stack Hub region is selected, i.e. tca. Then, click the **Next : IP Addresses >** button.
![3_Virtual_Machines_2.png](/.attachments/3_Virtual_Machines_2-cff0f91f-2382-47d8-9440-14d90d3b6183.png)

3.	Populate the default subnet CIDR using the next increment of enclave per **10.X.0.0/16** convention, for example, **10.1.0.0/16**. Edit the default subnet to the corresponding IP range like **10.1.0.0/24** and rename the subnet to missionenclave. Save the changes and click the **Review + Create** button.
![3_Virtual_Machines_3.png](/.attachments/3_Virtual_Machines_3-2848f223-8764-4c99-9aca-eea4a6fd67ee.png)

4.	Then select the **Create** button.
![3_Virtual_Machines_4.png](/.attachments/3_Virtual_Machines_4-4c980f4a-9f2f-41e0-9274-2147918ef5b5.png)

##Create Virtual Network Gateways (Optional Step – skip if VNET peering approach is allowed)
Create Virtual Network Gateways to connect the enclave Virtual Network to Gates Core hub Virtual Network in the following subsequent steps.

1.	Type virtual network gateways in the search bar and select Virtual network gateways. Once in the Virtual Network Gateways pane, click Add:
![4_Virtual_Network_Gateway_1.png](/.attachments/4_Virtual_Network_Gateway_1-e49cd5cf-5e2b-4b27-8210-aab215145df7.png)

2.	In the Create virtual network gateway pane, populate the gateway info per below figure, selecting vnet-enclave-mission2 in the virtual network field and populating the below information in the corresponding fields:
a.	Name: _gw-enclave-mission2_
b.	Region: tca
c.	SKU: High performance
d.	Gateway subnet address range: 10.1.1.0/24
e.	Public IP: ip-gw-enclave-mission2

3.	Click the **Review + Create** button.
![4_Virtual_Network_Gateway_2.png](/.attachments/4_Virtual_Network_Gateway_2-ca0dcd47-c453-4103-95f4-597b18321401.png)

4.	Verify all information is correct and click the **Create** button.
![4_Virtual_Network_Gateway_3.png](/.attachments/4_Virtual_Network_Gateway_3-0878871a-0c9d-4fbd-bcc3-8c38a6d9f94f.png)

##Create Virtual Network Peering with Gates Core VDMS and VDI Virtual Networks
Mission Enclave resources need to be able to communicate with Gates Core resources, including VDMS-hosted Active Directory – consequently, Mission Enclave Virtual Network needs to be peered with Gates Core VDMS Virtual Network per following steps:

1.	Open _vnet-enclave-mission2_ virtual network view, select Peerings, click **Add**:
![5_Vnet_Peering_4.png](/.attachments/5_Vnet_Peering_4-ea6914fc-f96e-4453-b58e-33c169f4bf12.png)

2.	In the Add Peering section, under the "This virtual network" sub-section, enter the Peering link name, example, _mission2-to-vdms_. Go to the "Traffic forwarded from remote virtual network" and select the "Block traffic that originates from outside the virtual network". 
![5_Vnet_Peering_5.png](/.attachments/5_Vnet_Peering_5-b7ac2c76-d648-4401-81d5-341703269255.png)

3.	Scroll down to the "Remote virtual network" sub-section and enter the Peering link name, example, _vdms-to-mission2_ and fill in or select the following information:
-	Subscription: BMGatesDev
-	VNET: _vnet-gates-vdms-bmt-tca_,
-	Traffic forwarded from remote virtual network: Block traffic that originates from outside the virtual network
Verify all settings and click on the Add button.
![5_Vnet_Peering_6.png](/.attachments/5_Vnet_Peering_6-e9f54508-1565-448d-9481-1fb7dffc2110.png)

4.	Follow steps 1 to 3 with the second vnet peering for _mission2-to-vdi_ and _vdi-to-mission2_
![5_Vnet_Peering_7.png](/.attachments/5_Vnet_Peering_7-780e160b-c98a-448d-aa17-0f9b4b5b5400.png)
and _vdi-to-mission2_.
![5_Vnet_Peering_8.png](/.attachments/5_Vnet_Peering_8-bee38b82-d963-4d9c-829c-886775391d08.png)

5.	Open Peerings view to examine the resulting peering info – the view should be similar to depicted screenshot:
![5_Vnet_Peering_9.png](/.attachments/5_Vnet_Peering_9-e0875928-4245-4b33-bc2b-4712e70030c9.png)

