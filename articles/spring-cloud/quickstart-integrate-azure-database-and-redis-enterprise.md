---
title: "Quickstart - Integrate with Azure Database for PostgreSQL and Azure Cache for Redis"
description: Explains how to provision and prepare an Azure Database for PostgreSQL and an Azure Cache for Redis to be used with apps running Azure Spring Cloud Enterprise tier. 
author: maly7
ms.author: 
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 
ms.custom: 
---
# Quickstart: Integrate with Azure Database for PostgreSQL and Azure Cache for Redis

**This article applies to:** ❌ Basic/Standard tier ✔️ Enterprise tier

This quickstart shows you how to provision and prepare an Azure Database for PostgreSQL and an Azure Cache for Redis to be used with apps running Azure Spring Cloud Enterprise tier.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A license for Azure Spring Cloud Enterprise Tier. For more information, see [View Azure Spring Cloud Enterprise Tier Offer in Azure Marketplace](./how-to-enterprise-marketplace-offer.md).
- [The Azure CLI version 2.0.67 or higher](/cli/azure/install-azure-cli).
- [Git](https://git-scm.com/).
- [jq](https://stedolan.github.io/jq/download/)
- [!INCLUDE [install-enterprise-extension](includes/install-enterprise-extension.md)]
- Complete the previous quickstarts in this series:
  - [Build and deploy apps to Azure Spring Cloud using the Enterprise Tier](./quickstart-deploy-enterprise.md).

## Provision Services


### Provision Services Using Azure CLI

### Provision Services with ARM Template

The template used in this quickstart can be found in the [ACME Fitness Store GitHub Repository](https://github.com/Azure-Samples/acme-fitness-store/blob/Azure/azure/templates/azuredeploy.json).

To Deploy this template follow these steps:

1. Select the following image to sign in to Azure and open a template. The template creates an Azure Cache for Redis and a Azure Databse for PostgreSQL Flexible Server.
    [![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Facme-fitness-store%2FAzure%2Fazure%2Ftemplates%2Fazuredeploy.json)

1. Enter values for the following fields:

   - **Resource Group:** select **Create new**, enter a unique name for the **resource group**, and then select **OK**.
   - **cacheName:** Enter the name for the Azure Cache for Redis Server.
   - **dbServerName:** Enter the name for the Azure Database for PostgreSQL Flexible Server.
   - **administratorLogin:** Enter the admin username for the Azure Database for PostgreSQL Flexible Server.
   - **administratorLoginPassword:** Enter the admin password for the Azure Database for PostgreSQL Flexible Server.
   - **tags:** Enter any custom tags.

1. Select **Review + Create** and then **Create**.

## Create Service Connectors

The following steps show how to bind applications running in Azure Spring Cloud Enterprise tier to other Azure services using Service Connectors.

1. Create a service connector to Azure Database for PostgreSQL for the Order Service Application using the following command:

    ```azurecli
    az spring-cloud connection create postgres-flexible \
        --resource-group <resource-group> \
        --target-resource-group <target-resource-group> \
        --connection order_service_db \
        --service <spring-cloud-service> \
        --app order-service \
        --deployment default \
        --server <postgres-server-name> \
        --database acmefit_order \
        --secret name=<postgres-username> secret=<postgres_password> \
        --client-type dotnet
    ```

1. Create a service connector to Azure Database for PostgreSQL for the Catalog Service Application using the following command:

    ```azurecli
    az spring-cloud connection create postgres-flexible \
        --resource-group <resource-group> \
        --target-resource-group <target-resource-group> \
        --connection catalog_service_db \
        --service <spring-cloud-service> \
        --app catalog-service \
        --deployment default \
        --server <postgres-server-name> \
        --database acmefit_catalog \
        --secret name=<postgres-username> secret=<postgres_password> \
        --client-type springboot
    ```

1. Create a service connector to Azure Cache for Redis for the Cart Service Application using the following command:

    ```azurecli
    az spring-cloud connection create redis \
        --resource-group <resource-group> \
        --target-resource-group <target-resource-group> \
        --connection cart_service_cache \
        --service <spring-cloud-service> \
        --app cart-service \
        --deployment default \
        --server <cache-server-name> \
        --database 0 \
        --client-type java
    ```

1. Use the following command to reload the Catalog Service Application to load the new connection properties:

    ```azurecli
    az spring app restart
        --resource-group <resource-group> \
        --name catalog-service \
        --service <spring-cloud-service> 
    ```

1. Retrieve the database connection information and update the Order Service Application using the following commands:

    ```azurecli
    POSTGRES_CONNECTION_STR=$(az spring-cloud connection show \
        --resource-group <resource-group> \
        --service <spring-cloud-service> \
        --deployment default \
        --connection order_service_db \
        --app order-service | jq '.configurations[0].value' -r)

    az spring-cloud app update \
        --resource-group <resource-group> \
        --name order-service \
        --service <spring-cloud-service> \
        --env "DatabaseProvider=Postgres" "ConnectionStrings__OrderContext=${POSTGRES_CONNECTION_STR}"
    ```

1. Retrieve Redis connection information and update the Cart Service Application using the following commands:

    ```azurecli
    REDIS_CONN_STR=$(az spring-cloud connection show \
        --resource-group <resource-group> \
        --service <spring-cloud-service> \
        --deployment default \
        --app cart-service \
        --connection cart_service_cache | jq -r '.configurations[0].value')

    az spring-cloud app update \
        --resource-group <resource-group> \
        --name cart-service \
        --service <spring-cloud-service> \
        --env "CART_PORT=8080" 
    ```

## Clean up resources

If you plan to continue working with subsequent quickstarts and tutorials, you might want to leave these resources in place. When no longer needed, delete the resource group, which deletes the resources in the resource group. To delete the resource group by using Azure CLI, use the following commands:

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## Next steps

> [!div class="nextstepaction"]
> [Quickstart: Securely Load Application Secrets using Key Vault](./quickstart-key-vault-enterprise.md)
