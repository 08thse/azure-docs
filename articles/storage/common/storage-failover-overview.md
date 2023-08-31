---
title: Azure storage account failover overview
titleSuffix: Azure Storage
description: Azure Storage supports account failover for geo-redundant storage accounts. With account failover, you can initiate the failover process for your storage account if the primary storage service endpoints become unavailable, or to perform disaster recovery testing.
services: storage
author: jimmart-dev

ms.service: azure-storage
ms.topic: conceptual
ms.date: 08/31/2023
ms.author: jammart
ms.subservice: storage-common-concepts
ms.custom: 
---

# Azure storage account failover overview

Azure Storage supports account failover for geo-redundant storage accounts. With account failover, you can initiate the failover process for your storage account if the primary storage service endpoints become unavailable. The failover updates the DNS entries for the storage service endpoints such that the endpoints for the secondary region become the new primary endpoints for your storage account. Once the failover is complete, clients can begin writing to the new primary endpoints.

This article describes the concepts and process involved with an account failover, and discusses how to prepare your storage account for recovery with the least amount of impact to your users.

## Failover options

Azure Storage accounts support two types of failover:

- [**Customer-managed failover**](#customer-managed-failover) - For unexpected service outages.
- [**Microsoft-managed failover**](#microsoft-managed-failover) - Initiated by Microsoft only in the case of a severe disaster in a primary region.

Your disaster recovery plan should be based on customer-managed failover for unexpected regional outages. Do not rely on Microsoft-managed failover, which would only be used in extreme circumstances.

Each type of failover has a unique set of use cases and corresponding expectations for data loss. The scope, use cases, expectation of data loss, and whether storage accounts configured with a hierarchical namespace (HNS) are supported for each type are summarized in the table below:

| Type                               | Failover Scope  | Use case | Expected data loss | HNS supported |
|------------------------------------|-----------------|----------|---------------------|---------------|
| Customer-managed                   | Storage account | The storage service endpoints for the primary region become unavailable, but the secondary region is available. <br></br> An Azure Advisory in which Microsoft advises you to perform a failover operation of storage accounts potentially affected by an outage. | [Yes](#failover-and-data-loss) | [Yes *(In preview)*](#azure-data-lake-storage-gen2) |
| Microsoft-managed                  | Entire region   | The primary region becomes completely unavailable due to a significant disaster, but the secondary region is available. | [Yes](#failover-and-data-loss) | [Yes](#azure-data-lake-storage-gen2) |

### Customer-managed failover

If the data endpoints for the storage services in your storage account become unavailable in the primary region, you can fail over to the secondary region. After the failover is complete, the secondary region becomes the new primary and users can proceed to access data in the new primary region.

A customer-managed account failover usually involves some data loss. For more details about potential data loss from this type of failover, see [Failure and data loss](#failover-and-data-loss).

### Microsoft-managed failover

In extreme circumstances where an entire region is unavailable due to a significant disaster, Microsoft may initiate a failover of all storage accounts in the affected region. In this case, no action on your part is required. Until the Microsoft-managed failover has completed, you won't have write access to your storage account. Your applications can read from the secondary region if your storage account is configured for RA-GRS or RA-GZRS.

> [!IMPORTANT]
> A Microsoft-managed failover is extremely rare and would be used only in the event of a significant disaster in the region. It would only be used to failover **all** affected resources in the primary region. It would not be used to failover individual storage accounts. Customers should not rely on it as part of their disaster recovery plan. Instead, create a plan that relies primarily on customer-managed failover for unexpected regional outages.

## Storage account types that support failover

Account failover is available for general-purpose v1, general-purpose v2, and Blob storage account types with Azure Resource Manager deployments.

All geo-redundant offerings support Microsoft-managed failover. In addition, some account types support customer-managed account failover, as shown in the following table:

| Type of failover | GRS/RA-GRS | GZRS/RA-GZRS |
|---|---|---|
| **Customer-managed failover** | General-purpose v2 accounts</br> General-purpose v1 accounts</br> Legacy Blob Storage accounts | General-purpose v2 accounts |
| **Microsoft-managed failover** | All account types | General-purpose v2 accounts |

### Classic storage accounts

> [!IMPORTANT]
>
> ***Classic*** **storage accounts**
>
> Customer-managed account failover is only supported for storage accounts deployed using the Azure Resource Manager (ARM) deployment model. The Azure Service Manager (ASM) deployment model, also known as *classic*, is not supported. To make classic storage accounts eligible for customer-managed account failover, they must first be [migrated to the ARM model](classic-account-migration-overview.md). Your storage account must be accessible to perform the upgrade, so the primary region cannot currently be in a failed state.
>
> In the event of a disaster that affects the primary region, Microsoft will manage the failover for classic storage accounts. For more information, see [Microsoft-managed failover](storage-disaster-recovery-guidance.md#microsoft-managed-failover).

### Azure Data Lake Storage Gen2

[!INCLUDE [updated-for-az](../../../includes/storage-failover-unplanned-hns-preview-include.md)]

## Data loss and file inconsistencies

> [!CAUTION]
> Storage account failover usually involves some data loss. In your disaster recovery plan, it's important to consider the impact an account failover would have on your data.

Because data is written asynchronously from the primary region to the secondary region, there is always a delay before a write to the primary region is copied to the secondary region. If the primary region becomes unavailable, the most recent writes may not yet have been copied to the secondary region.

When you force a failover, all data in the primary region is lost as the secondary region becomes the new primary region. The new primary region is configured to be locally redundant after the failover.

All data already copied to the secondary is maintained when the failover happens. However, any data written to the primary that has not also been copied to the secondary is lost permanently.

#### Last sync time

The **Last Sync Time** property indicates the most recent time that data from the primary region is guaranteed to have been written to the secondary region. For accounts that have a hierarchical namespace, the same **Last Sync Time** property also applies to the metadata managed by the hierarchical namespace, including ACLs. All data and metadata written prior to the last sync time is available on the secondary, while data and metadata written after the last sync time may not have been written to the secondary, and may be lost. Use this property in the event of an outage to estimate the amount of data loss you may incur by initiating an account failover.

As a best practice, design your application so that you can use the last sync time to evaluate expected data loss. For example, if you are logging all write operations, then you can compare the time of your last write operations to the last sync time to determine which writes have not been synced to the secondary.

For more information about checking the **Last Sync Time** property, see [Check the Last Sync Time property for a storage account](last-sync-time-get.md).

#### File consistency for Azure Data Lake Storage Gen2

Replication for storage accounts with a hierarchical namespace enabled (Azure Data Lake Storage Gen2) occurs at the file level. This means that if an outage in the primary region occurs, it is possible that only some of the files in a container or directory might have successfully replicated to the secondary region. Consistency for all files in a container or directory is not guaranteed. Take this into account when creating your disaster recovery plan.

## Unsupported features and services

The following features and services are not supported for account failover:

- Storage accounts that have [change feed](../blobs/storage-blob-change-feed.md) enabled are not supported for failover. For example, [operational backup of Azure Blob Storage](../../backup/blob-backup-support-matrix.md#limitations) requires the change feed. For this reason, storage accounts that have operational backup configured do not support failover. You must disable operational backup and any other features that require the change feed before initiating a failover.
- Storage accounts that have [object replication policies](../blobs/object-replication-overview.md) enabled are not supported for failover. Object replication is another feature that requires the change feed feature be enabled. To disable object replication, you must backup (download) and delete any object replication rules and disable change feed before initiating a failover.
- Azure File Sync does not support storage account failover. Storage accounts containing Azure file shares being used as cloud endpoints in Azure File Sync should not be failed over. Doing so will cause sync to stop working and may also cause unexpected data loss in the case of newly tiered files.
- Storage accounts that have hierarchical namespace enabled (Data Lake Storage Gen2) are only supported with [Microsoft-managed failover](#microsoft-managed-failover). They are not supported for [customer-managed failover](#customer-managed-failover).
- A storage account containing premium block blobs cannot be failed over. Storage accounts that support premium block blobs do not currently support geo-redundancy.
- A storage account containing any [WORM immutability policy](../blobs/immutable-storage-overview.md) enabled containers cannot be failed over. Unlocked/locked time-based retention or legal hold policies prevent failover in order to maintain compliance.

## The time and cost of failing over

The time it takes to fail over after initiation can vary though typically less than one hour.

A customer-managed failover associated with an outage in the primary region loses its geo-redundancy after a failover (and failback). Your storage account is automatically converted to locally redundant storage (LRS) in the new primary region during a failover and storage account in the original primary region is deleted. You can re-enable geo-redundant storage (GRS) or read-access geo-redundant storage (RA-GRS) for the account, but note that converting from LRS to GRS or RA-GRS incurs an additional cost. The cost is due to the network egress charges to re-replicate the data to the new secondary region. For additional information, see [Bandwidth Pricing Details](https://azure.microsoft.com/pricing/details/bandwidth/).

After you re-enable GRS for your storage account, Microsoft begins replicating the data in your account to the new secondary region. Replication time depends on many factors, which include:

- The number and size of the objects in the storage account. Many small objects can take longer than fewer and larger objects.
- The available resources for background replication, such as CPU, memory, disk, and WAN capacity. Live traffic takes priority over geo replication.
- If using Blob storage, the number of snapshots per blob.
- If using Table storage, the [data partitioning strategy](/rest/api/storageservices/designing-a-scalable-partitioning-strategy-for-azure-table-storage). The replication process can't scale beyond the number of partition keys that you use.

## Additional considerations

Review the additional considerations described in this section to understand how your applications and services may be affected when you force a failover.

### Storage account containing archived blobs

Storage accounts containing archived blobs support account failover. However, after a [customer-managed failover](#customer-managed-failover) is complete, all archived blobs need to be rehydrated to an online tier before the account can be configured for geo-redundancy.

### Storage resource provider

Microsoft provides two REST APIs for working with Azure Storage resources. These APIs form the basis of all actions you can perform against Azure Storage. The Azure Storage REST API enables you to work with data in your storage account, including blob, queue, file, and table data. The Azure Storage resource provider REST API enables you to manage the storage account and related resources.

As part of a failover, the Azure Storage resource provider fails over too. As a result, resource management operations can occur in the new primary region after the failover. The [Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) property will return the new primary location as well.

### Azure virtual machines

Azure virtual machines (VMs) do not fail over as part of an account failover. If the primary region becomes unavailable, and you fail over to the secondary region, then you will need to recreate any VMs after the failover. Also, there is a potential data loss associated with the account failover. Microsoft recommends the following [high availability](../../virtual-machines/availability.md) and [disaster recovery](../../virtual-machines/backup-recovery.md) guidance specific to virtual machines in Azure.

Keep in mind that any data stored in a temporary disk is lost when the VM is shut down.

### Azure unmanaged disks

Microsoft does not support mounting unmanaged disks after a failover. If your storage account contains unmanaged disks, Microsoft recommends converting them to managed disks before failing over.

## Copying data as an alternative to failover

If your storage account is configured for read access to the secondary region, then you can design your application to read from the secondary endpoint. If you prefer not to fail over in the event of an outage in the primary region, you can use tools such as [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/), or the [Azure Data Movement library](../common/storage-use-data-movement-library.md) to copy data from your storage account in the secondary region to another storage account in an unaffected region. You can then point your applications to that storage account for both read and write availability.

> [!CAUTION]
> An account failover should not be used as part of your data migration strategy.

## See also

- [Use geo-redundancy to design highly available applications](geo-redundant-design.md)
- [Initiate an account failover](storage-initiate-account-failover.md)
- [Check the Last Sync Time property for a storage account](last-sync-time-get.md)
- [Tutorial: Build a highly available application with Blob storage](../blobs/storage-create-geo-redundant-storage.md)
