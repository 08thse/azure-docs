---
title: We're retiring classic storage accounts on August 31, 2024
titleSuffix: Azure Storage
description: Overview - migrate your classic storage accounts to the Azure Resource Manager deployment model. All classic accounts must be migrated by August 31, 2024.
services: storage
author: tamram

ms.service: storage
ms.topic: how-to
ms.date: 03/17/2023
ms.author: tamram
ms.subservice: common
---

# Migrate your classic storage accounts to Azure Resource Manager by August 31, 2024

The [Azure Resource Manager](../../azure-resource-manager/management/overview.md) deployment model now offers extensive functionality for Azure Storage accounts. For this reason, we deprecated the management of classic storage accounts through Azure Service Manager (ASM) on August 31, 2021. Classic storage accounts will be fully retired on August 31, 2024. All data in classic storage accounts must be migrated to Azure Resource Manager storage accounts by that date.

Today, about x??? percent of Azure storage accounts are using Azure Resource Manager. If you have classic storage accounts, start planning your migration now. Complete it by August 31, 2024, to take advantage of Azure Resource Manager. To learn more about the benefits of Azure Resource Manager, see [The benefits of using Resource Manager](../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager).

Storage accounts created using the classic deployment model will follow the [Modern Lifecycle Policy](https://support.microsoft.com/help/30881/modern-lifecycle-policy) for retirement.

## How does this affect me?

- Subscriptions created after August 31, 2022 can no longer create classic storage accounts.
- Subscriptions created before September 1, 2022 will be able to create classic storage accounts until September 1, 2023.
- On September 1, 2024, customers will no longer be able to connect to classic storage accounts by using Azure Service Manager. Any data still contained in these accounts will no longer be accessible through Azure Service Manager.

> [!WARNING]
> If you do not migrate your classic storage accounts to Azure Resource Manager by August 31, 2024, you will permanently lose access to the data in those accounts.

## What resources are available for this migration?

- If you have questions, get answers from community experts in [Microsoft Q&A](/answers/tags/98/azure-storage-accounts).
- If your organization or company has partnered with Microsoft or works with Microsoft representatives, such as cloud solution architects (CSAs) or customer success account managers (CSAMs), contact them for additional resources for migration.
- If you have a support plan and you need technical help, create a support request in the Azure portal:

    1. Search for **Help + support** in the [Azure portal](https://portal.azure.com#view/Microsoft_Azure_Support/HelpAndSupportBlade/~/overview).
    1. Select **Create a support request**.
    1. Under **Summary**, type a description of your issue.
    1. Under **Issue type**, select **Technical**.
    1. Under **Subscription**, select your subscription.
    1. Under **Service**, select **My services**.
    1. Under **Service type**, select **Storage Account Management**.
    1. Under **Resource**, select the resource you want to migrate.
    1. Under **Problem type**, select **Data Migration**.
    1. Under **Problem subtype**, select **Migrate account to new resource group/subscription/region/tenant**.
    1. Select **Next**, then follow the instructions to submit your support request.

## What actions should I take?

To migrate your classic storage accounts, you should:

1. Identify all classic storage accounts in your subscription(s).
1. Migrate any classic storage accounts to Azure Resource Manager.
1. Check your applications and logs to determine whether you are dynamically creating, updating, or deleting classic storage accounts from your code, scripts, or templates. If you are, then you need to update your applications to use Azure Resource Manager accounts instead.

For step-by-step instructions, see [Migrate a classic storage account to Azure Resource Manager](storage-account-migrate-classic.md).

## Unsupported features and known issues

- [Customer-managed failover](https://supportability.visualstudio.com/AzureIaaSVM/_wiki/wikis/AzureIaaSVM/496078/Storage-Account-Failover_Storage?anchor=unsupported-storage-accounts)
- Challenges locating classic OS disks or images - https://supportability.visualstudio.com/AzureIaaSVM/_wiki/wikis/AzureIaaSVM/496154/Locate-Classic-OS-Disks-VM-Images-in-Storage-Account_Storage
- Classic artifacts blocking storage account deletion - https://supportability.visualstudio.com/AzureIaaSVM/_wiki/wikis/AzureIaaSVM/496138/Delete-Classic-Storage-Account-Portal_Storage
- Customers that only have internal billable Classic Storage and have no visibility into those resources and no ability to migrate those resources to ARM - https://supportability.visualstudio.com/AzureIaaSVM/_wiki/wikis/AzureIaaSVM/606146/Classic-Storage-Account-Retirement-Message_Storage 
- Classic StgAcct not showing in the portal - https://supportability.visualstudio.com/AzureIaaSVM/_wiki/wikis/AzureIaaSVM/496225/Storage-Account-Not-Visible-in-Portal-or-PowerShell_Storage

## See also

[Migrate a classic storage account to Azure Resource Manager](storage-account-migrate-classic.md)