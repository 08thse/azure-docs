---
title: Copy a blob with Python
titleSuffix: Azure Storage
description: Learn how to copy a blob in Azure Storage by using the Python client library.
services: storage
author: pauljewellmsft

ms.author: pauljewell
ms.date: 01/19/2023
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.devlang: python
ms.custom: devx-track-python, devguide-python
---

# Copy a blob with Python

This article demonstrates how to copy a blob in an Azure Storage account. It also shows how to abort a copy operation. The example code uses the [Azure Storage client library for Python](/python/api/overview/azure/storage).

## About copying blobs

When you copy a blob within the same storage account, it's a synchronous operation. When you copy across accounts, it's an asynchronous operation.

The source blob for a copy operation may be a block blob, an append blob, a page blob, or a snapshot. If the destination blob already exists, it must be of the same blob type as the source blob. An existing destination blob will be overwritten.

The destination blob can't be modified while a copy operation is in progress. A destination blob can only have one outstanding copy operation. In other words, a blob can't be the destination for multiple pending copy operations.

The entire source blob or file is always copied. Copying a range of bytes or set of blocks isn't supported.

When a blob is copied, its system properties are copied to the destination blob with the same values.

A copy operation can take any of the following forms:

- Copy a source blob to a destination blob with a different name. The destination blob can be an existing blob of the same blob type (block, append, or page), or can be a new blob created by the copy operation.
- Copy a source blob to a destination blob with the same name, effectively replacing the destination blob. Such a copy operation removes any uncommitted blocks and overwrites the destination blob's metadata.
- Copy a source file in the Azure File service to a destination blob. The destination blob can be an existing block blob, or can be a new block blob created by the copy operation. Copying from files to page blobs or append blobs isn't supported.
- Copy a snapshot over its base blob. By promoting a snapshot to the position of the base blob, you can restore an earlier version of a blob.
- Copy a snapshot to a destination blob with a different name. The resulting destination blob is a writeable blob and not a snapshot.

## Copy a blob

To copy a blob, use the following method:

[BlobClient.copyFromUrl](/java/api/com.azure.storage.blob.specialized.blobclientbase#com-azure-storage-blob-specialized-blobclientbase-copyfromurl(java-lang-string))

This method copies the data at the source URL to a blob and waits for the copy to complete before returning a response. The source must be a block blob no larger and 256 MB, and must be a public blob or have a SAS token attached.

The following code example gets a `BlobClient` object representing an existing blob and copies it to a new blob in a different container. This example also gets a lease on the source blob before copying so that no other client can modify the blob until the copy is complete and the lease is broken.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/Python/blob-devguide/blob-devguide/blob-copy.py" id="Snippet_CopyBlobURL":::

Sample output is similar to:

```console
Source blob lease state: leased
Copy status: success
Copy progress: 5/5
Copy completion time: 2022-11-14T16:53:54Z
Total bytes copied: 5
Source blob lease state: broken
```

You can also copy a blob using the following method:

- [BlobClient.beginCopy](/java/api/com.azure.storage.blob.specialized.blobclientbase#com-azure-storage-blob-specialized-blobclientbase-begincopy(java-lang-string-java-time-duration)).

This method triggers a long-running, asynchronous operation. The source may be another blob or an Azure File resource. If the source is in another storage account, the source must either be public or authorized with a SAS token.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/Python/blob-devguide/blob-devguide/blob-copy.py" id="Snippet_CopyBlobBeginCopy":::

You can also specify extended options for the copy operation by passing in a [BlobBeginCopyOptions](/java/api/com.azure.storage.blob.options.blobbegincopyoptions) object to the `beginCopy` method. The following example shows how to create a `BlobBeginCopyOptions` object and configure options to pass with the copy request:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/Python/blob-devguide/blob-devguide/blob-copy.py" id="Snippet_CopyBlobOptions":::

## Abort a copy operation

Aborting a copy operation results in a destination blob of zero length. However, the metadata for the destination blob will have the new values copied from the source blob or set explicitly during the copy operation. To keep the original metadata from before the copy, make a snapshot of the destination blob before calling one of the copy methods. The final blob will be committed when the copy completes.

To abort a copy operation, use the following method:

- [BlobClient.abortCopyFromUrl](/java/api/com.azure.storage.blob.specialized.blobclientbase#com-azure-storage-blob-specialized-blobclientbase-abortcopyfromurl(java-lang-string))

The following example stops a pending copy and leaves a destination blob with zero length and metadata:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/Python/blob-devguide/blob-devguide/blob-copy.py" id="Snippet_AbortCopy":::

## Resources

To learn more about how to copy blobs using the Azure Blob Storage client library for Python, see the following resources.

### REST API operations

The Azure SDK for Python contains libraries that build on top of the Azure REST API, allowing you to interact with REST API operations through familiar Python paradigms. The client library methods for copying blobs use the following REST API operations:

- [Copy Blob](/rest/api/storageservices/copy-blob) (REST API)
- [Abort Copy Blob](/rest/api/storageservices/abort-copy-blob) (REST API)

### Code samples

- [View code samples from this article (GitHub)](https://github.com/Azure-Samples/AzureStorageSnippets/blob/master/blobs/howto/python/blob-devguide-py/blob-devguide-blobs.py)

[!INCLUDE [storage-dev-guide-resources-python](../../../includes/storage-dev-guides/storage-dev-guide-resources-python.md)]