---
title: 'Using network groups with security admin rules'
description: Learn how a network administrator can deploy security admin rules using network groups as the source and destination in Azure Virtual Network Manager.
author: mbender-ms
ms.author: mbender
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 04/15/2024
ms.custom: template-concept, engagement-fy23, references_regions
#customer intent: As a network administrator, I want to deploy security admin rules in Azure Virtual Network Manager. When creating security admin rules, I want to define network groups as the source and destination of traffic.
---

# Using network groups with security admin rules

In this article, you learn how to use network groups with security admin rules in Azure Virtual Network Manager (AVNM). Network groups allow you to create logical groups of virtual networks and subnets that have common attributes, such as environment, region, service type, and more. You can then specify your network groups as the source and/or destination of your security admin rules so that you can enforce the traffic among your grouped network resources. This feature streamlines the process of securing your traffic across workloads and environments, as it removes the manual step of specifying individual Classless Inter-Domain Routing (CIDR) ranges or resource IDs.

[!INCLUDE [virtual-network-manager-network-groups-source-destination-preview](../../includes/virtual-network-manager-network-groups-source-destination-preview.md)]

## Why use network groups with security admin rules?

Using network groups with security admin rules allows you to define the source and destination of the traffic for the security admin rule. This feature streamlines the process of securing your traffic across workloads and environments, as it removes the manual step of specifying individual CIDR ranges or resource IDs.

For example, you need to ensure traffic is denied between your production and nonproduction environments represented by two separate network groups. Create a security admin rule with an action type of **Deny**. Specify one of your network groups as the source. Specify the other network group as the destination. Select the direction of the traffic you want to deny. You can enforce the traffic between your grouped network resources without the need to specify individual CIDR ranges or resource IDs.

## How do I deploy a security admin rule using network groups?

From the Azure portal, you can [deploy a security admin rule using network groups](./how-to-create-security-admin-rule-network-groups.md) in the Azure portal. To create a security admin rule, create a security admin configuration and add a security admin rule that utilizes network groups as source and destination. Finally, deploy the security admin configuration and the rules apply to the network group resources. 

If you change the resources in your network group or a network group's CIDR range changes, you need to redeploy the configuration after the changes are made. After deployment, the new CIDR ranges will be applied across your network to all new and existing network group resources.

## Supported regions

During the public preview, network groups with security admin rules are supported in the following regions:

- Supported Regions:
    
    - Central US EUAP
    
    - East US
    
    - East US 2
    
    - East US 2 EUAP
    
    - South Central US
    
    - West US
    
    - West US 2
    
    - West US Central

## Limitations of network groups with security admin rules

The following limitations apply when using network groups with security admin rules:

- Only supports manual aggregation of CIDRs in a network group. The CIDR range in a rule only changes upon the customer commit.

- Supports 100 networking resources (virtual networks or subnets) in any one network group referenced in the security admin rule.

- CIDR ranges for network groups members can be either Ipv4 or Ipv6 CIDRs, but not both in the same group. If Ipv4 and Ipv6 ranges are present in the same group, your virtual network manager only uses the IPv4 ranges.

- Role-based access control ownership is inferred from the `Microsoft.Network/networkManagers/securityAdminConfigurations/rulecollections/rules/write` permission only.

- Network groups must have the same member-types. Virtual networks and subnets are supported but must be in separate network groups.

- Force-delete of any network group used as the source and/or destination in a security admin rule isn't currently supported. Usage causes an error.

## Next steps

> [!div class="nextstepaction"]
> [Create a security admin rule using network groups](./how-to-create-security-admin-rule-network-groups.md)