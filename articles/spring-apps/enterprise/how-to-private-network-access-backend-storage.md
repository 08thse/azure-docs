---
title:  Configure private network access for backend storage in your virtual network (Preview)
description: Learn how to configure private network access to backend storage in your virtual network
author: KarlErickson
ms.author: haozhan
ms.service: spring-apps
ms.topic: how-to
ms.date: 03/10/2024
ms.custom: devx-track-java, devx-track-extended-java, devx-track-azurecli
---

# Configure private network access for backend storage in your virtual network (Preview)

> [!NOTE]
> Azure Spring Apps is the new name for the Azure Spring Cloud service. Although the service has a new name, you'll see the old name in some places for a while as we work to update assets such as screenshots, videos, and diagrams.

**This article applies to:** ✔️ Standard ✔️ Enterprise

This article explains how to configure private network access to backend storage for your application within your virtual network.

When you deploy an application in an Azure Spring Apps service instance with VNet injection, the service instance relies on backend storage for housing associated assets, including JAR files and logs. While the default configuration routes traffic to this backend storage over the public network, you can turn on the private storage access feature. This allows you to direct the traffic through your private network, enhancing security and potentially improving performance.

## Prerequisites

- An Azure subscription. If you don't have a subscription, create a free account before you begin.
- Azure CLI version 2.56.0 or higher.
- An existing Azure Spring Apps service instance deployed to a virtual network. For more information, see [Deploy Azure Spring Apps in a virtual network](./how-to-deploy-in-azure-virtual-network.md).

## Limitations

- This feature applies to Azure Spring Apps VNet injected service instance only.
- Before enabling this feature for your Azure Spring Apps service instance, ensure that there are at least two available IP addresses in the service runtime subnet.
- Enabling or disabling this feature changes the way of DNS resolution to the backend storage. You may experience a short period of time that deployments fail to establish connection to the backend storage or cannot resolve its endpoint during the update.
- After enabling this feature, the backend storage is only privately accessible, so you have to deploy your application within the virtual network.

## Enable Private Storage Access when creating a new Azure Spring Apps instance

When you [create an Azure Spring Apps instance in the virtual network](./how-to-deploy-in-azure-virtual-network.md), you can pass the argument `--enable-private-storage-access true` to enable private storage access.

```azurecli
az spring create \
    --resource-group "<resource-group>" \
    --name "<azure-spring-apps-instance-name>" \
    --vnet "<virtual-network-name>" \
    --service-runtime-subnet "<service-runtime-subnet>" \
    --app-subnet "<apps-subnet>" \
    --location "<location>" \
    --enable-private-storage-access true
```

One more resource group is created in your subscription to host the private link resources for the Azure Spring Apps instance. The resource group is named as `ap-res_{service instance name}_{service instance region}`.

   :::image type="content" source="media/how-to-private-network-access-backend-storage/ap-res-group.png" alt-text="Screenshot of the Azure portal Resource Group page showing the private link resource details." lightbox="media/how-to-private-network-access-backend-storage/ap-res-group.png":::

There are two sets of private link resources being deployed in the resource group, each comprising the following Azure resources:
- A private endpoint represents the backend storage account's private endpoint.
- A network interface (NIC) maintains a private IP address within the service runtime subnet.
- A private DNS zone is deployed for your virtual network, with a DNS A record also created for the storage account within this DNS zone.

> [!IMPORTANT]
> The resource groups are fully managed by the Azure Spring Apps service. Do *not* manually delete or modify any resource inside.

## Enable or Disable Private Storage Access for an existing Azure Spring Apps instance

Use the following command to update an existing Azure Spring Apps instance to enable or disable private storage access.

```azurecli
az spring update \
    --resource-group "<resource-group>" \
    --name "<azure-spring-apps-instance-name>" \
    --enable-private-storage-access true/false
```

> [!IMPORTANT]
> While enabling or disabling this feature, the way of DNS resolution to the backend storage will change. You may experience a short period of time that deployments fail to establish connection to the backend storage or cannot resolve its endpoint.

> [!IMPORTANT]
> After enabling this feature, the backend storage is only privately accessible, so you have to deploy your application within the virtual network.

## Additional Costs

The Azure Spring Apps instance does not incur charges for this feature. However, you will be billed for the private link resources hosted in your subscription that support this feature. For more details, refer to the [Azure Private Link Pricing](https://azure.microsoft.com/pricing/details/private-link/) and [Azure DNS Pricing](https://azure.microsoft.com/pricing/details/dns/).

## Using Custom DNS Servers

If you are using a custom DNS server and the Azure DNS IP `168.63.129.16` is not configured as the upstream DNS server, you should manually bind all the DNS records of private DNS zones shown in the resource group `ap-res_{service instance name}_{service instance region}` to resolve the private IP addresses.

## Next steps

- [Customer Responsibilities for Running Azure Spring Apps in VNET](vnet-customer-responsibilities.md)