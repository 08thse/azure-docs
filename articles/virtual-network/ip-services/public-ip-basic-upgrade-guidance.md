---

title: Upgrading a Basic Public IP to Standard Public IP - Guidance
description: Overview of upgrade options and guidance for migrating basic Public IP to standard Public IP for future basic Public IP address retirement
author: mbender-ms
ms.service: load-balancer
ms.author: mbender
ms.topic: overview
ms.date: 09/19/2022
#customer-intent: As an cloud engineer with basic Public IP services, I need guidance and direction on migrating my workloads off basic to standard SKUs
---

# Upgrading a Basic Public IP to Standard Public IP - Guidance

In this article, we'll discuss guidance for upgrading your Basic Public IPs to Standard Public IPs. Standard Public IPs are recommended for all production instances and provide many [key differences](#basic-sku-vs-standard-sku) to your infrastructure.
## Steps to complete the upgrade 

We recommend the following approach to upgrade to Standard SKU Public IP addresses. 

1. Learn about some of the [key differences](#basic-sku-vs-standard-sku) between Basic Public IP and Standard Public IP. 
1. Identify the basic public IP to upgrade.
1. Create a migration plan for planned downtime.
1. Depending on the usage of Public IPs, perform the upgrade based on the following chart:

    | Service using Basic Public IP | Decision path |
    | ------ | ------ |
    |Zone Redundancy | Create a new standard IP. |
    | No Zone Redundancy | Use script to upgrade to standard IP. |
    | Load Balancer |  |
    | VPN Gateway | Cannot dissociate and upgrade. You will need to create a new VPN GW.|
    | App Gateway | Cannot dissociate and upgrade. You will need to create a new App Gateway. |
1. Verify your application and workloads are receiving traffic through the Standard Load Balancer.

## Basic SKU vs. Standard SKU 

This section lists out some key differences between these two Public IP addresses SKUs.

|""| Standard Public IP SKU | Basic Public IP SKU |
|---------|---------|---------|
| **Idle Timeout** | Have an adjustable inbound originated flow idle timeout of 4-30 minutes, with a default of 4 minutes, and fixed outbound originated flow idle timeout of 4 minutes. | Have an adjustable inbound originated flow idle timeout of 4-30 minutes, with a default of 4 minutes, and fixed outbound originated flow idle timeout of 4 minutes. |
| **Security** | Secure by default model and be closed to inbound traffic when used as a frontend. Allow traffic with [network security group](../network-security-groups-overview.md#network-security-groups) is required (for example, on the NIC of a virtual machine with a Standard SKU Public IP attached). | Open by default. Network security groups are recommended but optional for restricting inbound or outbound traffic. |
| **[Availability zones](../../availability-zones/az-overview.md)** | Supported. Standard IPs can be non-zonal, zonal, or zone-redundant. Zone redundant IPs can only be created in [regions where three availability zones](../../availability-zones/az-region.md) are live. IPs created before zones are live won't be zone redundant. | Not supported |
| **[Routing preference](routing-preference-overview.md)** | Supported to enable more granular control of how traffic is routed between Azure and the Internet. | Not supported. |
| **Global tier** | Supported via [cross-region load balancers](../../load-balancer/cross-region-overview.md)| Not supported |
| **[Standard Load Balancer Support](../../load-balancer/skus.md)** | Both IPv4 and IPv6 are supported | Not supported |
| **[NAT Gateway Support](../nat-gateway/nat-overview.md)** | IPv4 is supported | Not supported |
| **[Azure Firewall Support](../nat-gateway/nat-overview.md)** | IPv4 is supported | Not supported |

## Upgrade using Portal, PowerShell, and Azure CLI 

Use the Azure portal, Azure PowerShell, or Azure CLI to help upgrade from Basic to Standard SKU. 

- [Upgrade a public IP address - Azure portal](public-ip-upgrade-portal.md)
- [Upgrade a public IP address - Azure PowerShell](public-ip-upgrade-powershell.md)
- [Upgrade a public IP address - Azure CLI](public-ip-upgrade-cli.md)

## Next Steps

For guidance on upgrading basic Load Balancer to Standard SKUs, see:

> [!div class="nextstepaction"]
> [Upgrading from Basic Load Balancer - Guidance](../../load-balancer/load-balancer-basic-upgrade-guidance.md)