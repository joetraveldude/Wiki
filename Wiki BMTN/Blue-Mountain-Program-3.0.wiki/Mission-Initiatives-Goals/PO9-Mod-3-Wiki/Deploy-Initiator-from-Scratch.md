	1. Pre-reqs
		a. Create a new folder structure close to your root (e.g. c:\mycode\)
		b. Clone the repo: git clone https://BlueMountainAGMP@dev.azure.com/BlueMountainAGMP/Blue%20Mountain%20Program%202.0/_git/AzureStackHub_Automation_PO9Mod3
		c. Install Packer
			i. Downloads | Packer by HashiCorp
				1) Amd64 Version
				2) Unzip file to a folder on your machine
				3) Add folder to your path (System Environment Variable)
				4) Close and Reopen VSCode (so it picks up on your env variable)
	2. Deploy Initiator
		a. Create rg: gates-image-factory
			i. This is referenced in the packer file
		b. Create storage account: stdwimages
			i. This is the storage account for your VM image
		c. Create app registration
			i. This is for the initiator VM creation. Packer will use this.
			ii. Name: e.g. InitiatorVMServicePrincipal
			iii. Single Tenant, defaults, nothing in the Redirect URI
			iv. Create a secret
				1) Copy the value: 
		d. Add role assignment on subscription
			i. Use the identity just created in app reg
			ii. Contributor, app registration
		e. Update the initiator-vhd.json file
			i. subscription id:
			ii. client id:
			iii. client secret: 
			iv. tenant id:
			v. cloud env name:     "cloud_environment_name": "USGovernment",
			vi. storage account: 
			vii. location (line 28)
		f. Build the image:
			i. cd to gatesedge\packer folder in VSCode
			ii. packer build .\initiator-vhd.json 
			iii. May get error. Run again. A couple times
		g. Create an image (initiator VM):
			i. Look in the .pdf docs for this: thru 2.2
		h. Create a VM
                        i. Refer to Deployment pdf doc

