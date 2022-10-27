# Getting Started
Deploying Azure Web Apps can be done in numerous ways.  This document will cover the manual methods of deploying via Azure CLI.

---
# Dependencies
1. [Nodejs](https://nodejs.org/en/)
    - After installation, run the following in an **elevated** command command prompt:
```bash
npm config set prefix 'C:\Program Files\nodejs'
```
2. [Git](https://git-scm.com/download/win)
3. [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli)

---
# Building Project

1. Clone project repository or download as zip
2. Run the following commands (in extracted folder):

```bash
npm install
npm run build
```
---
# Deployment
##Authentication
To deploy, the user must first authenticate into their environment:
```bash
az cloud set --name AzureUSGovernment
az login --service-principal --username $appId --tenant $tenantId --password $password
```
> Note: 
>  $appId and $password will be stored in your subscriptions keyvault secrets. $tenantId can be obtained in various ways: [How to find your Azure Active Directory tenant ID](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)

## Publishing code

### Azure CLI Zip Deployment
Zip deployment requires that you zip your built Azure Function App and then run the following command against that zip file location:
```bash
az webapp deployment source config-zip -g <resource_group> -n <app_name> --src <zip_file_path>
```

> **References**
> [az webapp deployment source](https://docs.microsoft.com/en-us/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip)