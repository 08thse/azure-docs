---
title: How to upgrade the Redis version of Azure Cache for Redis
description: Learn how to upgrade the version of Azure Cache for Redis.
author: flang-msft

ms.author: franlanglois
ms.service: cache
ms.topic: how-to
ms.date: 08/17/2023
ms.custom: template-how-to, devx-track-azurecli
---

# How to upgrade the version of your Redis instance

New versions of Redis server software are frequently released with new features, more commands, and stability improvements. Maintaining Redis instances using the latest version of Redis is a good way to ensure that you get the best possible Redis experience.

This article details how to upgrade your Redis instance to the latest version of Redis available in Azure Cache for Redis.

> [!IMPORTANT]
> Following the [standard Redis versioning](https://redis.io/docs/about/releases/), this article only covers upgrades to the _major_ version of Redis, not the _minor_ or _patch_ versions. Updates to the minor and patch versions are made automatically during the normal patching cycle each month.
>

## Scope of availability

| **Tier**         | Basic, Standard, Premium                                | Enterprise, Enterprise Flash |
|:--------------------------|:----------------------------------------------:|:----------------------------:|
| **Latest Redis Version** | Redis 6.0 (GA)                                 | Redis 6.2 (GA) / Redis 7.2 (Preview)|
| **Upgrade Policy** | Manual upgrade to newer version     | Automatic upgrade to latest GA version         |

## How to upgrade - Basic, Standard, and Premium tiers

Currently, no upgrade is available.

## How to upgrade - Enterprise and Enterprise Flash tiers

> [!NOTE]
> Updates in the [Redis Enterprise software version](https://docs.redis.com/latest/rs/release-notes/) are done automatically as a part of the normal monthly patching process.
>

### Automatic upgrade

Redis server version upgrades are made automatically as a part of the standard monthly patching process. Upgrades to the latest version of Redis occur once that Redis version reaches general availability (GA) on Azure.

Your Redis instance is automatically upgraded to a preview version of Redis software.

### Trigger an upgrade manually

As an alternative to automatic upgrade, you can manually start an upgrade to the latest version of Redis software. Manual upgrades provides two other benefits instead waiting for the automatic upgrade to occur:

- You control when the upgrade occurs.
- You can upgrade to preview releases of Redis server.

1. In the portal, navigate to the **Overview** of the cache using the Resource menu. Then, choose **Upgrade** to start an upgrade.

    <!--- Fran-need a screenshot here of the overview blade--->
    :::image type="icon" source="media/cache-how-to-upgrade/cache-upgrade-overview.png" alt-text="Screenshot showing the upgrade pane, the current version and the available version. :::

1. After you select the button, you see an **Upgrade** pane that shows you the your current Redis version, and any version that you can upgrade to. As noted in the pane, upgrading is irreversible. You cannot downgrade. To confirm and begin the upgrade process, select **Upgrade** in the pane.

    > [!WARNING]
    > Once your Redis instance has been upgraded, it cannot be downgraded to the previous version.
    >
    <!--- Fran-need a screenshot here of the upgrade blade--->
    :::image type="content" source="media/cache-how-to-upgrade/cache-upgrade-pane.png" alt-text="Screenshot showing overview selected in the resource menu and the Essentials of the cache.":::

    If you're already running the latest version of Redis software available, the **Upgrade** button is disabled.

### Defer Upgrades

You can defer an automatic upgrade when a new version of Redis software is available and GA. You can defer automatic upgrades by as many as 90 days. This option gives you time to test new versions and ensure that everything works as expected. The cache is then upgraded either 90 days after the new Redis version reaches GA, or whenever you trigger the upgrade manually.

To defer upgrades to your cache, navigate to the **Advanced Settings** on the Resource menu, and select the **Defer Redis DB version updates** box.
    <!--- Fran-need a screenshot here--->
:::image type="content" source="media/cache-how-to-upgrade/cache-defer-upgrade.png" alt-text="Screenshot showing Advanced settings selected in the Resource menu and a red box around Defer Redis DB version updates.":::
> [!IMPORTANT]
> Selecting the option to defer upgrades only applies to the next automatic upgrade event. Caches that have already been upgraded cannot be downgraded using the defer option.
>

## Considerations before upgrading Redis versions

Each new Redis version is intended to be a seamless upgrade from previous versions with backwards-compatibilty as a design principle. However, small changes and bug fixes do occur which can cause application changes. Being conscious of these changes is always a good idea.

### Client Version

If you're using an outdated Redis client, new commands or Redis features can't be supported properly. We always recommend updating to the latest stable version of your Redis client, as newer versions often have stability and performance improvements as well. For more information on configuring your client library, see [best practices using client libraries](cache-best-practices-client-libraries.md).

### RESP3

Redis version 7.2 enables an updated Redis serialization protocol specification (RESP) called [RESP3](https://redis.io/docs/reference/protocol-spec/). This protocol offers richer data types and performance improvements. Using RESP3 is optional and is negotiated by the Redis client. Because some Redis clients, such as [Go-Redis](https://github.com/redis/go-redis) version 9+ and [Lettuce](https://github.com/lettuce-io/lettuce-core) version 6+, enable RESP3 by default, upgrading the Redis server instance to version 7.2 can produce a response with a different format. To avoid this breaking change, you can [configure these clients to use RESP2](https://docs.redis.com/latest/rs/references/compatibility/resp/) by default instead.

### Breaking Changes

Each version of Redis often has a few minor bug fixes that can present breaking changes. If you have concerns, we recommend reviewing the Redis 7.0 and 7.2 release notes before upgrading your Redis version:

- [Redis 7.0 release notes](https://raw.githubusercontent.com/redis/redis/7.0/00-RELEASENOTES)
- [Redis 7.2 release notes](https://raw.githubusercontent.com/redis/redis/7.2/00-RELEASENOTES)

<!---# How to upgrade an existing Redis 4 cache to Redis 6

Azure Cache for Redis supports upgrading the version of your Azure Cache for Redis from Redis 4 to Redis 6. Upgrading is similar to regular monthly maintenance. Upgrading follows the same pattern as maintenance: First, the Redis version on the replica node is updated, followed by an update to the primary node. Your client application should treat the upgrade operation exactly like a planned maintenance event.

As a precautionary step, we recommend exporting the data from your existing Redis 4 cache and testing your client application with a Redis 6 cache in a lower environment before upgrading.

For more information on how to export, see [Import and Export data in Azure Cache for Redis](cache-how-to-import-export-data.md).

> [!IMPORTANT]
> As announced in [What's new](cache-whats-new.md#upgrade-your-azure-cache-for-redis-instances-to-use-redis-version-6-by-june-30-2023), we'll retire version 4 for Azure Cache for Redis instances on June 30, 2023. Before that date, you need to upgrade any of your cache instances to version 6.
>
> For more information on the retirement of Redis 4, see [Retirements](cache-retired-features.md) and [Frequently asked questions](cache-retired-features.md#redis-4-retirement-questions)
>

## Prerequisites

- Azure subscription - [create one for free](https://azure.microsoft.com/free/)

### Limitations

- When you upgrade a cache in the Basic tier, it's unavailable for several minutes and results in data loss.
- Upgrading on geo-replicated cache isn't supported. You must manually unlink the cache instances before upgrading.
- Upgrading a cache with a dependency on Cloud Services isn't supported. You should migrate your cache instance to Virtual Machine Scale Set before upgrading.
  - For more information, see [Caches with a dependency on Cloud Services (classic)](./cache-faq.yml) for details on cloud services hosted caches.
- When trying to upgrade to Redis 6, your VNet must be configured in accord with the requirements. Your upgrade might fail or the cache might not function properly after the upgrade if not configured correctly.
  - For more information on the VNet requirements, see [What are some common misconfiguration issues with Azure Cache for Redis and virtual networks](cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks).

### Check the version of a cache

Before you upgrade, check the Redis version of a cache by selecting **Properties** from the Resource menu of the Azure Cache for Redis. We recommend you use Redis 6.

:::image type="content" source="media/cache-how-to-upgrade/cache-version-portal.png" alt-text="Screenshot of properties selected in the Resource menu.":::

## Upgrade using the Azure portal

1. In the Azure portal, select the Azure Cache for Redis instance that you want to upgrade from Redis 4 to Redis 6.

1. On the left side of the screen, select **Advanced settings**.

1. If your cache instance is eligible to be upgraded, you should see the following blue banner. If you want to proceed, select the text in the banner.

    :::image type="content" source="media/cache-how-to-upgrade/blue-banner-upgrade-cache.png" alt-text="Screenshot informing you that you can upgrade your cache to Redis 6 with more features. Upgrading your cache instance can't be reversed.":::

1. A dialog box displays a popup notifying you that upgrading is permanent and might cause a brief connection blip. Select **Yes** if you would like to upgrade your cache instance.

    :::image type="content" source="media/cache-how-to-upgrade/dialog-version-upgrade.png" alt-text="Screenshot showing a dialog with more information about upgrading your cache with Yes selected.":::

1. To check on the status of the upgrade, navigate to **Overview**.

    :::image type="content" source="media/cache-how-to-upgrade/upgrade-status.png" alt-text="Screenshot showing Overview in the Resource menu. Status shows cache is being upgraded.":::

## Upgrade using Azure CLI

To upgrade a cache from 4 to 6 using the Azure CLI that is not using Private Endpoint, use the following command. 

```azurecli-interactive
az redis update --name cacheName --resource-group resourceGroupName --set redisVersion=6
```

### Private Endpoint

If Private Endpoint is enabled on the cache, use the command that is appropriate based on whether `PublicNetworkAccess` is enabled or disabled:

If `PublicNetworkAccess` is enabled:

```azurecli
 az redis update --name <cacheName> --resource-group <resourceGroupName> --set publicNetworkAccess=Enabled redisVersion=6
```

If `PublicNetworkAccess` is disabled:

```azurecli
az redis update --name <cacheName> --resource-group <resourceGroupName> --set publicNetworkAccess=Disabled redisVersion=6
```

## Upgrade using PowerShell

To upgrade a cache from 4 to 6 using PowerShell, use the following command:

```powershell-interactive
Set-AzRedisCache -Name "CacheName" -ResourceGroupName "ResourceGroupName" -RedisVersion "6"
```
--->

## Related content

- To learn more about Azure Cache for Redis features: [Azure Cache for Redis service tiers](cache-overview.md#service-tiers)
