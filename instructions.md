
# Azure Beckons Containers


### Build a 'Hello World' web app​

    dotnet new webapp –o HelloWorld  
    cd HelloWorld  
    dotnet run  

### Containerize the app

Create dockerfile (Visual Studio). **Note** Below port 5164 is the port specified in Dockerfile. With docker run command port is mapped to 8080

    docker build .\ -t helloworldapp
    docker run -p 8080:5164 -d --name helloworldapp_container helloworldapp
    docker ps -a

### Create a resource group
    az group create --name azure-containers-demo-rg --location eastus

### Create Azure Container Registry
    az acr create --name <unique name> --resource-group azure-containers-demo-rg --sku premium --admin-enabled true

(Assume \<unique name\> is "azurehwcontainer" for future reference)

### Get credentials for acr
    az acr credential show --name azurehwcontainer --resource-group azure-containers-demo-rg 

(Store output of the above command as it will be used later)

### Login in docker, tag and push image (run on local system)
    docker login azurehwcontainer.azurecr.io
    docker tag helloworldapp azurehwcontainer.azurecr.io/helloworldapp:latest
    docker push azurehwcontainer.azurecr.io/helloworldapp:latest

### Login to ACR
    
### Check if image is pushed

    az acr repository list --name azurehwcontainer --resource-group azure-containers-demo-rg

### Create container instance 
**Note** ACI does not support port mapping yet! Hence, ensure the port specified is same as one in Dockerfile

    az container create --resource-group azure-containers-demo-rg --name azurehwcontainerinstance --image azurehwcontainer.azurecr.io/helloworld2app:latest --dns-name-label azurehwcontainerdemo --registry-username azurehwcontainer --registry-password <password> --ports 5164

### URL where container running

    az container show --resource-group azure-containers-demo-rg --name azurehwcontainerinstance --query ipAddress.fqdn

### Open browser and enter URL from above output
