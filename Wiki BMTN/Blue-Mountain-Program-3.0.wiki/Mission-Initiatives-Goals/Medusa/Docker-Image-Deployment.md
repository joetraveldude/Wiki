#Image Deployment

##Prerequisites
Azure ACR must be deployed prior via portal or bicep deployment.


## Azure CLI
Use the following snippet to login to Azure ACR, tag a docker image (optional if done already) and create a container using the aforementioned image.
```Bash
az login
az acr login --name [container registry name]
docker build --tag [image name]:[version] .
docker image tag [image name]:[version] [container registry name].azurecr.us/[image name]:[version]
docker push [container registry name].azurecr.us/[image name]:[version]
```
After you tag your image is ready for deployment, create an Azure Container Instance (ACI) and deploy your image to that container in the ACR
```Bash
# get the ACR password
$password = az acr credential show -n $acrName --query "passwords[0].value"  -o tsv

# create a new ACI instance to run this container
az container create -n samplewebapp -g $resourceGroup
            --image [container registry name].azurecr.us/[image name]:[version]
            --registry-username [container registry name]
            --registry-password "$password" `
                --restart-policy OnFailure 
    --environment-variables 'NumWords'='5' 'MinLength'='8'
    --secure-environment-variables 'foo'='bar'
```

##References
[Set environment variables in container instances](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-environment-variables#:~:text=To%20set%20environment%20variables%20when%20you%20start%20a,a%20value%20of%208%20for%20the%20second%20variable.)
[Build container images with ACR](https://markheath.net/post/build-container-images-with-acr)
[Deploy to Azure Container Instances from Azure Container Registry using a service principal](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-using-azure-container-registry)