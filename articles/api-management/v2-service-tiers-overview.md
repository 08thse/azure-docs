---
title: Azure API Management - v2 tiers (preview)
description: Introduction to key scenarios, capabilities, and concepts of the v2 tiers (SKUs) of the Azure API Management service. The v2 tiers are in preview.
services: api-management
author: dlepow
editor: ''
 
ms.service: api-management
ms.topic: conceptual
ms.date: 09/06/2023        
ms.author: danlep
ms.custom: references_regions
---

# New Azure API Management tiers (preview)

We're introducing a new set of pricing tiers (SKUs) for Azure API Management: the *v2 tiers*. The new tiers are built on a new, more reliable and scalable platform and are designed to make API Management accessible to a broader set of customers and offer flexible options for a wider variety of scenarios.

Currently in preview, the following v2 tiers are available:

* **Basic v2** - The Basic v2 tier is designed for development and testing scenarios, and is supported with an SLA. In the Basic v2 tier, the developer portal is an optional add-on.

* **Standard v2** - Standard v2 is a production-ready tier with support planned for advanced API Management features previously available only in a Premium tier of API Management, including high availability and networking options.

## Key capabilities

* **Faster deployment and scaling** - Deploy a production-ready API Management instance in minutes. Scale up quickly to meet the needs of your API management workloads.

* **Simplified networking** - The Standard v2 tier supports [outbound connections](#networking-options) to network-isolated backends.

* **Built-in analytics** -  The v2 tiers include built-in analytics based on Azure Log Analytics workbooks.

* **More options for production workloads** - The v2 tiers are all supported with an SLA.

* **Consumption-based pricing** - The v2 tiers have a consumption-based pricing model, based on the number of API calls made through your API Management gateway. For details, see [API Management pricing](https://azure.microsoft.com/pricing/details/api-management/).

* **Developer portal options** - Enable the [developer-portal](api-management-howto-developer-portal.md) when you're ready to let API consumers discover your APIs. The developer portal is included in the Standard v2 tier, and is an add-on in the Basic v2 tier.

## Networking options

In preview, the v2 tiers currently support the following options to limit network traffic from your API Management instance:


* **Standard v2**

    **Outbound** - VNet integration to allow your API Management instance to reach API backends that are isolated in a VNet. The API Management gateway, management plane, and developer portal remain publicly accessible from the internet. The VNet must be in the same region as the API Management instance. [Learn more](integrate-vnet-outbound.md).

    
## Features and limitations

### API version

The v2 tiers are supported in API Management API version 2023-03-01-preview or later.

### Supported regions

In preview, the v2 tiers are available in the following regions:

* East US
* South Central US
* West US
* France Central
* West Europe
* UK South
* Brazil South
* South Africa North
* Australia East
* Australia Southeast
* East Asia

### Feature availability

Most capabilities of the existing (v1) tiers are planned for the v2 tiers. However, the following capabilities aren't supported in the v2 tiers:

* API Management service configuration using Git
* Back up and restore of API Management instance
* Enabling Azure DDoS Protection

### Preview limitations

Currently, the following API Management capabilities are unavailable in the v2 tiers preview and are planned for later release. Where indicated, certain features are planned only for the Standard v2 tier. Features may be enabled during the preview period.


**Infrastructure and networking**
* Zone redundancy (*Standard v2*)
* Multi-region deployment (*Standard v2*)
* Autoscaling
* Multiple custom domain names (*Standard v2*)
* Capacity metric
* Deployment (injection) in a VNet (*Standard v2*) 
* Inbound connection using a private endpoint
* Upgrade to v2 tiers from v1 tiers
* Workspaces
* Compute platform version property  

**Developer portal**
* Delegation of user registration and product subscription

**Gateway**
* Management of Websocket APIs
* Requests to the gateway over localhost
* Self-hosted gateway (*Standard v2*)
* Rate limit by key policy

## Deployment

Deploy an instance of the Basic v2 or Standard v2 tier using the Azure portal, Azure REST API, or Azure Resource Manager template.

## Frequently asked questions

### Q: Can I migrate from my existing API Management instance to a new v2 tier instance?

A: No. Currently you can't migrate an existing API Management instance (in the Consumption, Developer, Standard, or Premium tier) to a new v2 tier instance. You must deploy a new instance in the v2 tier.

### Q: What's the relationship between the stv2 compute platform and the v2 tiers?
A: They're not related. API Management's stv2 compute platform is a successor platform version used for the [retirement of the stv1 compute platform](./breaking-changes/stv1-platform-retirement-august-2024.md) in the current Developer, Standard, and Premium tiers. The v2 tiers aren't affected by the retirement of the stv1 compute platform.

### Q: How can I provide feedback?

Provide feedback or report issues in the [v2 tier GitHub repo](https://github.com/Azure/api-management-tiersv2), or send email to apimskv2@microsoft.com.

## Related content

* Learn more about the API Management [tiers](api-management-features.md).