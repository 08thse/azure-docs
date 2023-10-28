---
title: Azure Native Qumulo Scalable File Service overview
description: Learn about what Azure Native Qumulo Scalable File Service offers you.

ms.topic: overview
ms.date: 11/13/2023

---

# What is Azure Native Qumulo Scalable File Service?

Azure Native ISV Services enable you to easily provision, manage, and tightly integrate independent software vendor (ISV) software and services on Azure. This Azure Native ISV Service is developed and managed by Microsoft and Qumulo.

You can find Azure Native Qumulo Scalable File Service in the [Azure portal](https://portal.azure.com/#view/HubsExtension/BrowseResource/resourceType/Qumulo.Storage%2FfileSystems) or get it on [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/qumulo1584033880660.qumulo-saas-mpp?tab=Overview).

Qumulo is an industry leader in distributed file system and object storage. Qumulo provides a scalable, performant, and simple-to-use cloud-native file system that can support a wide variety of data workloads. The file system uses standard file-sharing protocols, such as NFS, SMB, FTP, and S3.

The Azure Native Qumulo Scalable File Service offering on Azure Marketplace enables you to create and manage a Qumulo file system by using the Azure portal with a seamlessly integrated experience. You can also create and manage Qumulo resources by using the Azure portal through the resource provider `Qumulo.Storage/fileSystem`. Qumulo manages the service while giving you full admin rights to configure details like file system shares, exports, quotas, snapshots, and Active Directory users.

> [!NOTE]
> Azure Native Qumulo Scalable File Service stores and processes data only in the region where the service was deployed. No data is stored outside that region.

## Versions and capabilities

You can chose from two versions of _Azure Native Qumulo_ (ANQ) Scalable File Service.

- ANQ V2
- ANQ V1

Azure Native Qumulo Scalable File Service provides:

- Seamless onboarding: Easily include Qumulo as a natively integrated service on Azure. The service can be deployed in few minutes.
- Multi-protocol support: ANQ supports all standard file system protocols NFS, SMB, FTP, and S3.
- Unlimited storage scaling: Each qumulo instance can be scaled up to exabytes of storage capacity.
- Unified billing: Get a single bill for all resources that you consume on Azure for the Qumulo service.
- Private access: The service is directly connected to your own virtual network (sometimes called _VNet injection_).
- Global Namespaces: This capability enables all workloads to be pointed to a single namespace regardless of where the data resides Azure Native Qumulo V2 Scalable File Service or on-premises Qumulo instance. This feature is only available with ANQ V2.

ANQ V2 is the latest offering and it provides enhanced capabilities over ANQ 1. The following section provides details of those key capabilities:

| Feature Description | ANQ V2 | ANQ V1 |
| ------------------- | :-----: | :------: |
| Seamless onboarding| Yes | Yes |
| True multi-protocol support| Yes | Yes |
| unlimited storage scaling| Yes | Yes |
| Azure CLI, SDK, API | No | Yes |
| Pay-as-you-pricing| Yes | No |
| support for independent performance scaling| Yes | No |
| global namespaces| Yes| No |

## Next steps

- For more help with using Azure Native Qumulo Scalable File Service, see the [Qumulo documentation](https://docs.qumulo.com/cloud-guide/azure/).
- To get started with the Azure Native Qumulo Scalable File Service, see the [quickstart](qumulo-create.md).
- Get started with Azure Native Qumulo Scalable File Service on

    > [!div class="nextstepaction"]
    > [Azure portal](https://portal.azure.com/#view/HubsExtension/BrowseResource/resourceType/Qumulo.Storage%2FfileSystems)

    > [!div class="nextstepaction"]
    > [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/qumulo1584033880660.qumulo-saas-mpp?tab=Overview)
