The following ACR sample model is provided as an example, using Matlab application deployment in a virtual enclave:

[[_TOC_]]

# MatLab Sizing and ACR
## Mathlab Desktop Client: 

### Reference: https://www.mathworks.com/support/requirements/matlab-system-requirements.html

### Resources requirement:
 -vCores: 4 
 -RAM: 8GB
 -Disk: 32GB SSD
 -GPU: 1GB RAM

### Azure VM Size matching:
- General Purpose:
 - VM Series: Ddv5 
   Reference: https://docs.microsoft.com/en-us/azure/virtual-machines/dv5-dsv5-series
 - VM Size: Standard_D8_v5 D8v5
  or
- GPU Optimized:
 - VM Series:NCv3-series and NC T4_v3-series
   Reference: https://docs.microsoft.com/en-us/azure/virtual-machines/nct4-v3-series
 - Optional: NCasT4_v3-series (or Standard_NC16as_T4_v3 (NC16asT4v3))

Accounting for Virtual Desktop recommendations, a few users per session host:
 Reference: https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/virtual-machine-recs

### Per user resources:
 User type = Low (Connected but nearly dormant)
 
 User type = Medium
 Minimum requirements:
 -vCores: 2
 -RAM: 8GB
 -Disk: 32GB SSD

 User type = High
 Recommended Configuration:
 -vCores: 4
 -RAM: 16GB
 -Disk: 64GB SSD

Usage Model per session host - Example #1:
 - 10 total connected users
 - 2 concurrent heavy Matlab users

Recommended VM Size: D8dsv5

---------------------------------------
## MATLAB License Manager Requirements
### Reference: https://www.mathworks.com/support/requirements/matlab-license-manager.html
### Resources requirement:
 - vCores: 2
 - RAM: 1+GB RAM 
 - Disk: small (32GB?)

Azure VM Size matching:
- General Purpose:
 - VM Series: Standard_D2_v5
 - VM Size: D2v5

---------------------------------------
# ACR Estimate: 
Components:
 - Mathlab Client: 
   Usage Model Example #1 D8v5; P6 Premium SSD Disk;
 - Mathlab Licensing Server: D2v5; E3 Standard SSD Disk

 ### Monthly ACR: 
  - Azure Commercial (US East):     ~$425
  - Azure Government:        +25%:  ~$530
  - Azure Government Secret: +75%:  ~$750

# Jira
##Jira Client: 

Form factor: web client

Reference: https://confluence.atlassian.com/adminjiraserver/jira-applications-installation-requirements-938846826.html

###Resource requirements: web-browser

###Virtual Desktop recommendations, a few users per session host
 Reference: https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/virtual-machine-recs

Light workload: 4 vCPUs, 16 GB RAM, 32 GB storage
Azure VM Size matching:
- General Purpose:
  -VM Series: D4sv4
  -VM Size: Standard_D4_v4

----------------------------------
##Jira Web Server Requirements
Reference: https://confluence.atlassian.com/adminjiraserver/jira-applications-installation-requirements-938846826.html

###Resources requirements
 Production system:
 - vCores: 8
 - RAM: 32GB RAM 
 - Disk: 256GB

###Azure VM Size matching
 - General Purpose:
   - VM Series: Dv4
   - VM Size: Standard_D8_v4

---------------------------------------
##Jira SQL Server Requirements
Reference: 
- https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices-vm-size?view=azuresql
- https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-memory

###Resources requirements
 Production system:
 - vCores: 8
 - RAM: 64GB RAM 
 - Disk: 512GB

###Option 1: Azure VM Size matching
- Memory Optimized
  - VM Series Esv4
  - VM Size: Standard_E8s_v4	

###Option 2: Azure SQL DB  matching
- Business Critical Service Tier
- 6 vCores; 32GB DB storage
- Zone Redundant
- ZRS DB Storage 
 
---------------------------------------

##ACR Estimate (Windows & SQL OS license included): 
 - Components
   - Jira Web Server: D8v4;  P15 Premium SSD ZRS Disk;
   - Jira SQL Server: E8bdsv5; P20 Premium SSD ZRS Disk

 - Monthly ACR: 
   - Azure Commercial (US East):     ~$3600
   - Azure Government:        +25%:  ~$4500
   - Azure Government Secret: +75%:  ~$6300
