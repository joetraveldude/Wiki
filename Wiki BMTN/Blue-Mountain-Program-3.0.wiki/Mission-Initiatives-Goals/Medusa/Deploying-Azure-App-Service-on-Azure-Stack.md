# Getting Started
Azure App Service on Azure Stack requires various prerequisites to be performed before deployment.  The following resources will help developers understand the process and requirements:

[Azure App Service and Azure Functions on Azure Stack Hub overview](https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-app-service-overview?view=azs-2102)

[Prerequisites for deploying App Service on Azure Stack Hub](https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-app-service-before-you-get-started?view=azs-2102&pivots=state-connected)

[Deploy App Service in Azure Stack Hub](https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-app-service-deploy?view=azs-2102&pivots=state-connected)

# Limitations
The current Azure Function runtime used in proof of concept is v3.x.  This supports languages such as TypeScript, JavaScript, as well as PowerShell.  Azure Stack hub seems to have a limitation of v1.x:

![image.png](/.attachments/image-3b461cf7-d666-4c12-89cb-7c1a92c20ed6.png)
[Source](https://docs.microsoft.com/en-us/azure/azure-functions/functions-versions?tabs=csharp%2Cv4#languages)

# Workarounds
Deploying Azure Functions to Docker containers could be a potential workaround for Azure Functions Runtime limitations.  
[Docker](https://docs.docker.com/cloud/aci-integration/)
[Create a function on Linux using a custom container](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-function-linux-custom-image?tabs=in-process%2Cbash%2Cazure-cli&pivots=programming-language-typescript)