[[_TOC_]]

# Intake
- [ ] Is Open Source?
- [ ] Has Foreign Ownership, Control, or Interest?
- [ ] License Quantity
- [ ] User Quantity
- [ ] Specific Product identified (what sku do we procure)
- [ ] Target Prod environment
- [ ] Use Description

# CONOPS
- [ ] Diagram
- [ ] PPS
- [ ] App Category
- [ ] Summary
- [ ] Vendor Info
- [ ] Research App Requirements
- [ ] 

# Install Guide
- [ ] Prerequisites
- [ ] AD changes needed (service accounts, security groups, etc)
- [ ] Consistent naming convention
- [ ] Utilize KV in hyperscale for secrets
- [ ] Services that need to be running for proper app operation

# Install in Development Environment
- [ ] Test Locally (optional)
- [ ] GPOs
- [ ] STIGs
- [ ] AD Integration
- [ ] Remote App
- [ ] Web App (HTTPs)
- [ ] 

# CR Submit
- [ ] CONOPS
- [ ] Install Guide
- [ ] security tools we will use on RX work properly on FF before approval, good vuln scan, hbss installed and working, VM reporting to Log A, defender for cloud okay
- [ ] Check if windows updates are available on local VMs. If so, install
- [ ] 
- [ ] 
- [ ] 

# Enclave Deployment Validation
- [ ] VMs can connect to Key Vault
- [ ] VMs can connect to Storage Account
- [ ] vPAW can access KV and SA
- [ ] Bastion available and subnet created
- [ ] Administrator Group includes ABS Team Group
- [ ] Storage Account Exist?
- [ ] Domain join account allows VMs to join domain
- [ ] KV Secret: 
- [ ] 


# Enclave Jumpbox/Storage Account
- [ ] NSGs (different from other VMs?)
- [ ] Domain Joined?
- [ ] Azure CLI
- [ ] Bicep CLI
- [ ] Powershell (ISE or v7+ ?)
- [ ] MECM?
- [ ] STIG Viewer?
- [ ] Power STIG?
- [ ] Storage Explorer
- [ ] 

# Hardening
- [ ] McAfee Agent Installed
- [ ] Nessus Agent Installed
- [ ] Moved to "Services" OU for appropriate enclave
- [ ] Windows Updates Applied
- [ ] Save Nessus Scans to Repo as html (request scan from Andy Serfass)

# Code/ PR Review
- [ ] Review PR
- [ ] Schedule 30 min for app review with ABS Leads (Andy, Junaid, Lily)

# Package Complete
- [ ] CONOPS Checklist Complete
- [ ] Install Guide Checklist Complete
- [ ] Application Installer (exe/msi/etc)
- [ ] PS ADT scripts for installation
- [ ] Up-to-date infra folder
- [ ] License?
- [ ] Nessus html scan

# AFT/DTA (need more info on this)
- [ ] Download from repo
- [ ] Download of any large installer files outside of repo (storage account etc)
- [ ] Send notice to person deploying app (CC deployment team)

# Production
- [ ] Request AD Changes
- [ ] Apply GPOs
- [ ] 
- [ ] 