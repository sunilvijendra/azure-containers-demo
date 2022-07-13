
# Azure Beckons Containers


### Build a 'Hello World' web app​

    dotnet new webapp –o HelloWorld  
    cd HelloWorld  
    dotnet run  

### Containerize the app

Create dockerfile (Visual Studio)

    docker build –t helloworld2app .​
    docker run -p 8080:5077 -d –-name helloworld2app_sample helloworld2app​
    docker ps -a

### Create a resource group
    az group create --name azure-containers-demo-rg --location eastus

### Create Azure Container Registry
    az acr create --name <unique name> --resource-group azure-containers-demo-rg --sku premium --admin-enabled true

(Assume \<unique name\> is "myregistry" for future reference)

### Get credentials for acr
    az acr credential show --name myregistry --resource-group azure-containers-demo-rg 

(Store output of the above command as it will be used later)

### Login in docker, tag and push image (run on local system)
    docker login myregistry.azurecr.io
    docker tag helloworld2app myregistry.azurecr.io/helloworld2app:latest
    docker push myregistry.azurecr.io/helloworld2app:latest

### Check if image is pushed

    az acr repository list --name myregistry --resource-group azure-containers-demo-rg

### Create container instance

    az container create --resource-group azure-containers-demo-rg --name myinstance --image myregistry.azurecr.io/helloworld2app:v2 --dns-name-label mydnsname --registry-username <username> --registry-password <password> --ports 5170

### URL where container running

    az container show --resource-group azure-containers-demo-rg --name myinstance --query ipAddress.fqdn

### Open browser and enter URL from above output