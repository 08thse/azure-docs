---
title: List blobs with Go
titleSuffix: Azure Storage
description: Learn how to list blobs in your storage account using the Azure Storage client library for Go. Code examples show how to list blobs in a flat listing, or how to list blobs hierarchically, as though they were organized into directories or folders.
services: storage
author: pauljewellmsft

ms.service: azure-blob-storage
ms.topic: how-to
ms.date: 04/22/2024
ms.author: pauljewell
ms.devlang: golang
ms.custom: devx-track-go, devguide-go
---

# List blobs with Go

[!INCLUDE [storage-dev-guide-selector-list-blob](../../../includes/storage-dev-guides/storage-dev-guide-selector-list-blob.md)]

This article shows how to list blobs using the [Azure Storage client module for Go](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/sdk/storage/azblob#section-readme).

[!INCLUDE [storage-dev-guide-prereqs-go](../../../includes/storage-dev-guides/storage-dev-guide-prereqs-go.md)]

## Set up your environment

[!INCLUDE [storage-dev-guide-project-setup-go](../../../includes/storage-dev-guides/storage-dev-guide-project-setup-go.md)]

### Authorization

The authorization mechanism must have the necessary permissions to upload a blob. For authorization with Microsoft Entra ID (recommended), you need Azure RBAC built-in role **Storage Blob Data Reader** or higher. To learn more, see the authorization guidance for [List Blobs](/rest/api/storageservices/list-blobs#authorization).

## About blob listing options

When you list blobs from your code, you can specify many options to manage how results are returned from Azure Storage. You can specify the number of results to return in each set of results, and then retrieve the subsequent sets. You can specify a prefix to return blobs whose names begin with that character or string. And you can list blobs in a flat listing structure, or hierarchically. A hierarchical listing returns blobs as though they were organized into folders.

To list the blobs in a container using a flat listing, call the following method:

- [NewListBlobsFlatPager](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/sdk/storage/azblob#Client.NewListBlobsFlatPager)

To list the blobs in a container using a hierarchical listing, call the following method from a container client object:

- [NewListBlobsHierarchyPager](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/sdk/storage/azblob/container#Client.NewListBlobsHierarchyPager)

### Filter results with a prefix

To filter the list of blobs, specify a string for the `name_starts_with` keyword argument. The prefix string can include one or more characters. Azure Storage then returns only the blobs whose names start with that prefix.

### Flat listing versus hierarchical listing

Blobs in Azure Storage are organized in a flat paradigm, rather than a hierarchical paradigm (like a classic file system). However, you can organize blobs into *virtual directories* in order to mimic a folder structure. A virtual directory forms part of the name of the blob and is indicated by the delimiter character.

To organize blobs into virtual directories, use a delimiter character in the blob name. The default delimiter character is a forward slash (/), but you can specify any character as the delimiter.

If you name your blobs using a delimiter, then you can choose to list blobs hierarchically. For a hierarchical listing operation, Azure Storage returns any virtual directories and blobs beneath the parent object. You can call the listing operation recursively to traverse the hierarchy, similar to how you would traverse a classic file system programmatically.

## Use a flat listing

By default, a listing operation returns blobs in a flat listing. In a flat listing, blobs aren't organized by virtual directory.

The following example lists the blobs in the specified container using a flat listing:

:::code language="go" source="~/blob-devguide-go/cmd/list-blobs/list_blobs.go id="snippet_list_blobs_flat":::

Sample output is similar to:

```console
List blobs flat:
Name: file4.txt
Name: folderA/file1.txt
Name: folderA/file2.txt
Name: folderA/folderB/file3.txt
```

You can also specify options to filter list results or show additional information. The following example lists blobs and blob tags:

:::code language="go" source="~/blob-devguide-go/cmd/list-blobs/list_blobs.go id="snippet_list_blobs_flat_options":::

Sample output is similar to:

```console
List blobs flat:
Name: file4.txt, Tags: None
Name: folderA/file1.txt, Tags: None
Name: folderA/file2.txt, Tags: None
Name: folderA/folderB/file3.txt, Tags: {'tag1': 'value1', 'tag2': 'value2'}
```

> [!NOTE]
> The sample output shown assumes that you have a storage account with a flat namespace. If you've enabled the hierarchical namespace feature for your storage account, directories are not virtual. Instead, they are concrete, independent objects. As a result, directories appear in the list as zero-length blobs.</br></br>For an alternative listing option when working with a hierarchical namespace, see [List directory contents (Azure Data Lake Storage Gen2)](data-lake-storage-directory-file-acl-python.md#list-directory-contents).

## Use a hierarchical listing

When you call a listing operation hierarchically, Azure Storage returns the virtual directories and blobs at the first level of the hierarchy.

To list blobs hierarchically, use the following method:

- [ContainerClient.walk_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#azure-storage-blob-containerclient-walk-blobs)

The following example lists the blobs in the specified container using a hierarchical listing:

:::code language="go" source="~/blob-devguide-go/cmd/list-blobs/list_blobs.go id="snippet_list_blobs_hierarchical":::

Sample output is similar to:

```console
folderA/
  folderA/folderB/
    folderA/folderB/file3.txt
  folderA/file1.txt
  folderA/file2.txt
file4.txt
```

> [!NOTE]
> Blob snapshots cannot be listed in a hierarchical listing operation.

## Resources

To learn more about how to list blobs using the Azure Blob Storage client module for Go, see the following resources.

### REST API operations

The Azure SDK for Go contains libraries that build on top of the Azure REST API, allowing you to interact with REST API operations through familiar Go paradigms. The client library methods for listing blobs use the following REST API operation:

- [List Blobs](/rest/api/storageservices/list-blobs) (REST API)

### Code samples

- View [code samples](https://github.com/Azure-Samples/blob-storage-devguide-go/cmd/list-blobs/list_blobs.go) from this article (GitHub)

[!INCLUDE [storage-dev-guide-resources-go](../../../includes/storage-dev-guides/storage-dev-guide-resources-go.md)]

### See also

- [Enumerating Blob Resources](/rest/api/storageservices/enumerating-blob-resources)
- [Blob versioning](versioning-overview.md)