---
title: Actions and attributes for Azure role assignment conditions for Azure Queue Storage
titleSuffix: Azure Storage
description: Supported actions and attributes for Azure role assignment conditions and Azure attribute-based access control (Azure ABAC) for Azure Queue Storage. 
services: storage
author: jimmart-dev

ms.service: storage
ms.topic: conceptual
ms.date: 03/27/2023
ms.author: jammart
ms.reviewer: nachakra
ms.subservice: queues
---

# Actions and attributes for Azure role assignment conditions for Azure Queue Storage

This article describes the supported attribute dictionaries that can be used in conditions on Azure role assignments for each Azure Storage [DataAction](../../role-based-access-control/role-definitions.md#dataactions). For the list of Queue service operations that are affected by a specific permission or DataAction, see [Permissions for Queue service operations](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-queue-service-operations).

To understand the role assignment condition format, see [Azure role assignment condition format and syntax](../../role-based-access-control/conditions-format.md).

[!INCLUDE [storage-abac-preview](../../../includes/storage-abac-preview.md)]

## Azure Queue Storage actions

This section lists the supported Azure Queue Storage actions you can target for conditions.

Storage accounts support the following actions:

| Display name | DataAction |
| :--- | :--- |
| [Peek messages](#peek-messages) | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` |
| [Put a message](#put-a-message) | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action` |
| [Put or update a message](#put-or-update-a-message) | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/write` |
| [Clear messages](#clear-messages) | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete` |
| [Get or delete messages](#get-or-delete-messages) | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

### Peek messages

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Peek messages |
> | **Description** | DataAction for peeking messages. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Queue name](#queue-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### Put a message

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Put a message |
> | **Description** | DataAction for putting a message. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Queue name](#queue-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### Put or update a message

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Put or update a message |
> | **Description** | DataAction for putting or updating a message. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/write` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Queue name](#queue-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### Clear messages

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Clear messages |
> | **Description** | DataAction for clearing messages. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Queue name](#queue-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |
Azure queues
### Get or delete messages

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Get or delete messages |
> | **Description** | DataAction for getting or deleting messages. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Queue name](#queue-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

## Azure Queues Storage attributes

This section lists the Azure Queue storage attributes you can use in your condition expressions depending on the action you target. If you select multiple actions for a single condition, there might be fewer attributes to choose from for your condition because the attributes must be available across all of the selected actions.

> [!NOTE]
> Attributes and values listed are considered case-insensitive, unless stated otherwise.

The following table summarizes the available attributes by source:

| Attribute Source | Display name         | Description                                                        |
| :--------------- | :------------------- | :----------------------------------------------------------------- |
| **Environment**  | | |
| | [Is private link](#is-private-link)   | Whether access is over a private link.                             |
| | [Private endpoint](#private-endpoint) | The name of the private endpoint from which an object is accessed. |
| | [Subnet name](#subnet-name)           | The name of the subnet from which an object is accessed.           |
| | [UTC now](#utc-now)                   | The current date and time in Coordinated Universal Time.           |
| **Resource**      | | |
| | [Account name](#account-name)         | The storage account name.                                          |
| | [Queue name](#queue-name)             | The storage queue name.                                            |

### Account name

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Account name |
> | **Description** | Name of a storage account. |
> | **Attribute** | `Microsoft.Storage/storageAccounts:name` |
> | **Attribute source** | Resource |
> | **Attribute type** | String |
> | **Examples** | `@Resource[Microsoft.Storage/storageAccounts:name] StringEquals 'sampleaccount'` |

### Is private link

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Is private link |
> | **Description** | Whether access is over a private link.<br/>Use to require access over any private endpoint. |
> | **Attribute** | `isPrivateLink` |
> | **Attribute source** | [Environment](../../role-based-access-control/conditions-format.md#environment-attributes) |
> | **Attribute type** | [Boolean](../../role-based-access-control/conditions-format.md#boolean-comparison-operators) |
> | **Examples** | `@Environment[isPrivateLink] BoolEquals true`<br/>[Example: Allow read access to blobs based on private link and tags](../blobs/storage-auth-abac-examples.md#example-allow-read-access-to-blobs-based-on-private-link-and-tags) |
> | **Learn more** | [Use private endpoints for Azure Storage](../common/storage-private-endpoints.md) |

### Private endpoint

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Private endpoint |
> | **Description** | The name of the private endpoint from which an object is accessed.<br/>Use to restrict access over a specific private endpoint. |
> | **Attribute** | `Microsoft.Network/privateEndpoints` |
> | **Attribute source** | [Environment](../../role-based-access-control/conditions-format.md#environment-attributes) |
> | **Attribute type** | [String](../../role-based-access-control/conditions-format.md#string-comparison-operators) |
> | **Examples** | `@Environment[Microsoft.Network/privateEndpoints] StringEqualsIgnoreCase '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/example-group/providers/Microsoft.Network/privateEndpoints/privateendpoint1'`<br/>[Example: Allow read access to a container only from a specific private endpoint](../blobs/storage-auth-abac-examples.md#example-allow-read-access-to-a-container-only-from-a-specific-private-endpoint) |
> | **Learn more** | [Use private endpoints for Azure Storage](../common/storage-private-endpoints.md) |

### Queue name

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Queue name |
> | **Description** | Name of a storage queue. |
> | **Attribute** | `Microsoft.Storage/storageAccounts/queueServices/queues:name` |
> | **Attribute source** | Resource |
> | **Attribute type** | String |

### Subnet name

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Subnet name |
> | **Description** | The name of the subnet from which an object is accessed.<br/>Use to restrict access to a specific subnet. |
> | **Attribute** | `Microsoft.Network/virtualNetworks/subnets` |
> | **Attribute source** | [Environment](../../role-based-access-control/conditions-format.md#environment-attributes) |
> | **Attribute type** | [String](../../role-based-access-control/conditions-format.md#string-comparison-operators) |
> | **Examples** | `@Environment[Microsoft.Network/virtualNetworks/subnets] StringEqualsIgnoreCase '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/example-group/providers/Microsoft.Network/virtualNetworks/virtualnetwork1/subnets/default'`<br/>[Example: Allow read access to blobs based on a subnet and tags](../blobs/storage-auth-abac-examples.md#example-allow-read-access-to-blobs-based-on-a-subnet-and-tags) |
> | **Learn more** | [Subnets](../../virtual-network/concepts-and-best-practices.md) |

### UTC now

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | UTC now |
> | **Description** | The current date and time in Coordinated Universal Time.<br/>Use to control access to objects for a specific date and time period. |
> | **Attribute** | `UtcNow` |
> | **Attribute source** | [Environment](../../role-based-access-control/conditions-format.md#environment-attributes) |
> | **Attribute type** | [DateTime](../../role-based-access-control/conditions-format.md#datetime-comparison-operators)</br> *(Only operators* ***DateTimeGreaterThan*** *and* ***DateTimeLessThan*** *are supported for the UTC now attribute.)* |
> | **Examples** | `@Environment[UtcNow] DateTimeGreaterThan '2023-03-29T22:03:00.0Z'`<br/>[Example: Allow read access to blobs after a specific date and time](../blobs/storage-auth-abac-examples.md#example-allow-read-access-to-blobs-after-a-specific-date-and-time) |

## See also

- [Example Azure role assignment conditions](../blobs\storage-auth-abac-examples.md)
- [Azure role assignment condition format and syntax](../../role-based-access-control/conditions-format.md)
- [Troubleshoot Azure role assignment conditions](../../role-based-access-control/conditions-troubleshoot.md)