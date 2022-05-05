---
title: What is Azure DNS Private Resolver?
description: In this article, get started with an overview of the Azure DNS Private Resolver service.
services: dns
author: greg-lindsay
ms.service: dns
ms.topic: overview
ms.date: 05/05/2022
ms.author: greglin
#Customer intent: As an administrator, I want to evaluate Azure DNS Private Resolver so I can determine if I want to use it instead of my current DNS resolver service.
---

# What is Azure DNS Private Resolver?

Azure DNS Private Resolver is a new service currently in [public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure DNS Private Resolver enables you to query Azure DNS private zones from an on-prem environment and vice versa without deploying VM based DNS servers. 

## How does it work?

Azure DNS Private Resolver requires an [Azure Virtual Network](/azure/virtual-network/virtual-networks-overview).  When you create an Azure DNS Private Resolver inside a virtual network, one or more [inbound endpoints](#inbound-endpoints) are established that can be used as the destination for DNS queries. The inbound endpoint processes DNS queries based on a [DNS forwarding ruleset](#dns-forwarding-rulesets) that you configure.  DNS queries that are initiated in networks linked to a ruleset can be sent to the DNS resolver's inbound endpoint, or to other DNS servers.

The DNS query process is summarized below:

1. A client in a virtual network issues a DNS query.
2. If the DNS servers for this virtual network are [specified as custom](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#specify-dns-servers), then the query is forwarded to the specified IP addresses.
3. If Default (Azure-provided) DNS servers are configured in the virtual network, then [Virtual Network Links](#virtual-network-links) for [DNS Forwarding Rulesets](#dns-forwarding-rulesets) are consulted.
4. If no ruleset links are present, then Azure DNS is used to resolve the query.
5. If ruleset links are present, the [DNS Forwarding Rules](#dns-forwarding-rules) are evaluated.
7. If a suffix match is found, query is forwarded to the specified address.
8. If multiple matches are present, the longest suffix is used.
9. If no match is found, no DNS forwarding occurs and Azure DNS is used to resolve the query.

The architecture for Azure DNS Private Resolver is summarized in the following figure. DNS resolution between Azure virtual networks and on-prem networks requires [Azure ExpressRoute](/azure/expressroute/expressroute-introduction) or a VPN.

![private DNS resolver architecture](./media/dns-resolver-overview/resolver-architecture.png)

Figure 1: Azure DNS Private Resolver architecture

For more information about creating a private DNS resolver, see:
- [Quickstart: Create an Azure DNS Private Resolver using the Azure portal](private-dns-resolver-getstarted-portal.md)
- [Quickstart: Create an Azure DNS Private Resolver using Azure PowerShell](private-dns-resolver-getstarted-powershell.md)

## Azure DNS Private Resolver benefits

Azure DNS Private Resolver provides the following benefits:
* Fully managed: Built-in high availability, zone redundancy.
* Cost reduction: Reduce operating costs and run at a fraction of the price of traditional IaaS solutions.
* Private access to your Private DNS Zones: Conditionally forward to and from on-prem.
* Scalability: High performance per endpoint.
* DevOps Friendly: Build your pipelines with Terraform, ARM, or Bicep.

## Regional availability

Azure DNS Private Resolver is available in the following regions:

- Australia East
- UK South
- North Europe
- South Central US
- West US 3
- East US
- North Central US
- Central US EUAP
- East US 2 EUAP
- West Central US
- East US 2
- West Europe

## DNS resolver endpoints

### Inbound Endpoints

An inbound endpoint enables name resolution from on-prem or other private locations via an IP address that is part of your private virtual network address space. This endpoint requires a subnet in the VNet where it’s provisioned. The subnet can only be delegated to **Microsoft.Network/dnsResolvers** and cannot be used for other services. DNS queries received by the inbound endpoint will ingress to Azure. You can resolve names in scenarios where you have Private DNS Zones, including VMs which are using auto registration, or Private Link enabled services.

### Outbound Endpoints

An outbound endpoint enables conditional forwarding name resolution from Azure to on-prem, other cloud providers, or external DNS servers. This endpoint requires a dedicated subnet in the VNet where it’s provisioned, with no other service running in the subnet, and can only be delegated to **Microsoft.Network/dnsResolvers**. DNS queries sent to the outbound endpoint will egress from Azure.

## Virtual Network Links

Virtual network links enable name resolution for virtual networks which are linked to an outbound endpoint with a DNS forwarding ruleset. This is a 1:1 relationship.

## DNS Forwarding Rulesets

A DNS forwarding ruleset is a group of DNS forwarding rules (up to 1,000) which can be applied to one or more outbound endpoints, or linked to one or more virtual networks. This is a 1:N relationship.

## DNS forwarding rules

A DNS rorwarding rule includes one or more target DNS servers that will be used for conditional forwarding, and is represented by:
- A domain name, 
- A target IP address, 
- A target Port and Protocol (UDP or TCP).

## Restrictions:

### Virtual network restrictions 

The following restrictions hold with respect to virtual networks:
- A DNS resolver can only reference a virtual network in the same region as the DNS resolver.
- A virtual network can't be shared between multiple DNS resolvers. A single virtual network can only be referenced by a single DNS resolver.

### Subnet restrictions 

Subnets used for DNS resolver have the following limitations:
- A subnet must be a minimum of /28 address space or a maximum of /24 address space.
- A subnet cannot be shared between multiple DNS resolver endpoints. A single subnet can only be used by a single DNS resolver endpoint.
- All IP configurations for a DNS resolver inbound endpoint must reference the same subnet. Spanning multiple subnets in the IP configuration for a single DNS resolver inbound endpoint is not allowed.
- The subnet used for for a DNS resolver inbound endpoint must be within the virtual network referenced by the parent DNS resolver.

### Outbound endpoint restrictions

Outbound endpoints have the following limitations:
- Outbound endpoint cannot be deleted unless the DNS forwarding ruleset and the virtual network links under it are deleted

### DNS forwarding ruleset restrictions

DNS forwarding ruleset have the following limitations:
- A DNS forwarding ruleset cannot be deleted unless the virtual network links under it are deleted

### Other restrictions

- DNS resolver endpoints cannot be updated to include IP configurations from a different subnet
- IPv6 enabled subnets are not supported in Public Preview


## Next steps

* Learn how to create an Azure DNS Private Resolverby using [Azure PowerShell](./private-dns-resolver-getstarted-powershell.md) or [Azure Portal](./private-dns-resolver-getstarted-portal.md).
* Learn about some of the other key [networking capabilities](../networking/fundamentals/networking-overview.md) of Azure.
* [Learn module: Introduction to Azure DNS](/learn/modules/intro-to-azure-dns).
