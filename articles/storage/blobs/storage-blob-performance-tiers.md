---
title: Block blob storage performance tiers — Azure Storage
description: Discusses the difference between premium and standard performance tiers for Azure block blob storage.
author: normesta

ms.author: normesta
ms.date: 05/17/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
---

# Performance tiers for block blob storage

As enterprises deploy performance sensitive cloud-native applications, it's important to have options for cost-effective data storage at different performance levels.

Azure block blob storage offers two different performance tiers:

- **Premium**: optimized for high transaction rates and single-digit consistent storage latency
- **Standard**: optimized for high capacity and high throughput

The following considerations apply to the different performance tiers:

| Area | Standard performance | Premium performance |
|--|--|--|
| Region availability | All  regions | In [select regions](https://azure.microsoft.com/global-infrastructure/services/?products=storage) |
| Supported [storage account types](../common/storage-account-overview.md#types-of-storage-accounts) | General purpose v2, general purpose v1, legacy blob | Premium block blob |
| Supports [high throughput block blobs](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) | Yes, at greater than 4 MiB PutBlock or PutBlob sizes | Yes, at greater than 256 KiB PutBlock or PutBlob sizes |
| Redundancy | See [Types of storage accounts](../common/storage-account-overview.md#types-of-storage-accounts) | Currently supports only locally-redundant storage (LRS) and zone-redudant storage (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div> |

<div id="zone-redundant-storage"><sup>1</sup>Zone-redundant storage (ZRS) is available in select regions for premium  block blob storage accounts.</div>

Regarding cost, premium performance provides optimized pricing for applications with high transaction rates to help [lower total storage cost](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) for these workloads.

## Premium performance

Premium performance block blob storage makes data available via high-performance hardware. Data is stored on solid-state drives (SSDs) which are optimized for low latency. SSDs provide higher throughput compared to traditional hard drives.

Premium performance storage is ideal for workloads that require fast and consistent response times. It's best for workloads that perform many small transactions. Example workloads include:

- **Interactive workloads**. These workloads require instant updates and user feedback, such as e-commerce and mapping applications. For example, in an e-commerce application, less frequently viewed items are likely not cached. However, they must be instantly displayed to the customer on demand. As another example, data scientists, analysts and developers can derive time-sensitive insights even faster by running queries on data stored in an account that uses the premium performance tier.

- **IoT/ streaming analytics**. In an IoT scenario, lots of smaller write operations might be pushed to the cloud every second. Large amounts of data might be taken in, aggregated for analysis purposes, and then deleted almost immediately. The high ingestion capabilities of premium block blob storage make it efficient for this type of workload.

- **Artificial intelligence/machine learning (AI/ML)**. AI/ML deals with the consumption and processing of different data types like visuals, speech, and text. This high-performance computing type of workload deals with large amounts of data that requires rapid response and efficient ingestion times for data analysis.

- **Data transformation**. Processes that require constant editing, modification, and conversion of data require instant updates. For accurate data representation, consumers of this data must see these changes reflected immediately.

To learn more about how other partners have benefited from using the premium performance tier for their workloads, see [Premium block blob storage scenarios](storage-blob-block-blob-premium.md).

The premium performance tier has a higher storage cost but a lower transaction cost as compared to the standard performance tier. If your applications and workloads execute a large number of transactions, the premium performance tier can be cost-effective, especially if the workload is write-heavy.

In most cases, workloads executing more than 35 to 40 transactions per second per terabyte (TPS/TB) are good candidates for this performance tier. For example, if your workload executes 500 million read operations and 100 million write operations in a month, then you can calculate the TPS/TB as follows: 

- Write transactions per second = 100,000,000 / (30*24*60*60) = 39 (_rounded to the nearest whole number_) 

- Read transactions per second = 500,000,000 / (30*24*60*60) = 193 (_rounded to the nearest whole number_)

- Total transactions per second = 193 + 39 = 232 

- Assuming your account had 5 TB data on average, then TPS/TB would be 230/5 = 46. 

> [NOTE]
> Prices differ per operation and per region. Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to compare pricing between standard and premium performance tiers. 

The following table demonstrates the cost-effectiveness of the premium performance tier in an Azure Data Lake Storage Gen2 enabled account (an account that has a hierarchical namespace). Each column represents the number of transactions in a month. Each row represents the percentage of transactions that are read transactions. Each cell in the table shows the percentage of cost reduction associated with a read transaction percentage and the number of transactions executed.

For example, assuming that your account is in the East US 2 region, the number of transactions with your account exceeds 90M, and 70% of those transactions are read transactions, the premium performance tier is more cost-effective.

> [!div class="mx-imgBorder"]
> ![Performance table](./media/storage-blob-performance-tiers/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE]
> If you prefer to evaluate cost effectiveness based on the number of transactions per second for each TB of data, you can use the column headings that appear at the bottom of the table.

For more information about pricing, see the [Azure Data Lake Storage Gen2 pricing](https://azure.microsoft.com/pricing/details/storage/data-lake/) page.

## Standard performance

Standard performance supports different [access tiers](storage-blob-storage-tiers.md) to store data in the most cost-effective manner. It's optimized for high capacity and high throughput on large data sets.

- **Backup and disaster recovery datasets**. Standard performance storage offers cost-efficient tiers, making it a perfect use case for both short-term and long-term disaster recovery datasets, secondary backups, and compliance data archiving.

- **Media content**. Images and videos often are accessed frequently when first created and stored, but this content type is used less often as it gets older. Standard performance storage offers suitable tiers for media content needs.

- **Bulk data processing**. These kinds of workloads are suitable for standard storage because they require cost-effective high-throughput storage instead of consistent low latency. Large, raw datasets are staged for processing and eventually migrate to cooler tiers.

## Migrate from standard to premium

You can't convert an existing standard performance storage account to a block blob storage account with premium performance. To migrate to a premium performance storage account, you must create a premium block blob account, and migrate the data to the new account. For more information, see [Create a BlockBlobStorage account](../common/storage-account-create.md).

To copy blobs between storage accounts, you can use the latest version of the [AzCopy](../common/storage-use-azcopy-v10.md#transfer-data) command-line tool. Other tools such as Azure Data Factory are also available for data movement and transformation.

## Blob lifecycle management

Blob storage lifecycle management offers a rich, rule-based policy:

- **Premium**: Expire data at the end of its lifecycle.
- **Standard**: Transition data to the best access tier and expire data at the end of its lifecycle.

To learn more, see [Manage the Azure Blob storage lifecycle](./lifecycle-management-overview.md).

You can't move data that's stored in a premium block blob storage account between hot, cool, and archive tiers. However, you can copy blobs from a block blob storage account to the hot access tier in a *different* account. To copy data to a different account, use the [Put Block From URL](/rest/api/storageservices/put-block-from-url) API or [AzCopy v10](../common/storage-use-azcopy-v10.md). The **Put Block From URL** API synchronously copies data on the server. The call completes only after all the data is moved from the original server location to the destination location.

## Next steps

Evaluate hot, cool, and archive in GPv2 and Blob storage accounts.

- [Learn about rehydrating blob data from the archive tier](archive-rehydrate-overview.md)
- [Evaluate usage of your current storage accounts by enabling Azure Storage metrics](./monitor-blob-storage.md)
- [Check hot, cool, and archive pricing in Blob storage and GPv2 accounts by region](https://azure.microsoft.com/pricing/details/storage/)
- [Check data transfers pricing](https://azure.microsoft.com/pricing/details/data-transfers/)
