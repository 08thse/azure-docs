---
title: Quickstart - Create a service connection in Spring Cloud with the Azure CLI
description: Quickstart showing how to create a service connection in Spring Cloud with the Azure CLI
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart 
ms.date: 10/29/2021
---

# Quickstart: Create a service connection in Spring Cloud with the Azure CLI

The [Azure command-line interface (Azure CLI)](/cli/azure) is a set of commands used to create and manage Azure resources. The Azure CLI is available across Azure services and is designed to get you working quickly with Azure, with an emphasis on automation. This quickstart shows you the options to create Azure Web PubSub instance with the Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- This quickstart requires version 2.22.0 or higher of the Azure CLI. If using Azure Cloud Shell, the latest version is already installed.

- This quickstart assumes that you already have at least a Spring Cloud application running on Azure. If you don't have a Spring Cloud application, [create one](../spring-cloud/quickstart.md).


## View supported target service types

Use the Azure CLI [az spring-cloud connection]() command create and manage service connections to your Spring Cloud application. 

```azurecli-interactive
az spring-cloud connection list-support-types
```

## Create a service connection

Use the Azure CLI [az spring-cloud connection]() command to create a service connection to a SQL database, providing the following information:

- **Source compute service resource group name:** The resource group name of the Spring Cloud.
- **Spring Cloud name:** The name of your Spring Cloud that connects to the target service.
- **Target service resource group name:** The resource group name of the SQL database.
- **SQL DB name:** The server name of your SQL DB database.
- **Connection name:** The connection name that identifies the connection between your Spring Cloud and target service.

```azurecli-interactive
az spring-cloud connection create sql -sg "<your-spring-cloud-resource-group>" --spring-cloud "<your-spring-cloud-name>" -tg "<your-sql-db-resource-group>" --sql-name "<your-sql-database-name>" --name "<your-connection-name>"
```

## View connections

Use the Azure CLI [az spring-cloud connection]() command to list connection to your Spring Cloud application, providing the following information:

- **Source compute service resource group name:** The resource group name of the Spring Cloud.
- **Spring Cloudname:** The name of your Spring Cloud that connects to the target service.

```azurecli-interactive
az spring-cloud connection list-configuration -sg "<your-spring-cloud-resource-group>" --spring-cloud "<your-spring-cloud-name>"
```

## Next steps

Follow the tutorials listed below to start building your own application with Service Connector.

> [!div class="nextstepaction"]
> [Tutorial: Spring Cloud + MySQL](./tutorial-java-spring-mysql.md)

> [!div class="nextstepaction"]
> [Tutorial: Spring Cloud + Apache Kafka on Confluent Cloud](./tutorial-java-spring-confluent-kafka.md)