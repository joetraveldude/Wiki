# Getting Started
Deploying Azure Functions can be done in numerous ways.  This document will cover the manual methods of deploying via command line.  

---
# Dependencies
1. [Nodejs](https://nodejs.org/en/)
    - After installation, run the following in an **elevated** command command prompt:
```bash
npm config set prefix 'C:\Program Files\nodejs'
npm install azure-functions-core-tools@3 -g --unsafe-perm true
```
2. [Git](https://git-scm.com/download/win)
3. [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli)
4. [Azure Function Configurations](https://dev.azure.com/bmpo9/ACAI-bmpo9/_wiki/wikis/ACAI-bmpo9.wiki/10/Azure-Function-Configurations)

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
There are two options for publishing your code, Azure Function Core Tools and Zip Deployment.
### Azure Function Core Tools
Run the following command in your repository after authenticating to deploy your project.: 
```bash
func azure functionapp publish <app_name> --typescript
```

### Zip Deployment
Zip deployment requires that you zip your built Azure Function App and then run the following command against that zip file location:
```bash
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

> **References**
> [Work with Azure Functions Core Tools](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local)
> [Zip deployment for Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/deployment-zip-push)