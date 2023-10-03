---
title: Reliability in Azure Load Balancer
description: Find out about reliability in Azure Load Balancer
author: anaharris-ms
ms.author: anaharris
ms.topic: conceptual
ms.custom: subject-reliability
ms.service: virtual-machines
ms.date: 10/03/2023
---

# Reliability in Load Balancer

This article contains [specific reliability recommendations](#reliability-recommendations) for [Load Balancer](/azure/load-balancer/load-balancer-overview), as well as detailed information on regional resiliency with [availability zones](#availability-zone-support) and [cross-region disaster recovery and business continuity](#cross-region-disaster-recovery-and-business-continuity). 


For an architectural overview of reliability in Azure, see [Azure reliability](/azure/architecture/framework/resiliency/overview).


## Reliability recommendations

[!INCLUDE [Reliability recommendations](includes/reliability-recommendations-include.md)]
 
### Reliability recommendations summary

| Category | Priority |Recommendation |  
|---------------|--------|---|
| [**High Availability**](#high-availability) |:::image type="icon" source="media/icon-recommendation-high.svg":::| [Use Standard Load Balancer SKU](#use-standard-load-balancer-sku) |
||:::image type="icon" source="media/icon-recommendation-high.svg"::: |[Ensure that the backend pool contains at least two instances](#ensure-that-the-backend-pool-contains-at-least-two-instances) | 
||:::image type="icon" source="media/icon-recommendation-medium.svg":::|[Use NAT Gateway instead of outbound rules for production workloads](#use-nat-gateway-instead-of-outbound-rules-for-production-workloads) | 

### High availability
 
#### :::image type="icon" source="media/icon-recommendation-high.svg"::: **Use Standard Load Balancer SKU**

Standard SKU Load Balancer supports availability zones and zone resiliency, while the Basic SKU doesn't. When a zone goes down, your zone-redundant Standard Load Balancer will not be impacted and your deployments are able to withstand zone failures within a region. In addition, Standard Load Balancer supports cross region load balancing to ensure that your application isn't impacted by region failures. 

>[!NOTE]
> Basic load balancers don’t have a Service Level Agreement (SLA).

# [Azure Resource Graph](#tab/graph)

:::code language="kusto" source="~/azure-proactive-resiliency-library/docs/content/services/networking/load-balancer/code/lb-1/lb-1.kql":::

----

#### :::image type="icon" source="media/icon-recommendation-high.svg"::: **Ensure that the backend pool contains at least two instances**

Deploy Load Balancer with at least two instances in the backend. A single instance could result in a single point of failure. In order to build for scale, you might want to pair load balancer with [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md).


# [Azure Resource Graph](#tab/graph)

:::code language="kusto" source="~/azure-proactive-resiliency-library/docs/content/services/networking/load-balancer/code/lb-2/lb-2.kql":::

----

#### :::image type="icon" source="media/icon-recommendation-medium.svg"::: **Use NAT Gateway instead of outbound rules for production workloads**

Outbound rules ensure that you're not faced with connection failures as a result of SNAT port exhaustion. While outbound rules help improve the solution for small to mid size deployments, for production workloads, it's recommended that you couple Standard Load Balancer or any subnet deployment with VNet NAT.


# [Azure Resource Graph](#tab/graph)

:::code language="kusto" source="~/azure-proactive-resiliency-library/docs/content/services/networking/load-balancer/code/lb-3/lb-3.kql":::

----


## Availability zone support

[!INCLUDE [Availability zone description](includes/reliability-availability-zone-description-include.md)]

Azure Load Balancer supports availability zones scenarios. You can use Load Balancer to increase availability throughout your scenario by aligning resources with, and distribution across zones. 

A Load Balancer can be deployed as zone redundant, zonal, or non-zonal. To configure Load Balancer for availability zones, you'll need to select the appropriate type of Frontend IP Configuration.


> [!NOTE]
> It isn't required to have a load balancer for each zone, rather having a single load balancer with multiple frontends (zonal or zone redundant) associated to their respective backend pools will serve the purpose. 

### Prerequisites

- To use availability zones with Load Balancer, you need to create your load balancer in a region that supports availability zones.  To review which regions support availability zones, see the [list of supported regions](availability-zones-service-support.md#azure-regions-with-availability-zone-support). 

- Use Standard SKU for load balancer and Public IP for availability zones support.

- Basic SKU type isn't supported. 

- To create your resource, you need to have Network Contributor role or higher.

### Zone redundant Frontend IP Configuration

When the resources (VMs, NICs, IP addresses, etc.) inside a backend pool are distributed across zones, then it's recommended that you create a zone-redundant frontend. 

A zone-redundant frontend survives zone failure by using dedicated infrastructure in all the zones simultaneously. One or more availability zones can fail, and the data path survives as long as one zone in the region remains healthy. 

To deploy a zone-redundant load balancer, you'll need to select a single frontend IP that will be used to reach all backend pool members that are in healthy zones. 

When one or more availability zones fail, the data path survives as long as one zone in the region remains healthy. The frontend's IP address is served simultaneously by multiple independent infrastructure deployments in multiple availability zones. Any retries or reestablishment will succeed in the zones that aren't affected by the zone failure.

:::image type="content" source="../load-balancer/media/az-zonal/zone-redundant-lb-1.svg" alt-text="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration.":::

Members in the backend pool of a load balancer are normally associated with a single zone such as with zonal virtual machines. A common design for production workloads would be to have multiple zonal resources. For example, placing virtual machines from zone 1, 2, and 3 in the backend of a load balancer with a zone-redundant frontend meets this design principle.


### Zonal Frontend IP Configuration

It's recommended that you create a zonal frontend when the backend is concentrated in a particular zone. For example, if backend instances are pinned to availability zone 2, then it makes sense to create Frontend IP configuration in availability zone 2.

To deploy a zonal load balancer, you'll need to select a frontend guaranteed to a single zone that serves all inbound or outbound flow.  Unlike zone-redundant deployment, your frontend IP will be unavailable if the zone should fail. However, the data path is unaffected by failures in zones other than where it is guaranteed. You can use zonal frontends to expose an IP address per availability zone.  

You can also use zonal frontends directly for load-balanced endpoints within each zone. You can use this configuration to expose per zone load-balanced endpoints to individually monitor each zone. For public endpoints, you can integrate them with a DNS load-balancing product like [Traffic Manager](../traffic-manager/traffic-manager-overview.md) and use a single DNS name.


:::image type="content" source="../load-balancer/media/az-zonal/zonal-lb-1.svg" alt-text="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration.":::


### Non-Zonal IP Configuration

Load Balancers can also be created in a non-zonal configuration by use of a "no-zone" frontend. In these scenarios, a public load balancer would use a public IP or public IP prefix, an internal load balancer would use a private IP.  This option doesn't give a guarantee of redundancy. 

>[!NOTE]
>All public IP addresses that are upgraded from Basic SKU to Standard SKU will be of type "no-zone". Learn how to [Upgrade a public IP address in the Azure portal](../virtual-network/ip-services/public-ip-upgrade-portal.md).



### SLA improvements

Because availability zones are physically separate and provide distinct power source, network, and cooling, SLAs (Service-level agreements) can increase. For more information, see the [Service Level Agreements (SLA) for Online Services](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services?lang=1).

#### Create a resource with availability zone enabled

To learn how to load balance VMs within a zone or over multiple zones using a Load Balancer, see [Quickstart: Create a public load balancer to load balance VMs](/azure/load-balancer/quickstart-load-balancer-standard-public-portal). 


>[!NOTE]
> - Zones can't be changed, updated, or created for the resource after creation.
> - Resources can't be updated from zonal to zone-redundant or vice versa after creation.


### Fault tolerance

Virtual machines can fail over to another server in a cluster, with the VM's operating system restarting on the new server. You should refer to the failover process for disaster recovery, gathering virtual machines in recovery planning, and running disaster recovery drills to ensure their fault tolerance solution is successful.

For more information, see the [site recovery processes](../site-recovery/site-recovery-failover.md#before-you-start).


### Zone down experience

Zone-redundancy doesn't imply hitless data plane or control plane. Zone-redundant flows can use any zone and your flows will use all healthy zones in a region. In a zone failure, traffic flows using healthy zones aren't affected.

Traffic flows using a zone at the time of zone failure may be affected but applications can recover. Traffic continues in the healthy zones within the region upon retransmission when Azure has converged around the zone failure.

Review Azure cloud design patterns to improve the resiliency of your application to failure scenarios.

#### Zone outage preparation and recovery

Using multiple frontends allow you to load balance traffic on more than one port and/or IP address. When designing your architecture, ensure you account for how zone redundancy interacts with multiple frontends. If your goal is to always have every frontend resilient to failure, then all IP addresses assigned as frontends must be zone-redundant. If a set of frontends is intended to be associated with a single zone, then every IP address for that set must be associated with that specific zone. A load balancer isn't required in each zone. Instead, each zonal front end, or set of zonal frontends, could be associated with virtual machines in the backend pool that are part of that specific availability zone.


### Safe deployment techniques

Review [Azure cloud design patterns](/azure/architecture/patterns/) to improve the resiliency of your application to failure scenarios.


### Migrate to availability zone support

To learn how to migrate a VM to availability zone support, see [Migrate Load Balancer to availability zone support](./migrate-load-balancer.md).


## Cross-region disaster recovery and business continuity

[!INCLUDE [introduction to disaster recovery](includes/reliability-disaster-recovery-description-include.md)]

Azure Standard Load Balancer supports cross-region load balancing enabling geo-redundant High Availability scenarios such as:

* Incoming traffic originating from multiple regions.
* [Instant global failover](#regional-redundancy) to the next optimal regional deployment.
* Load distribution across regions to the closest Azure region with [ultra-low latency](#ultra-low-latency).
* Ability to [scale up/down](#ability-to-scale-updown-behind-a-single-endpoint) behind a single endpoint.
* Static anycast global IP address
* [Client IP preservation](#client-ip-preservation)
* [Build on existing load balancer](#build-cross-region-solution-on-existing-azure-load-balancer) solution with no learning curve

The frontend IP configuration of your cross-region load balancer is static and advertised across [most Azure regions](#participating-regions).

:::image type="content" source="../load-balancer/media/cross-region-overview/cross-region-load-balancer.png" alt-text="Diagram of cross-region load balancer." border="true":::

> [!NOTE]
> The backend port of your load balancing rule on cross-region load balancer should match the frontend port of the load balancing rule/inbound nat rule on regional standard load balancer. 

### Disaster recovery in multi-region geography

#### Regional redundancy

Configure regional redundancy by adding a global frontend public IP address to your existing load balancers. 

If one region fails, the traffic is routed to the next closest healthy regional load balancer.  

The health probe of the cross-region load balancer gathers information about availability of each regional load balancer every 5 seconds. If one regional load balancer drops its availability to 0, cross-region load balancer detects the failure. The regional load balancer is then taken out of rotation. 

:::image type="content" source="../load-balancer/media/cross-region-overview/global-region-view.png" alt-text="Diagram of global region traffic view." border="true":::


#### Ultra-low latency

The geo-proximity load-balancing algorithm is based on the geographic location of your users and your regional deployments. 

Traffic started from a client hits the closest participating region and travel through the Microsoft global network backbone to arrive at the closest regional deployment. 

For example, you have a cross-region load balancer with standard load balancers in Azure regions:

* West US
* North Europe

If a flow is started from Seattle, traffic enters West US. This region is the closest participating region from Seattle. The traffic is routed to the closest region load balancer, which is West US.

Azure cross-region load balancer uses geo-proximity load-balancing algorithm for the routing decision. 

The configured load distribution mode of the regional load balancers is used for making the final routing decision when multiple regional load balancers are used for geo-proximity.  

For more information, see [Configure the distribution mode for Azure Load Balancer](./load-balancer-distribution-mode.md).

Egress traffic follows the routing preference set on the regional load balancers.

### Ability to scale up/down behind a single endpoint

When you expose the global endpoint of a cross-region load balancer to customers, you can add or remove regional deployments behind the global endpoint without interruption. 

#### Static anycast global IP address

Cross-region load balancer comes with a static public IP, which ensures the IP address remains the same. To learn more about static IP, read more [here](../virtual-network/ip-services/public-ip-addresses.md#ip-address-assignment)

#### Client IP Preservation

Cross-region load balancer is a Layer-4 pass-through network load balancer. This pass-through preserves the original IP of the packet.  The original IP is available to the code running on the virtual machine. This preservation allows you to apply logic that is specific to an IP address.

#### Floating IP

Floating IP can be configured at both the global IP level and regional IP level. For more information, visit [Multiple frontends for Azure Load Balancer](./load-balancer-multivip-overview.md)

It is important to note that floating IP configured on the Azure cross-region Load Balancer operates independently of floating IP configurations on backend regional load balancers. If floating IP is enabled on the cross-region load balancer, the appropriate loopback interface needs to be added to the backend VMs. 

#### Health Probes

Azure cross-region Load Balancer utilizes the health of the backend regional load balancers when deciding where to distribute traffic to. Health checks by cross-region load balancer are done automatically every 5 seconds, given that a user has set up health probes on their regional load balancer.  

## Build cross region solution on existing Azure Load Balancer

The backend pool of cross-region load balancer contains one or more regional load balancers. 

Add your existing load balancer deployments to a cross-region load balancer for a highly available, cross-region deployment.

**Home region** is where the cross-region load balancer or Public IP Address of Global tier is deployed. 
This region doesn't affect how the traffic is routed. If a home region goes down, traffic flow is unaffected.

### Home regions
* Central US
* East Asia
* East US 2
* North Europe
* Southeast Asia
* UK South
* US Gov Virginia
* West Europe
* West US

> [!NOTE]
> You can only deploy your cross-region load balancer or Public IP in Global tier in one of the listed Home regions.

A **participating region** is where the Global public IP of the load balancer is being advertised.

Traffic started by the user travels to the closest participating region through the Microsoft core network. 

Cross-region load balancer routes the traffic to the appropriate regional load balancer.

:::image type="content" source="../load-balancer/media/cross-region-overview/multiple-region-global-traffic.png" alt-text="Diagram of multiple region global traffic.":::

### Participating regions
* Australia East 
* Australia Southeast 
* Central India 
* Central US 
* East Asia 
* East US 
* East US 2 
* Japan East 
* North Central US 
* North Europe 
* South Central US 
* Southeast Asia 
* UK South 
* US DoD Central
* US DoD East
* US Gov Arizona
* US Gov Texas
* US Gov Virginia
* West Central US 
* West Europe 
* West US 
* West US 2 

> [!NOTE]
> The backend regional load balancers can be deployed in any publicly available Azure Region and is not limited to just participating regions.

## Limitations

* Cross-region frontend IP configurations are public only. An internal frontend is currently not supported.

* Private or internal load balancer can't be added to the backend pool of a cross-region load balancer 

* NAT64 translation isn't supported at this time. The frontend and backend IPs must be of the same type (v4 or v6).

* UDP traffic isn't supported on Cross-region Load Balancer for IPv6.

* UDP traffic on port 3 isn't supported on Cross-Region Load Balancer

* Outbound rules aren't supported on Cross-region Load Balancer. For outbound connections, utilize [outbound rules](../load-balancer/outbound-rules.md) on the regional load balancer or [NAT gateway](../nat-gateway/nat-overview.md).

## Pricing and SLA
Cross-region load balancer shares the [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) of standard load balancer.

## Next steps
- [Reliability in Azure](/azure/reliability/availability-zones-overview)
- See [Tutorial: Create a cross-region load balancer using the Azure portal](tutorial-cross-region-portal.md) to create a cross-region load balancer.
- Learn more about [cross-region load balancer](https://www.youtube.com/watch?v=3awUwUIv950).
- Learn more about [Azure Load Balancer](load-balancer-overview.md).
