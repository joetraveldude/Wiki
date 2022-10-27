# How to Deploy a VM and install an app using Bicep and the Azure DSC extension 
 
## In this I'm going to show you how to use these 2 tools in combination to Deploy a VM and then install an app in that VM. 
 
### What Is Bicep? 
Bicep is a wrapper for ARM templates in Azure, similar to how Typescript is a wrapper for JavaScript. ARM templates in Azure are used to deploy resources like VMs in the cloud. 
Bicep is just easier to read and work with than ARM templates.  
 
You can convert an existing ARM template to Bicep using "az bicep decompile --file template.json".  
 
Make sure to install the Visual Studio Code extension for Bicep 
See the Bicep documentation for more info on Bicep  
  
### What Is DSC? 
DSC(Desired State Configuration) is a PowerShell tool that automates the process of getting Windows OS in a certain state.  
Instead of writing a script that has a ton of checks to see if certain apps are installed, or if ports are open ect...  
For example, if you need an app to be installed, you just define a DSC saying that app is present and DSC will either spit out "success" if the app's installed, or install the app for you. 
 
See Get started with Desired State Configuration (DSC) for Windows for more info  
  
YouTube video that does a better job than me at explaining all this, he doesn't use Bicep though.  
  
 ## How To: 
### 1. Create a DSC script for your app 
I've provided a template to get you off the ground, it's in the 3rd party software teams files. "Bicep+DSC"->"dsc"->deploy-winmerge.ps1 and "Bicep+DSC"->"dsc"->deps/ 
 
When you install using the bicep DSC extension, it automatically unpacks the DSC package you published to this directory: 
C:/Packages/Plugins/Microsoft.Powershell.DSC/2.83.2.0/DSCWork/winmerge.ps1.0  
*Note the .0 at the end, every time you apply the DSC to a VM it'll increment that number, so if you deploy again to the same VM the path will be: 
C:/Packages/Plugins/Microsoft.Powershell.DSC/2.83.2.0/DSCWork/winmerge.ps1.1 
 
I used $((Get-ChildItem C:\Packages\Plugins\Microsoft.Powershell.DSC\2.83.2.0\DSCWork\winmerge.ps1* | sort Name -Descending | select -First 1).FullName) to get the most recent deployment's path 
 
### 2. Publish your DSC package to a storage account using Publish-AzureRmVMDscConfiguration *see the docs for more info  
After you have created a DSC, you need to upload your config package to an Azure storage account. 
 
There's a built in command as a part of the Azure CLI that packages and uploads a DSC package to a storage account: 
Publish-AzureRmVMDscConfiguration -ConfigurationPath "./winmerge.ps1" -AdditionalPath "./deps"  
*You have to define the path to your install binaries using the -AdditionalPath flag, my deps directory just has my installer in it WinMerge-2.16.18-x64-Setup.exe. 
*Uploads to the storage account as windows-powershell-dsc/winmerge.ps1.zip 
 
### 3. Create a bicep script for deploying your VM 
Quickstart: Create a Windows virtual machine using a Bicep file *This includes a template for creating a vm, but it can be overwelming all the parameters they use* 
I've provided a few templates to assist in this, it's in the 3rd party software teams files. "Bicep+DSC" 
Before even getting started with this you'll need to know the target subnet you want to deploy the VM to. You can find this by clicking export on an existing NIC attached to a VM in a network you want to target, the subnet ID looks like this: /subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/virtualNetworks/VNETNAME/subnets/SUBNETNAME 
 
deploy-all.bicep: 
Creates a network interface for the VM you want to deploy, creates the vm and deploys the app using the Azure DSC extension.  
For template I referenced my own NSG and VNet I created on my account. I quickly created these just by manually setting up a VM and deleting everything but the VNET and NSG 
 
deploy-winmerge.bicep: 
For use with an existing VM, all this does is deploy the DSC app installer on a given VM.  
You can also use the Azure CLI to apply a DSC without using Bicep at all using the Set-AzureRMVMDscExtension  
Note for this one you don't include the parent, and the name of the resource is 'vmname/dscExtension' that "vmname" needs to be the hostname of the VM where you want to apply the DSC to 
 
### 4. Add a step to your bicep script for installing the app 
My example deploy-all already has the step in there for you labeled "Microsoft_Powershell_DSC" 
 
### 5. Deploy 
Run this to run your bicep script and deploy the VM and app DSC configuration 
az deployment group create --name testdeployment --resource-group bmntmitch --template-file .\deploy-all.bicep 
  
### Troubleshooting: 
 If your deployment hangs forever, it's likely the user account you're trying to deploy as is already logged in, try restarting the VM 
Troubleshooting Powershell DSC extension https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/dsc-windows#troubleshoot-and-support 
 
Setup this script to target a stackhub from the Jumpserver VM CB01.MSCPEC.COM or govcloud: 
For govcloud you simply have to run 'az cloud set azureus' 
For a stackhub it's not as simple: 
1. You first have to get a certificate from the stackhub azure portal 
 ![GetImageAttachment.png](/.attachments/GetImageAttachment-1596788f-f42f-4d6f-a5a0-77de56dc7bdf.png)
 
2. run $env:REQUESTS_CA_BUNDLE = 'C:\certs\azurecert2.cer' to get azure cli to utilize the 
Cert 
$env:ADAL_PYTHON_SSL_NO_VERIFY = '1' 
$env:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
3. run az cloud register -n "AzureStackUser" --endpoint-resource-manager 
"https://management.tca.tca.contosolabs.com" --suffix-storage-endpoint 
"tca.tca.contosolabs.com" --suffix-keyvault-dns 
"vault.tca.tca.contosolabs.com" --endpoint-active-directory-graph-resource-id 
"https://graph.tca.tca.contosolabs.com/" 
4. run az cloud update --profile "2020-09-01-hybrid" 
5.az bicep install --version "v0.3.1" 
6. finally, az cloud set -n "AzureStackUser" Now you can use azure cli 
  
TLDR - Too Long Didn't Read: 
1.	Modify my DSC example "Bicep+DSC"->"dsc"->winmerge.ps1 for your app 
2.	Put your dependencies in the deps directory 
3.	Publish this to a storage account with Publish-AzureRmVMDscConfiguration, don't forget to use the AdditionalPath flag for your dependencies 
4.	Modify my deploy-all.bicep script, you'll have to modify the Vnet it's referencing to target the subnet you're deploying to 
5.	Deploy using az deployment group create --name testdeployment --resource-group bmntmitch --template-file .\deploy-all.bicep 
 

# 