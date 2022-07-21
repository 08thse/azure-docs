---
title: Ensure reliability of your Azure API Management instance
titleSuffix: Azure API Management
description: Learn how to use Azure reliability features including availability zones and multiregion deployments to make your Azure API Management service instance resilient to cloud failures.
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 07/20/2022
ms.author: danlep
---

# Ensure API Management availability and reliability


This article introduces service capabilities and considerations to ensure that your API Management instance continues to serve API requests if Azure outages occur. 

API Management supports the following key service capabilities that are recommended for [reliable](../availability-zones/overview.md) Azure solutions. Use them individually, or together, to improve the availability of your API Management solution:

* **Availability zones**, to provide resilience to datacenter-level outages

* **Multi-region deployment**, to provide resilience to regional outages

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## Availability zones

Azure [availability zones](../availability-zones/az-overview.md) are physically separate locations within an Azure region that are tolerant to datacenter-level failures. Each zone is composed of one or more datacenters equipped with independent power, cooling, and networking infrastructure. To ensure resiliency, a minimum of three separate availability zones are present in all availability zone-enabled regions.  


Enabling [zone redundancy](../availability-zones/migrate-api-mgt.md) for an API Management instance in a supported region provides redundancy for all [service components](api-management-key-concepts.md#api-management-components): gateway, management plane, and developer portal. Azure automatically replicates all service components across the zones that you select. 

When you enable zone redundancy in a region, consider the number of API Management scale [units](upgrade-and-scale.md) that need to be distributed. Minimally, configure the same number of units as the number of availability zones, or a multiple so that the units are distributed evenly across the zones. For example, if you select 3 availability zones in a region, you could have 3 units so that each zone hosts one unit.

> [!NOTE]
> Use the [capacity](api-management-capacity.md) metric and your own testing to decide on the number of scale units that will provide the gateway performance for your needs. Learn more about [scaling and upgrading](upgrade-and-scale.md) your service instance.

## Multi-region deployment

With [multi-region deployment](api-management-howto-deploy-multi-region.md), you add regional gateways to your existing API Management instance in any number of supported Azure regions. The service instance's management plane and developer portal remain hosted only in the *primary* region, the region where you originally deployed the service. Gateway configurations such as APIs and policy definitions are regularly synchronized between the primary and secondary regions you add.


* Multi-region deployment ensures the availability of the API gateway in more than one region and provides service availability if one region goes offline.

* API Management routes API requests to regional gateways based on lowest latency, which can reduce latency experienced by geographically distributed API consumers.

* If a region goes offline, API requests are automatically routed around the failed region to the next closest gateway.

* If the primary region goes offline, the API Management management plane and developer portal become unavailable, but secondary regions continue to serve API requests using the most recent gateway configuration. 

## Combine availability zones and multi-region deployment

The combination of availability zones for redundancy within a region, and multi-region deployments to improve the gateway availability if there is a regional outage, helps enhance both the reliability and performance of your API Management instance.

Examples:

* Use availability zones to improve the resilience of the primary region in a multi-region deployment

* Distribute scale units across availability zones and regions to enhance regional gateway performance


## SLA considerations

API Management provides an SLA of 99.99% when you deploy at least one unit in two or more availability zones or regions. For more information, see [Pricing](https://azure.microsoft.com/pricing/details/api-management/).

> [!NOTE]
> While Azure continually strives for highest possible resiliency in SLA for the cloud platform, you must define your own target SLAs for other components of your solution.

## Backend availability

Depending on where and how your backend services are hosted, you may need to set up redundant backends in different regions to meet your requirements for service availability. You can manage regional backends and handle failover through API Management to maintain availability. For example:  

* In multi-region deployments, use [policies to route requests](api-management-howto-deploy-multi-region.md#-route-api-calls-to-regional-backend-services) through regional gateways to regional backends. 

* Configure policies to route requests conditionally to different backends if there is backend failure in a particular region.

* Use caching to reduce failing calls.

For details, see the blog post [Back-end API redundancy with Azure API Manager](https://devblogs.microsoft.com/premier-developer/back-end-api-redundancy-with-azure-api-manager/).

## Next steps

* Learn more about [resiliency in Azure](../availability-zones/overview.md)
* Learn more about [designing reliable Azure applications](/azure/architecture/framework/resiliency/app-design)
* Read [API Management and reliability](/azure/architecture/framework/services/networking/api-management/reliability) in the Azure Well-Architected Framework
