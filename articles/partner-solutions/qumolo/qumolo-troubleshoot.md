---
title: Troubleshooting Azure Native Qumolo Service
description: This article provides information about troubleshooting Azure Native Qumolo Service.

ms.topic: conceptual
ms.date: 12/31/2022

---

# Troubleshoot Azure Native Qumolo Service

This article describes how to contact support when working with Azure Native Qumulo Scalable File Service. Before contacting support, see Fix common errors.

## Contact support

To contact support about the Azure Native Qumulo Scalable File Service, select [**New Support request**](https://aka.ms/partners/Qumulo/Support) in the left pane. Select the link to the Qumulo support website.

  Screenshot

## Fix common errors

This document supplies information to troubleshoot common problems about
Azure Native Qumulo Scalable File Service.

## Purchase error

- Purchase fails because a valid credit card is not connected to the Azure subscription, or a payment method is not associated with the subscription.

  - Use a different Azure subscription. Or, add or update the credit card or payment method for the subscription. For more information, see [updating the credit and payment method](/azure/cost-management-billing/manage/change-credit-card).

- The EA (Microsoft Enterprise Agreement) subscription does not allow *Marketplace* purchases.

  - Use a different subscription. Or check if your EA subscription is enabled for Marketplace purchase. For more information, see [Enable Marketplace purchases](/azure/cost-management-billing/manage/ea-azure-marketplace#enabling-azure-marketplace-purchases).

If those options do not solve the problem, contact [Qumulo support.](https://aka.ms/partners/Qumulo/Support)

## Unable to create resource

- To set up the Azure Native Qumulo Scalable Service integration, you must have **Owner** or **Contributor** access on the Azure subscription. Ensure you have the proper access on both the subnet resource group and Qumulo service resource group before starting the setup.

- Custom RBAC roles needs to have the following permission in the subnet and Qumulo service resource groups to successfully create a Qumulo service. These permissions are

  - Qumulo.Storage/\*

  - Microsoft.Network/virtualNetworks/subnets/join/action

## Next Steps
