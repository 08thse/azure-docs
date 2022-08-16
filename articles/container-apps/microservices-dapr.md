---
title: 'Tutorial: Deploy a Dapr application to Azure Container Apps using the Azure CLI'
description: Deploy a Dapr application to Azure Container Apps using the Azure CLI.
services: container-apps
author: asw101
ms.service: container-apps
ms.topic: conceptual
ms.date: 06/23/2022
ms.author: aawislan
ms.custom: ignite-fall-2021, devx-track-azurecli 
ms.devlang: azurecli
---

# Tutorial: Deploy a Dapr application to Azure Container Apps using the Azure CLI

[Dapr](https://dapr.io/) (Distributed Application Runtime) is a runtime that helps build resilient, stateless, and stateful microservices. In this tutorial, a sample Dapr application is deployed to Azure Container Apps.

You learn how to:

> [!div class="checklist"]

> * Create a Container Apps environment for your container apps
> * Create an Azure Blob Storage state store for the container app
> * Deploy two apps that produce and consume messages and persist them in the state store
> * Verify the interaction between the two microservices.

With Azure Container Apps, you get a [fully managed version of the Dapr APIs](./dapr-overview.md) when building microservices. When you use Dapr in Azure Container Apps, you can enable sidecars to run next to your microservices that provide a rich set of capabilities. Available Dapr APIs include [Service to Service calls](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/), [Pub/Sub](https://docs.dapr.io/developing-applications/building-blocks/pubsub/), [Event Bindings](https://docs.dapr.io/developing-applications/building-blocks/bindings/), [State Stores](https://docs.dapr.io/developing-applications/building-blocks/state-management/), and [Actors](https://docs.dapr.io/developing-applications/building-blocks/actors/).

In this tutorial, you deploy the same applications from the Dapr [Hello World](https://github.com/dapr/quickstarts/tree/master/tutorials/hello-kubernetes) quickstart. 

The application consists of:

* a client (Python) app that generates messages 
* a service (Node) app that consumes and persists those messages in a configured state store

The following architecture diagram illustrates the components that make up this tutorial:

:::image type="content" source="media/microservices-dapr/azure-container-apps-microservices-dapr.png" alt-text="Architecture diagram for Dapr Hello World microservices on Azure Container Apps":::

[!INCLUDE [container-apps-create-cli-steps.md](../../includes/container-apps-create-cli-steps.md)]

---


# [Bash](#tab/bash)

Individual container apps are deployed to an Azure Container Apps environment. To create the environment, run the following command:

```azurecli
az containerapp env create \
  --name $CONTAINERAPPS_ENVIRONMENT \
  --resource-group $RESOURCE_GROUP \
  --location "$LOCATION"
```

# [PowerShell](#tab/powershell)

A Log Analytics workspace is required for the Container Apps environment.  The following commands create a Log Analytics workspace and save the workspace ID and primary shared key to environment variables.  

Note that the `Get-AzOperationalInsightsWorkspaceSharedKey` will result in a warning, but the command will still succeed.

```powershell
$CmdArgs = @{
  Name = "myworkspace"
  ResourceGroupName = $RESOURCE_GROUP
  Location = $LOCATION
  PublicNetworkAccessForIngestion = "Enabled"
  PublicNetworkAccessForQuery = "Enabled"
}
New-AzOperationalInsightsWorkspace @CmdArgs
$WORKSPACE_ID = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $RESOURCE_GROUP -Name $CmdArgs.Name).CustomerId
$WORKSPACE_SHARED_KEY = (Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName $RESOURCE_GROUP -Name $CmdArgs.Name).PrimarySharedKey
```

To create the environment, run the following command:

```powershell
$CmdArgs = @{
  EnvName = $CONTAINERAPPS_ENVIRONMENT
  ResourceGroupName = $RESOURCE_GROUP
  Location = $LOCATION
  AppLogConfigurationDestination = "log-analytics"
  LogAnalyticConfigurationCustomerId = $WORKSPACE_ID
  LogAnalyticConfigurationSharedKey = $WORKSPACE_SHARED_KEY
}

New-AzContainerAppManagedEnv @CmdArgs
```

---

## Set up a state store

### Create an Azure Blob Storage account


Choose a name for `STORAGE_ACCOUNT`. Storage account names must be *unique within Azure*, from 3 to 24 characters in length and must contain numbers and lowercase letters only.


# [Bash](#tab/bash)

```bash
STORAGE_ACCOUNT_NAME ="<storage account name>"
```

# [PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT_NAME="<storage account name>"
```

---

Set the `STORAGE_ACCOUNT_CONTAINER` name.

# [Bash](#tab/bash)

```bash
STORAGE_ACCOUNT_CONTAINER="mycontainer"
```

# [PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT_CONTAINER="mycontainer"
```

---

Use the following command to create an Azure Storage account.

# [Bash](#tab/bash)

```azurecli
az storage account create \
  --name $STORAGE_ACCOUNT_NAME \
  --resource-group $RESOURCE_GROUP \
  --location "$LOCATION" \
  --sku Standard_RAGRS \
  --kind StorageV2
```

# [PowerShell](#tab/powershell)

```powershell
$CmdArgs = @{
  Name = $STORAGE_ACCOUNT_NAME
  ResourceGroupName = $RESOURCE_GROUP
  Location = $LOCATION
  SkuName = "Standard_RAGRS"
  Kind = "StorageV2"
}
$STORAGE_ACCOUNT = New-AzStorageAccount @CmdArgs
```

---

Get the storage account key with the following command:

# [Bash](#tab/bash)

```azurecli
STORAGE_ACCOUNT_KEY=`az storage account keys list --resource-group $RESOURCE_GROUP --account-name $STORAGE_ACCOUNT_NAME --query '[0].value' --out tsv`
```

# [PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT_KEY = (Get-AzStorageAccountKey -ResourceGroupName $RESOURCE_GROUP -Name $STORAGE_ACCOUNT_NAME)| Where-Object {$_.KeyName -eq "key1"}
```

---

### Configure the state store component

# [Bash](#tab/bash)

Create a config file named *statestore.yaml* with the properties that you sourced from the previous steps. This file helps enable your Dapr app to access your state store. The following example shows how your *statestore.yaml* file should look when configured for your Azure Blob Storage account:

```yaml
# statestore.yaml for Azure Blob storage component
componentType: state.azure.blobstorage
version: v1
metadata:
- name: accountName
  value: "<STORAGE_ACCOUNT>"
- name: accountKey
  secretRef: account-key
- name: containerName
  value: <STORAGE_ACCOUNT_CONTAINER>
secrets:
- name: account-key
  value: "<STORAGE_ACCOUNT_KEY>"
scopes:
- nodeapp
```

To use this file, update the placeholders:

* Replace `<STORAGE_ACCOUNT>` with the value of the `STORAGE_ACCOUNT_NAME` variable you defined. To obtain its value, run the following command:

    ```azurecli
    echo $STORAGE_ACCOUNT_NAME
    ```

* Replace `<STORAGE_ACCOUNT_KEY>` with the storage account key. To obtain its value, run the following command:

    ```azurecli
    echo $STORAGE_ACCOUNT_KEY
    ```

* Replace `<STORAGE_ACCOUNT_CONTAINER>` with the storage account container name.   To obtain its value, run the following command:

    ```azurecli
    echo $STORAGE_ACCOUNT_CONTAINER
    ```

> [!NOTE]
> Container Apps does not currently support the native [Dapr components schema](https://docs.dapr.io/operations/components/component-schema/). The above example uses the supported schema.

Navigate to the directory in which you stored the *statestore.yaml* file and run the following command to configure the Dapr component in the Container Apps environment.

If you need to add multiple components, create a separate YAML file for each component, and run the `az containerapp env dapr-component set` command multiple times to add each component.  For more information about configuring Dapr components, see [Configure Dapr components](dapr-overview.md#configure-dapr-components).

```azurecli
az containerapp env dapr-component set \
    --name $CONTAINERAPPS_ENVIRONMENT --resource-group $RESOURCE_GROUP \
    --dapr-component-name statestore \
    --yaml statestore.yaml
```

# [PowerShell](#tab/powershell)

```powershell
$acctname = New-AzContainerAppDaprMetadataObject -Name accountName -Value $STORAGE_ACCOUNT_NAME

$acctkey = New-AzContainerAppDaprMetadataObject -Name accountKey -SecretRef account-key

$containername = New-AzContainerAppDaprMetadataObject -Name containerName -Value $STORAGE_ACCOUNT_CONTAINER

$secret = New-AzContainerAppSecretObject -Name account-key -Value $STORAGE_ACCOUNT_KEY.Value

$CmdArgs = @{
  EnvName = $CONTAINERAPPS_ENVIRONMENT
  ResourceGroupName = $RESOURCE_GROUP
  DaprName = "statestore"
  Metadata = $acctname, $acctkey, $containername
  Secrets = $secret
  Scopes = "nodeapp"
  Version = "v1"
  ComponentType = "state.azure.blobstorage"
}

New-AzContainerAppManagedEnvDapr @CmdArgs
```

---

Your state store is configured using the Dapr component type state.azure.blobstorage. The component is scoped to a container app named `nodeapp` and isn't available to other container apps.

## Deploy the service application (HTTP web server)


# [Bash](#tab/bash)

```azurecli
az containerapp create \
  --name nodeapp \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image dapriosamples/hello-k8s-node:latest \
  --target-port 3000 \
  --ingress 'internal' \
  --min-replicas 1 \
  --max-replicas 1 \
  --enable-dapr \
  --dapr-app-id nodeapp \
  --dapr-app-port 3000 \
  --env-vars 'APP_PORT=3000'
```

This command deploys:

* the service (Node) app server on `--target-port 3000` (the app port) 
* its accompanying Dapr sidecar configured with `--dapr-app-id nodeapp` and `--dapr-app-port 3000'` for service discovery and invocation


# [PowerShell](#tab/powershell)

```powershell
$ENV_ID = (Get-AzContainerAppManagedEnv -ResourceGroupName $RESOURCE_GROUP -EnvName $CONTAINERAPPS_ENVIRONMENT).Id

$ENV_VARS = New-AzContainerAppEnvironmentVarObject `
  -Name APP_PORT `
  -Value 3000

$TEMPLATE_OBJ = New-AzContainerAppTemplateObject `
  -Name nodeapp `
  -Image dapriosamples/hello-k8s-node:latest `
  -Env $ENV_VARS

@CmdArgs = @{
  Name = "nodeapp"
  ResourceGroupName = $RESOURCE_GROUP
  Location = $LOCATION
  ManagedEnvironmentId = $ENV_ID
  TemplateContainer = $TEMPLATE_OBJ
  IngressTargetPort = 3000
  ScaleMinReplicas = 1
  ScaleMaxReplicas = 1
  DaprEnabled = $true
  DaprAppId = "nodeapp"
  DaprAppPort = 3000
}

New-AzContainerApp @CmdArgs
```

This command deploys:

* the service (Node) app server on `APP_PORT 3000` (the app port) 
* its accompanying Dapr sidecar configured with `-DaprAppId nodeapp` and `-DaprAppPort 3000'` for service discovery and invocation

---

By default, the image is pulled from [Docker Hub](https://hub.docker.com/r/dapriosamples/hello-k8s-node).


## Deploy the client application (headless client)

Run the following command to deploy the client container app.

# [Bash](#tab/bash)

```azurecli
az containerapp create \
  --name pythonapp \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image dapriosamples/hello-k8s-python:latest \
  --min-replicas 1 \
  --max-replicas 1 \
  --enable-dapr \
  --dapr-app-id pythonapp
```

# [PowerShell](#tab/powershell)

```powershell
$TEMPLATE_OBJ = New-AzContainerAppTemplateObject `
  -Name pythonapp `
  -Image dapriosamples/hello-k8s-python:latest

@CmdArgs = @{
  Name = "pythonapp"
  ResourceGroupName = $RESOURCE_GROUP
  Location = $LOCATION
  ManagedEnvironmentId = $ENV_ID
  TemplateContainer = $TEMPLATE_OBJ
  ScaleMinReplicas = 1
  ScaleMaxReplicas = 1
  DaprEnabled = $true
  DaprAppId = "pythonapp"
}

New-AzContainerApp @CmdArgs
```

---

By default, the image is pulled from [Docker Hub](https://hub.docker.com/r/dapriosamples/hello-k8s-python).

This command deploys `pythonapp` that also runs with a Dapr sidecar that is used to look up and securely call the Dapr sidecar for `nodeapp`. As this app is headless, there's no need to specify a target port nor is there a need to external enable ingress.  

## Verify the result

### Confirm successful state persistence

You can confirm that the services are working correctly by viewing data in your Azure Storage account.

1. Open the [Azure portal](https://portal.azure.com) in your browser and navigate to your storage account.

1. Select **Containers** left side menu.

1. Select **mycontainer**.

1. Verify that you can see the file named `order` in the container.

1. Select the file.

1. Select the **Edit** tab.

1. Select the **Refresh** button to observe how the data automatically updates.

### View Logs

Data logged via a container app are stored in the `ContainerAppConsoleLogs_CL` custom table in the Log Analytics workspace. You can view logs through the Azure portal or with the CLI. Wait a few minutes for the analytics to arrive for the first time before you're able to query the logged data.

Use the following CLI command to view logs on the command line.

# [Bash](#tab/bash)

```azurecli
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az containerapp env show --name $CONTAINERAPPS_ENVIRONMENT --resource-group $RESOURCE_GROUP --query properties.appLogsConfiguration.logAnalyticsConfiguration.customerId --out tsv`

az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'nodeapp' and (Log_s contains 'persisted' or Log_s contains 'order') | project ContainerAppName_s, Log_s, TimeGenerated | sort by TimeGenerated | take 5" \
  --out table |
```

# [PowerShell](#tab/powershell)

```powershell
$queryResults = Invoke-AzOperationalInsightsQuery -WorkspaceId $WORKSPACE_ID  -Query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'nodeapp' and (Log_s contains 'persisted' or Log_s contains 'order') | project ContainerAppName_s, Log_s, TimeGenerated | take 5 "
$queryResults.Results

```

---

The following output demonstrates the type of response to expect from the CLI command.

```bash
ContainerAppName_s    Log_s                            TableName      TimeGenerated
--------------------  -------------------------------  -------------  ------------------------
nodeapp               Got a new order! Order ID: 61    PrimaryResult  2021-10-22T21:31:46.184Z
nodeapp               Successfully persisted state.    PrimaryResult  2021-10-22T21:31:46.184Z
nodeapp               Got a new order! Order ID: 62    PrimaryResult  2021-10-22T22:01:57.174Z
nodeapp               Successfully persisted state.    PrimaryResult  2021-10-22T22:01:57.174Z
nodeapp               Got a new order! Order ID: 63    PrimaryResult  2021-10-22T22:45:44.618Z
```

## Clean up resources

Once you're done, run the following command to delete your resource group along with all the resources you created in this tutorial.

# [Bash](#tab/bash)

```azurecli
az group delete \
    --resource-group $RESOURCE_GROUP
```

# [PowerShell](#tab/powershell)

```powershell
Remove-AzResourceGroup -Name $RESOURCE_GROUP -Force
```

---

This command deletes the resource group that includes all of the resources created in this tutorial.

> [!NOTE]
> Since `pythonapp` continuously makes calls to `nodeapp` with messages that get persisted into your configured state store, it is important to complete these cleanup steps to avoid ongoing billable operations.

> [!TIP]
> Having issues? Let us know on GitHub by opening an issue in the [Azure Container Apps repo](https://github.com/microsoft/azure-container-apps).

## Next steps

> [!div class="nextstepaction"]
> [Application lifecycle management](application-lifecycle-management.md)

