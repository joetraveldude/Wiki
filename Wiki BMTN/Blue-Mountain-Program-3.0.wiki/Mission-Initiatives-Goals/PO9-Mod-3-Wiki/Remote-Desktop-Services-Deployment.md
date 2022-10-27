##1. Introduction

This document is to be used for internal teams to help deploy a Highly Available RDS services on the SPICE deployment

##1.1	Prerequisites
•	ASH deployed (Need access to Portal)
•	Bicep
•	VSCode
•	Sidecar
•	GatesOnPOSH Repo
•	Windows Server 2019
•	Sql 2019/SQL Server Management Studio
•	Software to install on Session Hosts

##2.	Base RDS Servers Overview
•	2x – RDS Connection Broker Servers
•	2x – RDS Web Access Servers
•	3x – RDS Session Hosts
•	1x – RDS Gateway Server
•	1x – RDS License Server
•	1x – SQL Server (Hosted specifically for RDS)
•	1x – File Share (Hosted specifically for RDS)

##3.	Installation Procedures
###3.1	Deploying Infrastructure
###3.1.1	Run Deployment Code on Initiator VM
1. Place Code on Initiator VM	
2. Log into Initiator.
3. CD to code directory and run code
###3.1.2	Validate Infrastructure 
1.	Confirm all servers were deployed
2.	Confirm all servers are part of the domain
3.	Confirm all servers are in the correct OU(Tier 2) and not quarantined
3.2	RDS Configuration
3.2.1	Initial Configuration
1.	Log into the first Connection Broker server and open Server Manager.
2.	Go to Manage in the top right and Add Roles and Features
3.	Select Remote Desktop Services Installation under Installation Type
4.	Select all the servers that you will be installing any RDS role onto.
5.	Deploy a “Standard Deployment” and “Session Based Desktop Deployment”
6.	Add the first Connection Broker when asked. (We will add second during High Availability setup)
7.	Add the Web Access servers and Session Hosts when asked.
8.	Confirm deployment
###3.2.2	Session Hosts and Creating a Collection
1.	Install all software needed for the workload on the Session Hosts
a.	Microsoft Edge
b.	Microsoft Office
c.	Microsoft Visio
d.	Microsoft Project
2.	On the Connection Broker, open Server Manager, and select Remote Desktop Services
3.	Select Collections, in the top righ of the Collections box, select Tasks and “Create Session Collection”
4.	Follow the wizard and create a Collection Name.
5.	Specify the Session Hosts that you would like to have in the collection
6.	Specify the User Groups that should have access to the RDS sessions
7.	Enable User Profile Disks and route them to the RDS Share Drive (We will route the Desktop/Documents folder for Sentris through a GPO)
8. Confirm NLA is enabled 

###3.2.3	Licensing Server
1.	License Server with Device CAL
##3.3	High Availability Setup
###3.3.1	SQL Server
1.	Open portal and create a base 2019 VM
2.	Install SQL 2019 (installer located on storage account) Keep the defaults
3.	Install SQL Server Management Studio
4.	In ADUC create a security group with the Connection Brokers in them.
5.	In SQL Server Management Studio right click “Logins”
a.	Under General click search. Search for the RDS Connection Broker group and select it
b.	Under Server Roles select dbcreator
c.	Click OK to return to the main SSMS screen
###3.3.2	HA Setup on Connection Brokers
1.	Log onto the RDS Connection Broker
2.	In Server Manager select Remote Desktop Services
3.	Under Deployment, right click the RD Connection Broker and select “Configure for High Availability”
4.	Select Dedicated Database Server
5.	Configure the DNS name, the connection string and point the folder to the correct location.
6.	Click Configure 
##3.4	Validation
###3.4.1	Base Functionality
1.	Create a test user
2.	Log into the web access portal
3.	Select a collection
4.	Log into the RDP file that was downloaded
5.	Confirm connectivity and confirm all software exists
###3.4.2	Sentris Validation
1.	Utilize the Gates RDS Validation Document
