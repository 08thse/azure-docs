---
title: 'Azure DDoS Protection SKU Comparisons'
description: Learn about the available configuration settings for Azure DDoS Protection Standard.
author: AbdullahBell
ms.author: Abell
ms.service: ddos-protection
ms.topic: conceptual 
ms.date: 09/15/2022
ms.custom: template-concept 
---


# About Azure DDoS Protection SKU Comparison


The sections in this article discuss the resources and settings of Azure DDoS Protection Standard.


## SKUs

Azure DDoS Protection Standard supports two SKU Types,
 DDoS IP Protection and DDoS Network Protection. The SKU is configured in the Azure portal during the workflow when you configure Azure DDoS Protection.

The following table shows features and corresponding SKUs.

| Feature | DDoS IP Protection | DDoS Network Protection |
|---|---|---|
| Active traffic monitoring & always on detection |  Yes| Yes |
| L3/L4 Automatic attack mitigation  | Yes | Yes |
| Automatic attack mitigation | Yes | Yes |
| Application based mitigation policies | Yes| Yes |
| Metrics & alerts | Yes | Yes |
| Mitigation reports | Yes | Yes |
| Mitigation flow logs| Yes| Yes |
| Mitigation policies tuned to customers application | Yes| Yes |
| Integration with Firewall Manager | Yes | Yes |
| Azure Sentinel data connector and workbook | Yes | Yes |
| DDoS rapid response support | Not available | Yes |
| Cost protection | Not available  | Yes |
| WAF discount | Not available | Yes |
| Protection of resources across subscriptions in a tenant   | Yes | Yes |
| Price | Per protected IP | Per 100 protected IP addresses |

>[!Note]
>At no additional cost, Azure DDoS infrastructure protection protects every Azure service that uses public IPv4 and IPv6 addresses. This DDoS protection service helps to protect all Azure services, including platform as a service (PaaS) services such as Azure DNS. For more details on supported PaaS services, see [DDoS Protection reference architectures](ddos-protection-reference-architectures.md). Azure DDoS infrastructure protection requires no user configuration or application changes. Azure provides continuous protection against DDoS attacks. DDoS protection does not store customer data.

## DDoS Network Protection

Azure DDoS Network Protection, combined with application design best practices, provides enhanced DDoS mitigation features to defend against DDoS attacks. It's automatically tuned to help protect your specific Azure resources in a virtual network.

## DDoS IP Protection

 DDoS IP Protection is a pay-per-protected IP model. DDoS IP Protection have the same core engineering features as DDoS Network Protection, but will differ in the following value-added services: DDoS rapid response support, cost protection, and discounts on WAF.

## Next steps

* [Quickstart: Create a DDoS Protection Plan](manage-ddos-protection.md)
* [Azure DDoS Protection Standard features](ddos-protection-standard-features.md)
