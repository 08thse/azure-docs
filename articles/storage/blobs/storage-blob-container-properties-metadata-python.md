---
title: Use Python to manage properties and metadata for a blob container
titleSuffix: Azure Storage
description: Learn how to set and retrieve system properties and store custom metadata on blob containers in your Azure Storage account using the Python client library.
services: storage
author: pauljewellmsft

ms.service: storage
ms.topic: how-to
ms.date: 01/04/2023
ms.author: pauljewell
ms.devlang: python
ms.custom: devx-track-python, devguide-python
---

# Manage container properties and metadata using the Python client library

Blob containers support system properties and user-defined metadata, in addition to the data they contain. This article shows how to manage system properties and user-defined metadata with the [Azure Storage client library for Python](/python/api/overview/azure/storage).

## About properties and metadata

- **System properties**: System properties exist on each Blob Storage resource. Some of them can be read or set, while others are read-only. Behind the scenes, some system properties correspond to certain standard HTTP headers. The Azure Storage client library for Python maintains these properties for you.

- **User-defined metadata**: User-defined metadata consists of one or more name-value pairs that you specify for a Blob storage resource. You can use metadata to store additional values with the resource. Metadata values are for your own purposes only, and don't affect how the resource behaves.

Metadata name/value pairs are valid HTTP headers, and should adhere to all restrictions governing HTTP headers. Metadata names must be valid HTTP header names, may contain only ASCII characters, and should be treated as case-insensitive. Metadata values containing non-ASCII characters should be Base64-encoded or URL-encoded.

## Retrieve container properties

To retrieve container properties, use the following method:

- [ContainerClient.get_container_properties](/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-get-container-properties)

The following code example fetches a container's system properties and writes the property values to a console window:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/Python/blob-devguide/blob-devguide-containers/container-properties-metadata.py" id="Snippet_GetContainerProperties":::

## Set and retrieve metadata

You can specify metadata as one or more name-value pairs on a blob or container resource. To set metadata, use the following method:

- [ContainerClient.set_container_metadata](/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-set-container-metadata)

The name of your metadata must conform to the naming conventions for C# identifiers. Metadata names preserve the case with which they were created, but are case-insensitive when set or read. If two or more metadata headers with the same name are submitted for a resource, Blob Storage comma-separates and concatenates the two values and return HTTP response code `200 (OK)`. 

Setting container metadata overwrites all existing metadata associated with the container. It's not possible to modify an individual name-value pair.

The following code example sets metadata on a container:

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Python/blob-devguide/blob-devguide-containers/container-properties-metadata.py" id="Snippet_AddContainerMetadata":::

To retrieve metadata, call the following method:

- [ContainerClient.get_container_properties](/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-get-container-properties)

The following example reads in metadata values: 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/Python/blob-devguide/blob-devguide-containers/container-properties-metadata.py" id="Snippet_ReadContainerMetadata":::

## See also

- [View code sample in GitHub](https://github.com/Azure-Samples/AzureStorageSnippets/blob/master/blobs/howto/Python/blob-devguide/blob-devguide-containers/container-properties-metadata.py)
- [Quickstart: Azure Blob Storage client library for Python](storage-quickstart-blobs-python.md)
- [Get Container Properties](/rest/api/storageservices/get-container-properties) (REST API)
- [Set Container Metadata](/rest/api/storageservices/set-container-metadata) (REST API)
- [Get Container Metadata](/rest/api/storageservices/get-container-metadata) (REST API)