# Getting Started
Deploying Azure resources can be done in numerous ways.  This document will cover the Infrastructure as Code (IaC) deployment of data fusion environment using PowerShell with Azure Resource Manager (ARM) templates.

---
# Prerequisites
## Azure PowerShell module
[Azure PowerShell module](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-6.6.0) needs to be installed  

## Pre-deployed Azure Resources
The following resources must be pre-deployed prior to deploying the datafusion environment:
1. Resource Group
2. Virtual Network
3. Subnets
---

# Deployment
##Authentication
To deploy, the user must first authenticate into their environment:
```PowerShell
Connect-AzAccount -Environment "AzureUSGovernment" -Subscription <Subscription Name>
```
> Note: 
>  This deployment assumes the cloud environment is Azure Government. To deploy to another cloud environment, replace the Environment parameter "AzureUSGovernment" with another cloud environment. For more details, refer to [Connect-AzAccount](https://docs.microsoft.com/en-us/powershell/module/az.accounts/connect-azaccount?view=azps-6.6.0)

## Deploying IaC

### Azure PowerShell Deployment
Navigate to the folder src/Infrastructure/iac of the ac-data repo and execute the [deploy_to_resource_group.ps1](https://dev.azure.com/bmpo9/ACAI-bmpo9/_git/ac-data?path=/src/Infrastructure/iac/deploy_to_resource_group.ps1) PowerShell script
```PowerShell
deploy_to_resource_group.ps1 -env <environment> -rg <resource group> 
# Example: deploy_to_resource_group.ps1 -env 'C1 Test' -rg 'DATAFUSION-RG-01'
```

#### References
[Deploy resources with ARM templates and Azure PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-powershell)