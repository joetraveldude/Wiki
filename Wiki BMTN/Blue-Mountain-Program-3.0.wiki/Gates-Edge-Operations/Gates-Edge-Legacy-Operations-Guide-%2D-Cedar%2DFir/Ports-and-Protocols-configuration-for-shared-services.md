[[_TOC_]]

The following services deployed in Gates Core need to be able to communicate with mission enclave:
-	Active Directory synchronization in one-way trust configuration between enclave AD and Gates Core AD
-	Antivirus: one-way connectivity between local agent components deployed on enclave VMs with EPO server (McAfee) hosted in Gates Core.
-	ACAS: one-way connectivity between Tenable Nessus server hosted within Gates Core to mission enclave VMs
-	Splunk: one-way connectivity between local agent components on mission enclave VMs with Splunk server deployed in Gates Core
-	WSUS: one-way connectivity between mission enclave Windows VMs with WSUS server deployed in Gates Core.

The sections below list ports / protocols connectivity requirements per specific product.

**_Note_**: More detailed instructions related to 3rd party products like Splunk, McAfee, Tenable Nessus needs to be followed on ISV site using links in corresponding References section as a starting point.

##Active Directory synchronization
The following network requirements around opening relevant ports to allow connectivity between primary Gates Edge AD and mission enclave AD configured in one-way trust configuration need to be implemented via subnet NSG configuration from both sides. 

The port / protocol requirements below correspond to Active Directory in Windows Server 2008 and later versions:


| **Client Port(s)** | **Server Port** | **Service** |
|--|--|--|
| 49152-65535/UDP | 123/UDP | W32Time |
| 49152-65535/TCP | 135/TCP | RPC Endpoint Mapper |
| 49152-65535/TCP | 464/TCP/UDP | Kerberos password change |
| 49152-65535/TCP | 49152-65535/TCP | RPC for LSA, SAM, NetLogon (*) |
| 49152-65535/TCP/UDP | 389/TCP/UDP | LDAP |
| 49152-65535/TCP | 636/TCP | LDAP SSL |
| 49152-65535/TCP | 3268/TCP | LDAP GC |
| 49152-65535/TCP | 3269/TCP | LDAP GC SSL |
| 53, 49152-65535/TCP/UDP | 53/TCP/UDP | DNS |
| 49152-65535/TCP | 49152-65535/TCP | FRS RPC (*) |
| 49152-65535/TCP/UDP | 88/TCP/UDP | Kerberos |
| 49152-65535/TCP/UDP | 445/TCP | SMB (**) |
| 49152-65535/TCP | 49152-65535/TCP | DFSR RPC (*) |

References:
1.	Windows Server 2012 Active Directory Read-Only Domain Controller configuration: https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/rodc/install-a-windows-server-2012-active-directory-read-only-domain-controller--rodc---level-200-
2.	How to configure a firewall for Active Directory domains and trusts: https://docs.microsoft.com/en-US/troubleshoot/windows-server/identity/config-firewall-for-ad-domains-and-trusts 

##Antivirus Services
The following network requirements around opening relevant ports to allow connectivity between ePolicy Orchestrator agent in mission enclave and ePolicy Orchestrator server in Gates Edge Core virtual network subnet.

Traffic direction qualifier definition:
-	Bidirectional - A connection is initiated from either direction.
-	Inbound - Connection initiated by a system remote to ePO server.
-	Outbound - Connection initiated by the local ePO server system.


| **Port** | **Description** | **Direction** |
|--|--|--|
| 80 | Agent-server communication port. | TCP port that the ePO server service uses to receive requests from agents.	Inbound connection to the Agent Handler and the ePO server from the McAfee Agent. |
| 135 | File sharing and server message block (SMB) | Outbound. Port 135 needs to be open to deploy the McAfee Agent from the System Tree. The following ports are associated with file sharing and server message block (SMB) communications: 1. Microsoft file sharing SMB 2. User Datagram Protocol (UDP) 3. Ports from 135 through 139. 4. Transmission Control Protocol (TCP) ports from 135 through 139. 5. Direct-hosted SMB traffic without a network basic input/output system (NetBIOS): port 445 (TCP and UPD) |
| 443 | Agent-server communication secure port | TCP port that the ePO server service uses to receive requests from agents and Remote Agent Handlers. Inbound connection to the Agent Handler and the ePO server from the McAfee Agent. Inbound connection to the ePO server from the Remote Agent Handler. |
| 8081 | Agent wake-up communication port. SuperAgent repository port. | TCP port that agents use to receive agent wake-up requests from the ePO server or Agent Handler. TCP port that the SuperAgents configured as repositories that are used to receive content from the ePO server during repository replication, and to serve content to client systems. Inbound connection from the ePO server or Agent Handler to the McAfee Agent. Inbound connection from client systems to SuperAgents configured as repositories. |
| 8082 | Agent broadcast communication port | UDP port that the SuperAgents use to forward messages from the ePO server/Agent Handler. Outbound connection from the SuperAgents to other McAfee Agent. |
| 8083 | RelayServer discovery for version 4.8 agents | UDP port McAfee Agents use |

References
1.	ePolicy Orchestrator port requirements for firewall traffic: https://kc.mcafee.com/corporate/index?page=content&id=KB66797 

##ACAS: Tenable Nessus
Tenable Nessus product requires full connectivity from Tenable Nessus server deployed in Gates Core to the subnet within mission enclave virtual network where VMs are deployed. 
As such, NSG configuration from both sides should reflect the following settings:
**Source**: Tenable Nessus IP address
**Target**: Mission Enclave virtual network subnet CIDR mask as defined in enclave infrastructure provisioning step
**Type**: All ports / protocols (TCP, UDP)

##Splunk
The following network requirements around opening relevant ports to allow connectivity between Splunk forwarders deployed on mission enclave VMs and Gates Edge Core-hosted Splunk server need to be implemented via subnet NSG configuration from both sides. 


| **Port** | **Description** |
|--|--|
| 9997 /TCP | Forwarders sending data to Splunk Server |
| 8000 /TCP | Clients accessing Splunk Search page |

References:
1.	[Components and their relationship with the network](https://docs.splunk.com/Documentation/Splunk/7.2.4/InheritedDeployment/Ports?_gl=1*1xt2j2a*_ga*MTIxNjMzMjM5Ni4xNjU2MzQ1NTUz*_gid*MTc0OTMyNjM4OC4xNjU2MzQ1NTUz&_ga=2.44617060.1749326388.1656345553-1216332396.1656345553#Components_and_their_relationship_with_the_network) 

##WSUS
The following network requirements around opening relevant ports to allow connectivity between VMs in mission enclave and WSUS server in Gates Edge Core virtual network subnet.

VMs in mission enclave must have outbound access to 8530 and 8531 ports on the WSUS server.

References:
Deploy Windows Server Update Services: Configure: https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/2-configure-wsus 