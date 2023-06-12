---
author: karlerickson
ms.author: v-shilichen
ms.service: spring-apps
ms.custom: event-tier1-build-2022
ms.topic: include
ms.date: 05/26/2022
---

<!-- 
For clarity of structure, a separate markdown file is used to describe how to deploy to Azure Spring Apps with Enterprise plan.

[!INCLUDE [deploy-event-driven-app-with-enterprise-plan](includes/quickstart-deploy-event-driven-app/deploy-event-driven-app-with-enterprise-plan.md)]

-->

## 2. Prepare Spring project

Use the following steps to prepare the sample locally.

[!INCLUDE [prepare-spring-project-git-event-driven](../../includes/quickstart-deploy-event-driven-app/prepare-spring-project-git-event-driven.md)]

## 3. Prepare the cloud environment

The main resources you need to run this sample is an Azure Spring Apps instance and an Azure Service Bus instance. Use the following steps to create these resources.

### 3.1. Resource naming and command default configuration

1. Accept the legal terms and privacy statements for the Enterprise tier.

   > [!NOTE]
   > This step is necessary only if your subscription has never been used to create an Enterprise tier instance of Azure Spring Apps.

   ```azurecli-interactive
   az provider register --namespace Microsoft.SaaS
   az term accept \ 
      --publisher vmware-inc \ 
      --product azure-spring-cloud-vmware-tanzu-2 \ 
      --plan asa-ent-hr-mtr
   ```

1. Use the following commands to create variables for the names of your resources and for other settings as needed. Resource names in Azure must be unique.

   ```azurecli
   RESOURCE_GROUP=<event-driven-app-resource-group-name>
   LOCATION=<desired-region>
   SERVICE_BUS_NAME_SPACE=<event-driven-app-service-bus-namespace>
   AZURE_SPRING_APPS_INSTANCE=<Azure-Spring-Apps-instance-name>
   APP_NAME=<event-driven-app-name>
   ```

2. Use the following command to sign in to Azure:

   ```azurecli
   az login
   ```

1. Use the following command to set the default location:

   ```azurecli
   az configure --defaults location=${LOCATION}
   ```

1. Use the following command to list all available subscriptions, then determine the ID for the subscription you want to use.

   ```azurecli
   az account list --output table
   ```

1. Use the following command to set your default subscription:

   ```azurecli
   az account set --subscription <subscription-ID>
   ```

1. Use the following command to create a resource group:

   ```azurecli
   az group create --resource-group ${RESOURCE_GROUP}
   ```

1. Use the following command to set the newly created resource group as the default resource group.

   ```azurecli
   az configure --defaults group=${RESOURCE_GROUP}
   ```

### 3.2. Create a Service Bus instance

Use the following command to create a Service Bus namespace:

```azurecli
az servicebus namespace create --name ${SERVICE_BUS_NAME_SPACE}
```

### 3.3. Create queues in your Service Bus instance

Use the following commands to create two queues named `lower-case` and `upper-case`:

```azurecli
az servicebus queue create \
    --namespace-name ${SERVICE_BUS_NAME_SPACE} \
    --name lower-case
az servicebus queue create \
    --namespace-name ${SERVICE_BUS_NAME_SPACE} \
    --name upper-case
```

### 3.4. Create the Azure Spring Apps instance

Use the following command to create your Azure Spring Apps instance:

```azurecli
az spring create \
    --name ${AZURE_SPRING_APPS_INSTANCE} \
    --sku Enterprise
```

### 3.5. Create an app in your Azure Spring Apps instance

Use the following command to create an app in the Azure Spring Apps instance:

```azurecli
az spring app create \
    --service ${AZURE_SPRING_APPS_INSTANCE} \
    --name ${APP_NAME} \
    --assign-endpoint true
```

### 3.6. Bind the Service Bus to Azure Spring Apps and deploy the app

Now both the Service Bus and the app in Azure Spring Apps have been created, but the app can't connect to the Service Bus. Use the following steps to enable the app to connect to the Service Bus, and then deploy the app.

Get the Service Bus's connection string by using the following command:

```azurecli
SERVICE_BUS_CONNECTION_STRING=$(az servicebus namespace authorization-rule keys list \
    --namespace-name ${SERVICE_BUS_NAME_SPACE} \
    --name RootManageSharedAccessKey \
    --query primaryConnectionString \
    --output tsv)
```

## 4. Deploy the app to Azure Spring Apps

1. Use the following command to provide the connection string to the app through an environment variable.

   ```azurecli
   az spring app update \
       --service ${AZURE_SPRING_APPS_INSTANCE} \
       --name ${APP_NAME} \
       --env SERVICE-BUS-CONNECTION-STRING=${SERVICE_BUS_CONNECTION_STRING} \
       spring.cloud.azure.keyvault.secret.property-source-enabled=false
   ```

1. Now the cloud environment is ready. Deploy the app by using the following command.

   ```azurecli
   az spring app deploy \
       --service ${AZURE_SPRING_APPS_INSTANCE} \
       --name ${APP_NAME} \
       --artifact-path target/simple-event-driven-app-0.0.1-SNAPSHOT.jar
   ```
