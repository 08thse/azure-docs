---
title: Manage Web Application Firewall policies using Azure Firewall Manager
description: Learn how to use Azure Firewall Manager to manage Azure Web Application Firewall policies
author: vhorne
ms.author: victorh
ms.service: firewall-manager
ms.topic: how-to
ms.date: 05/06/2022
---

# Manage Web Application Firewall policies using Azure Firewall Manager

Azure Firewall Manager is a platform to manage and protect your network resources at scale. You can centrally create and associate Web Application Firewall (WAF) policies for your application delivery platforms, including Azure Front Door and Azure Application Gateway.

## Prerequisites 

- A deployed [Azure Front Door](../frontdoor/quickstart-create-front-door.md) or [Azure Application Gateway](../application-gateway/quick-create-portal.md)

## Associate a WAF policy

1. Sign in to the Azure portal at [https://portal.azure.com](https://portal.azure.com).
2. In the Azure portal search bar, type **Firewall Manager** and press **Enter**.
3. On the Azure Firewall Manager page, select **Application Delivery Platforms**.
<!-- screenshot -->
4. Select your application delivery platform (Front Door or Application Gateway) to associate a WAF policy. In this example, we'll associate a WAF policy to a Front Door.
1. Select **Manage Security** and then select **Associate WAF policy**.
<!-- screenshot -->
6. Select either an existing policy or **Create New**.
1. Select the domain(s) that you want the WAF policy to protect with your Azure Front Door profile.
1. Select **Associate**.

## Manage WAF policies

1. On the Azure Firewall Manager page, under **Security**, select **Web application firewall policies** to view all your policies.
1. Select **Add** to create a new WAF policy or import settings from an existing WAF policy.
<!-- screenshot -->


## Next steps

- [Configure an Azure DDoS Protection Plan using Azure Firewall Manager (preview)](configure-ddos.md)

