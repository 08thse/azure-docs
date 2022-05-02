---
title: 'Tutorial: Connect a web app to Azure App Configuration with Service Connector'
description: Learn how you can connect an ASP.NET Core application hosted in Azure Web Apps to App Configuration using Service Connector'
author: maud-lv
ms.author: malev
ms.service: service-connector
ms.topic: tutorial
ms.date: 05/01/2022
---

# Tutorial: Connect a web app to Azure App Configuration with Service Connector

Learn how to connect an ASP.NET Core app running on Azure App Service, to Azure App Configuration, using one of the following methods:

- System-assigned managed identity (SMI)
- User-assigned managed identity (UMI)
- Service principal
- Connection string
  
In this tutorial, use the Azure CLI to complete the following tasks:

> [!div class="checklist"]
> - Set up Azure resources
> - Create a connection between a web app and App Configuration
> - Build and deploy your app to Azure App Service

## Prerequisites

- An Azure account with an active subscription. Your access role within the subscription must be "Contributor" or "Owner". [Create an account for free](https://azure.microsoft.com/free/dotnet).
- The Azure CLI. You can use it in [Azure Cloud Shell](https://shell.azure.com/) or [install it locally](/cli/azure/install-azure-cli).
- [.NET SDK](https://dotnet.microsoft.com/download)
- [Git](/devops/develop/git/install-and-set-up-git)

## Sign in to Azure

Sign in to the Azure portal at [https://portal.azure.com/](https://portal.azure.com/) with your Azure account.

## Set up Azure resources

Start by creating your Azure resources.

1. Clone the following sample repo:

    ```bash
    git clone https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet.git
    ```


1. Deploy the web app to Azure
    
    Run `az login` to sign in to and follow these steps to create an App Service and deploy the sample app. Make sure you have the Subscription Contributor role.

    ### [SMI](#tab/smi)

    Create an app service and deploy the sample app that uses system-assigned managed identity to interact with App Config.

    ```azurecli
    # Change directory to the SMI sample
    cd system-managed-identity

    # Create a web app

    LOCATION='eastus'
    RESOURCE_GROUP_NAME='service-connector-tutorial-rg'
    APP_SERVICE_NAME='webapp-appconfig-smi'

    az webapp up --location $LOCATION --resource-group $RESOURCE_GROUP_NAME --name $APP_SERVICE_NAME
    ```

    * *location* &rarr; A location near you, for example `eastus`. Use `az account list-locations --output table` to list locations.
    * *resource-group* &rarr; You will use this resource group to organize all the Azure resources needed to complete this tutorial. (for example, `service-connector-tutorial-rg`)
    * *name* &rarr; The app service name is used as both the name of the resource in Azure and to form the fully qualified domain name for your app in the form of the server endpoint `https://<app-service-name>.azurewebsites.com`. This name must be **unique across all Azure** and the only allowed characters are `A`-`Z`, `0`-`9`, and `-`. For example, use `webapp-appconfig-smi-\<unique-id>` where `\<unique-id>` is any three characters.

    ### [UMI](#tab/umi)

    Create an app service and deploy the sample app that uses user-assigned managed identity to interact with App Config. 

    ```azurecli
    # Change directory to the UMI sample
    cd user-assigned-managed-identity

    # Create a web app

    LOCATION='eastus'
    RESOURCE_GROUP_NAME='service-connector-tutorial-rg'
    APP_SERVICE_NAME='webapp-appconfig-umi'

    az webapp up --location $LOCATION --resource-group $RESOURCE_GROUP_NAME --name $APP_SERVICE_NAME
    ```

    * *location* &rarr; A location near you, for example `eastus`. Use `az account list-locations --output table` to list locations.
    * *resource-group* &rarr; You will use this resource group to organize all the Azure resources needed to complete this tutorial. (for example, `service-connector-tutorial-rg`)
    * *name* &rarr; The app service name is used as both the name of the resource in Azure and to form the fully qualified domain name for your app in the form of the server endpoint `https://<app-service-name>.azurewebsites.com`. This name must be **unique across all Azure** and the only allowed characters are `A`-`Z`, `0`-`9`, and `-`. For example, use `webapp-appconfig-umi-\<unique-id>` where `\<unique-id>` is any three characters.

    Create a user-assigned managed idendity. Save the output into a temporary notepad.
    ```azurecli
    az identity create --resource-group $RESOURCE_GROUP_NAME -n "myIdentity"
    ```

    ### [Service principal](#tab/serviceprincipal)

    Create an app service and deploy the sample app that uses service principal to interact with App Config.

    ```azurecli
    # Change directory to the service principal sample
    cd service-principal

    # Create a web app

    LOCATION='eastus'
    RESOURCE_GROUP_NAME='service-connector-tutorial-rg'
    APP_SERVICE_NAME='webapp-appconfig-sp'

    az webapp up --location $LOCATION --resource-group $RESOURCE_GROUP_NAME --name $APP_SERVICE_NAME
    ```

    * *location* &rarr; A location near you, for example `eastus`. Use `az account list-locations --output table` to list locations.
    * *resource-group* &rarr; You will use this resource group to organize all the Azure resources needed to complete this tutorial. (for example, `service-connector-tutorial-rg`)
    * *name* &rarr; The app service name is used as both the name of the resource in Azure and to form the fully qualified domain name for your app in the form of the server endpoint `https://<app-service-name>.azurewebsites.com`. This name must be **unique across all Azure** and the only allowed characters are `A`-`Z`, `0`-`9`, and `-`. For example, use `webapp-appconfig-sp-\<unique-id>` where `\<unique-id>` is any three characters.

    Create a service pricipal, make sure replace the `yourSubscriptionID` with your actual subscription ID. Save the output into a temporary notepad.
    
    ```azurecli
    az ad sp create-for-rbac --name myServicePrincipal --role Contributor --scopes /subscriptions/{yourSubscriptionID}/resourceGroups/$RESOURCE_GROUP_NAME
    ```

    ### [Connection string](#tab/connectionstring)

    Create an app service and deploy the sample app that uses connection string to interact with App Config. .

    ```azurecli
    # Change directory to the service principal sample
    cd connection-string

    # Create a web app

    LOCATION='eastus'
    RESOURCE_GROUP_NAME='service-connector-tutorial-rg'
    APP_SERVICE_NAME='webapp-appconfig-cs'

    az webapp up --location $LOCATION --resource-group $RESOURCE_GROUP_NAME --name $APP_SERVICE_NAME
    ```

    * *location* &rarr; A location near you, for example `eastus`. Use `az account list-locations --output table` to list locations.
    * *resource-group* &rarr; You will use this resource group to organize all the Azure resources needed to complete this tutorial. (for example, `service-connector-tutorial-rg`)
    * *name* &rarr; The app service name is used as both the name of the resource in Azure and to form the fully qualified domain name for your app in the form of the server endpoint `https://<app-service-name>.azurewebsites.com`. This name must be **unique across all Azure** and the only allowed characters are `A`-`Z`, `0`-`9`, and `-`. For example, use `webapp-appconfig-cs-\<unique-id>` where `\<unique-id>` is any three characters.

    ---

1. Create an Azure App Configuration store

    ```azurecli
    APP_CONFIG_NAME='my-app-config'

    az appconfig create -g $RESOURCE_GROUP_NAME -n $APP_CONFIG_NAME --sku Free -l eastus
    ```

1. Import the test configuration file to Azure App Configuration.

    ### [SMI](#tab/smi)

    Import the test configuration file to Azure App Configuration using a system-assigned managed identity.

    1. Cd into the folder `serviceconnector-webapp-appconfig-dotnet\system-managed-identity\Microsoft.Azure.ServiceConnector.Sample`
    1. Import the [./sampleconfigs.json](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/system-managed-identity/Microsoft.Azure.ServiceConnector.Sample/sampleconfigs.json) test configuration file into the App Configuration store. If you're using Cloud Shell, upload [sampleconfigs.json](../cloud-shell/persisting-shell-storage.md) before running the command.

        ```azurecli
        az appconfig kv import -n $APP_CONFIG_NAME --source file --format json --path ./sampleconfigs.json --separator : --yes
        ```

    ### [UMI](#tab/umi)

    Import the test configuration file to Azure App Configuration using a user-assigned managed identity.

    1. Cd into the folder `serviceconnector-webapp-appconfig-dotnet\user-assigned-managed-identity\Microsoft.Azure.ServiceConnector.Sample`
    1. Import the [./sampleconfigs.json](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/user-assigned-managed-identity/Microsoft.Azure.ServiceConnector.Sample/sampleconfigs.json) test configuration file into the App Configuration store. If you're using Cloud Shell, upload [sampleconfigs.json](../cloud-shell/persisting-shell-storage.md) before running the command.

        ```azurecli
        az appconfig kv import -n $APP_CONFIG_NAME --source file --format json --path ./sampleconfigs.json --separator : --yes
        ```

    ### [Service principal](#tab/serviceprincipal)

    Import the test configuration file to Azure App Configuration using service principal.

    1. Cd into the folder `serviceconnector-webapp-appconfig-dotnet\service-principal\Microsoft.Azure.ServiceConnector.Sample`
    1. Import the [./sampleconfigs.json](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/service-principal/Microsoft.Azure.ServiceConnector.Sample/sampleconfigs.json) test configuration file into the App Configuration store. If you're using Cloud Shell, upload [sampleconfigs.json](../cloud-shell/persisting-shell-storage.md) before running the command.

        ```azurecli
        az appconfig kv import -n $APP_CONFIG_NAME --source file --format json --path ./sampleconfigs.json --separator : --yes
        ```

    ### [Connection string](#tab/connectionstring)

    Import the test configuration file to Azure App Configuration using a connection string.

    1. Cd into the folder `serviceconnector-webapp-appconfig-dotnet\connection-string\Microsoft.Azure.ServiceConnector.Sample`
    1. Import the [./sampleconfigs.json](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/connection-string/Microsoft.Azure.ServiceConnector.Sample/sampleconfigs.json) test configuration file into the App Configuration store. If you're using Cloud Shell, upload [sampleconfigs.json](../cloud-shell/persisting-shell-storage.md) before running the command.

        ```azurecli
        az appconfig kv import -n $APP_CONFIG_NAME --source file --format json --path ./sampleconfigs.json --separator : --yes
        ```

    ---

## Connect the web app to App Configuration

Create a connection between your web application and your App Configuration store.

### [SMI](#tab/smi)

Create a connection between your web application and your App Configuration store, using a system-assigned managed identity authentication. This connection is done through Service Connector.

```azurecli
az webapp connection create appconfig -g $RESOURCE_GROUP_NAME -n $APP_SERVICE_NAME --app-config $APP_CONFIG_NAME --tg $RESOURCE_GROUP_NAME --connection "app_config_smi" --system-identity
```

`system-identity` refers to the system-assigned managed identity (SMI) authentication type. Service Connector also supports the following authentications: user-assigned managed identity (UMI), connection string (secret) and service principal.

### [UMI](#tab/umi)

Create a connection between your web application and your App Configuration store, using a user-assigned managed identity authentication. This connection is done through Service Connector.

```azurecli
az webapp connection create appconfig -g $RESOURCE_GROUP_NAME -n $APP_SERVICE_NAME --app-config $APP_CONFIG_NAME --tg $RESOURCE_GROUP_NAME --connection "app_config_umi" --user-identity client-id=<myIdentityClientId> subs-id=<myTestSubsId>
```

`user-identity` refers to the user-assigned managed identity authentication type. Service Connector also supports the following authentications: system-assigned managed identity, connection string (secret) and service principal.

There are two ways you can find the `client-id`:

- In the Azure CLI, enter `az identity show -n "myIdentity" -g $RESOURCE_GROUP_NAME  --query 'clientId'`.
- In the Azure portal, open the Managed Identity that was created earlier and in **Overview**, get the value under **Client ID**.

### [Service principal](#tab/serviceprincipal)

Create a connection between your web application and your App Configuration store, using a service principal. This is done through Service Connector.

```azurecli
az webapp connection create appconfig -g $RESOURCE_GROUP_NAME -n $APP_SERVICE_NAME --app-config $APP_CONFIG_NAME --tg $RESOURCE_GROUP_NAME --connection "app_config_sp" --service-principal client-id=<mySPClientId>  secret=<mySPSecret>
```

`service-principal` refers to the service principal authentication type. Service Connector also supports the following authentications: system-assigned managed identity (UMI), user-assigned managed identity (UMI) and connection string (secret).

### [Connection string](#tab/connectionstring)

Create a connection between your web application and your App Configuration store, using a connection string. This connection is done through Service Connector.

```azurecli
az webapp connection create appconfig -g $RESOURCE_GROUP_NAME -n $APP_SERVICE_NAME --app-config $APP_CONFIG_NAME --tg $RESOURCE_GROUP_NAME --connection "app_config_cs" --secret
```

`secret` refers to the connection-string authentication type. Service Connector also supports the following authentications: system-assigned managed identity, user-assigned managed identity, and service principal.

---

## Validate the connection

1. To check if the connection is working, navigate to your web app at `https://<myWebAppName>.azurewebsites.net/` from your browser. Once the website is up, you'll see it displaying "Hello. Your Azure WebApp is connected to App Configuration by ServiceConnector now".

## How it works

Find below what Service Connector manages behind the scenes for each authentication type.

### [SMI](#tab/smi)

Service Connector manages the connection configuration for you:

- Set up the web app's `AZURE_APPCONFIGURATION_ENDPOINT` to let the application access it and get the App Configuration endpoint. Access [sample code](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/system-managed-identity/Microsoft.Azure.ServiceConnector.Sample/Program.cs#L37).
- Activate the web app's system-assigned managed authentication and grant App Configuration a Data Reader role to let the application authenticate to the App Configuration using DefaultAzureCredential from Azure.Identity. Access [sample code](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/system-managed-identity/Microsoft.Azure.ServiceConnector.Sample/Program.cs#L43).

### [UMI](#tab/umi)

Service Connector manages the connection configuration for you:

- Set up the web app's `AZURE_APPCONFIGURATION_ENDPOINT` to let the application access it and get the App Configuration endpoint. Access [sample code](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/user-assigned-managed-identity/Microsoft.Azure.ServiceConnector.Sample/Program.cs#L37).
- Activate the web app's user-assigned managed authentication and grant App Configuration a Data Reader role to let the application authenticate to the App Configuration using DefaultAzureCredential from Azure.Identity. Access [sample code](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/user-assigned-managed-identity/ServiceConnectorSample/Program.cs#L16).

### [Service principal](#tab/serviceprincipal)

Service Connector manages the connection configuration for you:

- Set up the web app's `AZURE_APPCONFIGURATION_ENDPOINT` to let the application access it and get the App Configuration endpoint. Access [sample code](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/service-principal/ServiceConnectorSample/Program.cs#L10).
- Activate the web app's system-assigned managed authentication and grant App Configuration a Data Reader role to let the application authenticate to the App Configuration using DefaultAzureCredential from Azure.Identity. Access [sample code](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/service-principal/Microsoft.Azure.ServiceConnector.Sample/Program.cs#L43).

### [Connection string](#tab/connectionstring)

Service Connector manages the connection configuration for you:

- Set up the web app's `AZURE_APPCONFIGURATION_CONNECTIONSTRING` to let the application access it and get the App Configuration connection string. Access [sample code](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/connection-string/Microsoft.Azure.ServiceConnector.Sample/Program.cs#L9-L12).
- Activate the web app's system-assigned managed authentication and grant App Configuration a Data Reader role to let the application authenticate to the App Configuration using DefaultAzureCredential from Azure.Identity. Access [sample code](https://github.com/Azure-Samples/serviceconnector-webapp-appconfig-dotnet/blob/main/connection-string/Microsoft.Azure.ServiceConnector.Sample/Program.cs#L43).

---

For more information, go to [Service Connector internals.](concept-service-connector-internals.md)

## Test (optional)

Optionally, do the following tests:

1. Update the value of the key `SampleApplication:Settings:Messages` in the App Configuration Store.

    ```azurecli
    az appconfig kv set -n <myAppConfigStoreName> --key SampleApplication:Settings:Messages --value hello --yes
    ```

1. Navigate to your Azure web app by going to `https://<myWebAppName>.azurewebsites.net/` and refresh the page. You'll see that the message is updated to "hello".

## Cleanup

Once you're done, delete the Azure resources you created.

`az group delete -n <myResourceGroupName> --yes`

## Next steps

Follow the tutorials listed below to learn more about Service Connector.

> [!div class="nextstepaction"]
> [Learn about Service Connector concepts](./concept-service-connector-internals.md)
