---
title: Integrate Azure Blob Storage with Service Connector
description: Integrate Azure Blob Storage into your application with Service Connector
author: mcleanbyron
ms.author: mcleans
ms.service: service-connector
ms.custom: event-tier1-build-2022
ms.topic: how-to
ms.date: 06/13/2022
---

# Integrate Azure Blob Storage with Service Connector

This page shows the supported authentication types, client types and sample codes of Azure Bob Storage - Flexible Server using Service Connector. This page also shows default environment variable names and values (or Spring Boot configuration) you get when you create the service connection. Also detail steps with sample codes about how to make connection to the blob storage. You can learn more about [Service Connector environment variable naming convention](concept-service-connector-internals.md).

## Supported compute service

- Azure App Service
- Azure Container Apps
- Azure Spring Apps

## Supported authentication types and client types

Supported authentication and clients for App Service, Container Apps and Azure Spring Apps:


| Client type        | System-assigned managed identity     | User-assigned managed identity       | Secret / connection string           | Service principal                    |
|--------------------|--------------------------------------|--------------------------------------|--------------------------------------|--------------------------------------|
| .NET               | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Java               | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Java - Spring Boot |                                      |                                      | ![yes icon](./media/green-check.png) |
| Node.js            | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Python             | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| Go             | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |
| None               | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) | ![yes icon](./media/green-check.png) |


---

## Default environment variable names or application properties and Sample codes

Reference the connection details and sample codes in following tables, accordings to your connection's authentication type and client type, to connect compute services to Azure Blob Storage.


### System-assigned managed identity

| Default environment variable name  | Description           | Example value                                           |
|------------------------------------|-----------------------|---------------------------------------------------------|
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Blob Storage endpoint | `https://<storage-account-name>.blob.core.windows.net/` |


#### Sample codes

Follow these steps and sample codes to connect to Azure Blob Storage with system-assigned managed identity.
[!INCLUDE [code sample for blob](./includes/code-blob-aad.md)]


### User-assigned managed identity

| Default environment variable name  | Description           | Example value                                           |
|------------------------------------|-----------------------|---------------------------------------------------------|
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Blob Storage endpoint | `https://<storage-account-name>.blob.core.windows.net/` |
| AZURE_STORAGEBLOB_CLIENTID         | Your client ID        | `<client-ID>`                                           |

#### Sample codes

Follow these steps and sample codes to connect to Azure Blob Storage with user-assigned managed identity.
[!INCLUDE [code sample for blob](./includes/code-blob-aad.md)]

### Connection string

#### Others(#tab/other)
| Default environment variable name  | Description                    | Example value                                                                                                       |
|------------------------------------|--------------------------------|---------------------------------------------------------------------------------------------------------------------|
| AZURE_STORAGEBLOB_CONNECTIONSTRING | Blob Storage connection string | `DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net` |

#### SpringBoot(#tab/spring)

| Application properties      | Description                    | Example value                                           |
|-----------------------------|--------------------------------|---------------------------------------------------------|
| azure.storage.account-name  | Your Blob storage-account-name | `<storage-account-name>`                                |
| azure.storage.account-key   | Your Blob Storage account key  | `<account-key>`                                          |
| azure.storage.blob-endpoint | Your Blob Storage endpoint     | `https://<storage-account-name>.blob.core.windows.net/` |


#### Sample codes

Follow these steps and sample codes to connect to Azure Blob Storage with connection string.
[!INCLUDE [code sample for blob](./includes/code-blob-secret.md)]



### Service principal

| Default environment variable name  | Description           | Example value                                           |
|------------------------------------|-----------------------|---------------------------------------------------------|
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Blob Storage endpoint | `https://<storage-account-name>.blob.core.windows.net/` |
| AZURE_STORAGEBLOB_CLIENTID         | Your client ID        | `<client-ID>`                                           |
| AZURE_STORAGEBLOB_CLIENTSECRET     | Your client secret    | `<client-secret>`                                       |
| AZURE_STORAGEBLOB_TENANTID         | Your tenant ID        | `<tenant-ID>`                                           |

#### Sample codes

Follow these steps and sample codes to connect to Azure Blob Storage with service principal.
[!INCLUDE [code sample for blob](./includes/code-blob-aad.md)]


## Next steps

Follow the tutorials to learn more about Service Connector.

> [!div class="nextstepaction"]
> [Learn about Service Connector concepts](./concept-service-connector-internals.md)
