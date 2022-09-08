---
title: Introduction to Azure Storage Mover | Microsoft Docs
description: An overview of Azure Storage Mover, a fully managed migration service for your files and folder migrations to Azure Storage.
author: stevenmatthew

ms.service: storage-mover
ms.topic: overview
ms.date: 08/29/2022
ms.author: shaas
---

<!-- 
!########################################################
STATUS: IN REVIEW

CONTENT: final

REVIEW Stephen/Fabian: not reviewed
REVIEW Engineering: not reviewed
EDIT PASS: not started

Document score: 100 (393 words and 0 issues)

!########################################################
-->

# What is Azure Storage Mover?

<!--
Azure Storage Mover enables you to migrate your files and folders to Azure Storage. It's a fully-managed migration service that allows you to minimize downtime for your workload.

- You can use it for different migration scenarios, such as lift-and-shift as well as cloud migrations you'll have to repeat occasionally.
- Maintain oversight and manage the migration of all your globally distributed file shares from a single storage mover resource.

Azure Storage Mover is a new service, currently in public preview.
-->

Azure Storage Mover is a fully managed migration service that enables you to migrate your files and folders to Azure Storage while minimizing downtime for your workload. You can use Storage Mover for different migration scenarios such as *lift-and-shift*, and for cloud migrations that you'll have to repeat occasionally. Azure Storage Mover also helps maintain oversight and manage the migration of all your globally distributed file shares from a single storage mover resource.

Azure Storage Mover is a new service, currently in public preview.

## Supported sources and targets

:::row:::
  :::column:::
    :::image type="content" source="media/overview/nfs-to-flat-blob.png" alt-text="An image illustrating a source NFS share migrated through an Azure Storage Mover agent VM to an Azure Storage blob container." lightbox="media/overview/nfs-to-flat-blob-large.png":::
  :::column-end:::
  :::column:::
    <!--
    At this time in the Azure Storage Mover release, the service supports migrations from NFS shares on a NAS or server device in your network to an Azure blob container.
    -->

    The current Azure Storage Mover release supports migrations from NFS shares on a NAS or server device within your network to an Azure blob container.

    > [!IMPORTANT]
    > Storage accounts with the [hierarchical namespace service (HNS)](../storage/blobs/data-lake-storage-namespace.md) feature enabled are not supported at this time.
    
    An Azure blob container without the hierarchical namespace service feature doesn’t have a traditional file system. A standard blob container supports “virtual” folders. Files in folders on the source will get their path prepended to their name and placed in a flat list in the target blob container.
    
<!--Empty folders will be represented by the Storage Mover service as an empty blob in the target. The metadata of the source folder will be persisted in the custom metadata field of this blob, just like files.-->

    Empty folders will be represented by the Storage Mover service as an empty blob in the target. The metadata of the source folder will be persisted in the custom metadata field of this blob, just as they are with files.

  :::column-end:::
:::row-end:::

## Fully managed migrations

<!--
Azure Storage Mover provides a set of management resources that allow you to express your migration plan and retain oversight about migration progress and results for every share you like to migrate.

The [resource hierarchy article](resource-hierarchy.md) has more information about individual Storage Mover resources and how to best use them for your migration.

Create a migration project for every workload you like to migrate. Within a project, define the source, target, and migration settings for each source share your workload depends on. You can remain in full control about when to start the migration of a share, track it's progress, and see its results. 

A single storage mover resource, deployed to your subscription, can be used to manage migrations for source shares located in different parts of the world. The storage mover resource does not process your files and folders. Your data is sent directly from the migration agent to your selected targets in Azure. The [planning for an Azure Storage Mover deployment](deployment-planning.md) article has more details.
-->

A single storage mover resource deployed to your subscription can be used to manage migrations for your source shares located in different parts of the world. The storage mover resource itself doesn't process your files and folders. Rather, you'll deploy a migration agent near your source share to send your data directly to the selected targets in Azure.

Azure Storage Mover provides a set of management resources that can be used across every share you intend to migrate. For example, you can express your migration plan and retain oversight about migration progress and results on a per-share basis. To take advantage of this capability, create a migration project for every workload you'll migrate. Within the project, define the source, target, and migration settings for each source share on which your workload depends. You can remain in full control about when to start the migration of a share, track it's progress, and see its results.

The [resource hierarchy article](resource-hierarchy.md) has more information about individual Storage Mover resources and how best to use them for your migration. You can also get more deployment planning details in the [planning for an Azure Storage Mover deployment](deployment-planning.md) article.

## A hybrid cloud service

[!INCLUDE [hybrid-service-explanation](includes/hybrid-service-explanation.md)]

## Next steps
<!-- 
These articles can help you become more familiar with the Storage Mover service.
-->

The following articles can help you become more familiar with the Storage Mover service.

- [Planning for an Azure Storage Mover deployment](deployment-planning.md)
- [Understanding the Storage Mover resource hierarchy](resource-hierarchy.md)
- [Deploying a Storage Mover agent](agent-deploy.md)
