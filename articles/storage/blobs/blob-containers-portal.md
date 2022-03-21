---
title: Manage blob containers using the Azure Portal
titleSuffix: Azure Storage
description: Learn how to manage Azure storage containers using the Azure portal
services: storage
author: stevenmatthew

ms.service: storage
ms.topic: how-to
ms.date: 03/17/2022
ms.author: shaas
ms.subservice: blobs
---

# Manage blob containers using the Azure portal

Azure blob storage allows you to store large amounts of unstructured object data. You can use blob storage to gather or expose media, content, or application data to users. Because all blob data is stored within containers, you must create a storage container before you can begin to upload data. To learn more about blob storage, read the [Introduction to Azure Blob storage](storage-blobs-introduction.md).

In this how-to article, you learn to use the Azure portal to work with container objects.

## Prerequisites

To access Azure Storage, you'll need an Azure subscription. If you don't already have a subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

All access to Azure Storage takes place through a storage account. For this how-to, create a storage account using the [Azure portal](https://portal.azure.com/), Azure PowerShell, or Azure CLI. For help creating a storage account, see [Create a storage account](../common/storage-account-create.md).

## Create a container

To create a container in the Azure portal, follow these steps:

1. Navigate to a storage account in the [Azure portal](https://portal.azure.com). If the portal menu isn't visible, click the menu button to toggle its visibility.

    :::image type="content" source="media/blob-containers-portal/menu-expand-sml.png" alt-text="Image of the Azure Portal homepage showing the location of the Menu button near the top left corner of the browser" lightbox="media/blob-containers-portal/menu-expand-lrg.png":::

1. In the left menu for the storage account, scroll to the **Data storage** section, then select **Containers**.
1. Select the **+ Container** button.
1. Type a name for your new container. The container name must be lowercase, must start with a letter or number, and can include only letters, numbers, and the dash (-) character. For more information about container and blob names, see [Naming and referencing containers, blobs, and metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
1. Set the level of public access to the container. The default level is **Private (no anonymous access)**.
1. Select **Create** to create the container.

    :::image type="content" source="media/blob-containers-portal/create-container-sml.png" alt-text="Screenshot showing how to create a container in the Azure portal" lightbox="media/blob-containers-portal/create-container-lrg.png":::

## Manage container properties and metadata

A container exposes both system properties and user-defined metadata. System properties exist on each blob storage resource. Some properties are read-only, while others can be read or set.

User-defined metadata consists of one or more name-value pairs that you specify for a blob storage resource. You can use metadata to store additional values with the resource. Metadata values are for your own purposes only, and don't affect how the resource behaves.

### View container properties

To display the properties of a container in the Azure portal, follow these steps:

1. In the Azure portal, navigate to the list of containers in your storage account.
1. Click the checkbox next to the name of the container whose properties you want to view.
1. Select the container's **More** button (**...**), and select **Container properties** to display the container's **Properties** page.

    :::image type="content" source="media/blob-containers-portal/select-container-properties-sml.png" alt-text="Screenshot showing how to display container properties in the Azure portal" lightbox="media/blob-containers-portal/select-container-properties-lrg.png":::

### Read and write container metadata

Users that have large numbers of objects within their storage account can quickly locate specific containers based on their metadata. To manage a container's metadata, follow these steps:

1. In the Azure portal, navigate to the list of containers in your storage account.
1. Click the checkbox next to the name of the container whose properties you want to view.
1. Select the container's **More** button (**...**), and select **Edit metadata** to display the **Container metadata** pane.

    :::image type="content" source="media/blob-containers-portal/select-container-metadata-sml.png" alt-text="Screenshot showing how to display container properties in the Azure portal" lightbox="media/blob-containers-portal/select-container-metadata-lrg.png":::

1. The **Container metadata** pane will display existing metadata key-value pairs. Existing data can be edited by selecting the existing key or value and overwriting the data. You can add additional metadata by and supplying data in the provided fields. Finally, select **Save** to commit your data.

    :::image type="content" source="media/blob-containers-portal/add-container-metadata-sml.png" alt-text="Screenshot showing how to display container properties in the Azure portal" lightbox="media/blob-containers-portal/add-container-metadata-lrg.png":::

## Manage container and blob access

Properly managing access to containers and their blobs is key to ensuring that your data remains safe. The following sections illustrate ways in which you can meet your access requirements.

### Manage Azure RBAC role assignments for the container

Azure role-based access control (Azure RBAC) is the authorization system you use to manage access to Azure resources. To grant access, you'll assign a role to a user, group, service principal, or managed identity. You may also choose to add one or more conditions to the role assignment. You can read about the assignment of roles at [Assign Azure roles using the Azure portal](../../role-based-access-control/role-assignments-portal.md?tabs=current).

### Enable anonymous public read access

Although anonymous read access for containers is supported, it is disabled by default. All access requests must require authorization until anonymous access is explicitly enabled. After anonymous access is enabled, any client will be able to read data within that container without authorizing the request. Read about enabling public access level at [Configure anonymous public read access for containers and blobs](anonymous-read-access-configure.md?tabs=portal)
 
###	Generate container SAS

Service SAS

User delegation SAS

###	Create a stored access policy

See the REST API for details (https://docs.microsoft.com/en-us/rest/api/storageservices/define-stored-access-policy)
i.	Immutability policies are configured in the same pane. See https://docs.microsoft.com/en-us/azure/storage/blobs/immutable-policy-configure-version-scope?tabs=azure-portal and https://docs.microsoft.com/en-us/azure/storage/blobs/immutable-policy-configure-container-scope?tabs=azure-portal

## Delete a container

When you delete a container in the Azure portal, all blobs in the container will also be deleted.

> [!WARNING]
> Following the steps below may permanently delete containers and any blobs within them. Microsoft recommends enabling container soft delete to protect containers and blobs from accidental deletion. For more info, see [Soft delete for containers](soft-delete-container-overview.md).

To delete a container in the Azure portal, follow these steps:

1. In the Azure portal, navigate to the list of containers in your storage account.
1. Select the container to delete.
1. Select the **More** button (**...**), and select **Delete**.

    :::image type="content" source="media/blob-containers-portal/delete-container-sml.png" alt-text="Screenshot showing how to delete a container in the Azure portal" lightbox="media/blob-containers-portal/delete-container-lrg.png":::

1. In the **Delete container(s)** dialog, confirm that you want to delete the container.

View deleted containers: https://docs.microsoft.com/en-us/azure/storage/blobs/soft-delete-container-enable?tabs=azure-portal#view-soft-deleted-containers.

> [!WARNING]
> Running the following examples may permanently delete containers and blobs. Microsoft recommends enabling container soft delete to protect containers and blobs from accidental deletion. For more info, see [Soft delete for containers](soft-delete-container-overview.md).

If you have container soft delete enabled for your storage account, then it's possible to retrieve containers that have been deleted. To learn more about soft delete, refer to the [Soft delete for containers](soft-delete-container-overview.md) article.

## Restore a soft-deleted container

To learn more about the soft delete data protection option, refer to the [Soft delete for containers](soft-delete-container-overview.md) article.

## Restore a container

Point-in-time restore (which is different from soft delete): https://docs.microsoft.com/en-us/azure/storage/blobs/point-in-time-restore-manage?tabs=portal

## Acquire lease/break lease

See REST API for details (https://docs.microsoft.com/en-us/rest/api/storageservices/lease-container)














## Get a shared access signature for a container

A shared access signature (SAS) provides delegated access to Azure resources. A SAS gives you granular control over how a client can access your data. For example, you can specify which resources are available to the client. You can also limit the types of operations that the client can perform, and specify the interval over which the SAS is valid.

A SAS is commonly used to provide temporary and secure access to a client who wouldn't normally have permissions. To generate either a service or account SAS, you'll need to supply values for the `–-account-name` and `-–account-key` parameters. An example of this scenario would be a service that allows users read and write their own data to your storage account.

Azure Storage supports three types of shared access signatures: user delegation, service, and account SAS. For more information on shared access signatures, see the [Grant limited access to Azure Storage resources using shared access signatures](../common/storage-sas-overview.md) article.

> [!CAUTION]
> Any client that possesses a valid SAS can access data in your storage account as permitted by that SAS. It's important to protect a SAS from malicious or unintended use. Use discretion in distributing a SAS, and have a plan in place for revoking a compromised SAS.

The following example illustrates the process of configuring a service SAS for a specific container using the `az storage container generate-sas` command. Because it's generating a service SAS, the example first retrieves the storage account key to pass as the `--account-key` value.

The example will configure the SAS with start and expiry times and a protocol. It will also specify the **delete**, **read**, **write**, and **list** permissions in the SAS using the `--permissions` parameter. You can reference the full table of permissions in the [Create a service SAS](/rest/api/storageservices/create-service-sas) article.

Copy and paste the Blob SAS token value in a secure location. It will only be displayed once and can’t be retrieved once Bash is closed. To construct the SAS URL, append the SAS token (URI) to the URL for the storage service.
