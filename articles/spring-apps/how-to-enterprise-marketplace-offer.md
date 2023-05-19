---
title: Enterprise tier in Azure Marketplace
description: Learn about the Azure Spring Apps Enterprise tier offering available in Azure Marketplace.
author: karlerickson
ms.author: yuwzho
ms.service: spring-apps
ms.topic: how-to
ms.date: 03/24/2023
ms.custom: devx-track-java, devx-track-azurecli, event-tier1-build-2022, engagement-fy23, references_regions
---

# Enterprise tier in Azure Marketplace

> [!NOTE]
> Azure Spring Apps is the new name for the Azure Spring Cloud service. Although the service has a new name, you'll see the old name in some places for a while as we work to update assets such as screenshots, videos, and diagrams.

**This article applies to:** ❌ Basic/Standard tier ✔️ Enterprise tier

This article describes the Azure Marketplace offer and license requirements for the VMware Taznu components in the Enterprise tier in Azure Spring Apps.

## Enterprise tier and VMware Tanzu components

The Azure Spring Apps Enterprise tier is optimized for the needs of enterprise Spring developers and provides advanced configurability, flexibility, and portability. Azure Spring Apps also provides the enterprise-ready VMware Spring Runtime with 24/7 support in a strong partnership with VMware. You can learn more about the tier's value propositions in the [Enterprise plan](./overview.md#enterprise-plan) section of [What is Azure Spring Apps?](/azure/spring-apps/overview)

Because the Enterprise tier provides feature parity with the Standard tier, it provides a rich set of features that include app lifecycle management, monitoring, and troubleshooting.

The Enterprise tier provides the following managed VMware Tanzu components that empower enterprises to ship faster:

- Tanzu Build Service
- Application Configuration Service for Tanzu
- Tanzu Service Registry
- Spring Cloud Gateway for Tanzu
- API portal for VMware Tanzu
- Application Live View for VMware Tanzu
- Application Accelerator for VMware Tanzu

The pricing for Azure Spring Apps Enterprise tier is composed of the following two parts:

- Infrastructure pricing, set by Microsoft, based on vCPU and memory usage of apps and managed Tanzu components.
- Tanzu component licensing pricing, set by VMware, based on vCPU usage of apps.

For more information about pricing, see [Azure Spring Apps pricing](https://azure.microsoft.com/pricing/details/spring-apps/).

To provide the best customer experience to manage the Tanzu component license purchasing and metering, VMware creates an [Azure Spring Apps Enterprise](https://aka.ms/ascmpoffer) offer in Azure Marketplace. This offer represents a Tanzu component license that is automatically purchased on behalf of customers during the creation of an Azure Spring Apps Enterprise tier instance.

Under this implicit Azure Marketplace third-party offer purchase from VMware, your personal data and application vCPU usage data is shared with VMware. You agree to this data sharing when you agree to the marketplace terms upon creating the service instance.

To purchase the Tanzu component license successfully, the [billing account](../cost-management-billing/manage/view-all-accounts.md) of your subscription must be included in one of the locations listed in the [Supported geographic locations of billing account](#supported-geographic-locations-of-billing-account) section. Because of tax management restrictions from VMware in some countries/regions, not all countries/regions are supported.

The extra license fees apply only to the Enterprise tier. In the Azure Spring Apps Standard tier, there are no extra license fees because the managed Spring components use the OSS config server and Eureka server. No other third-party license fees are required.

On the [Azure Spring Apps Enterprise](https://aka.ms/ascmpoffer) offer page in Azure Marketplace, you can review the Tanzu component license pricing as shown in the following image.

:::image type="content" source="media/how-to-enterprise-marketplace-offer/enterprise-plan.png" alt-text="Screenshot of Azure Marketplace showing the Azure Spring Apps Enterprise tier VMware Tanzu offering." lightbox="media/how-to-enterprise-marketplace-offer/enterprise-plan.png":::

You can use the Azure portal or the Azure CLI to provision an Azure Spring Apps Enterprise tier service instance. You can also select **Subscribe** on the Azure Marketplace offer page to create the service instance. Azure Marketplace redirects you to the Azure Spring Apps creation page.

## Requirements

You must understand and fulfill the following requirements to successfully create an instance of Azure Spring Apps Enterprise tier when purchasing the Azure Marketplace offer. 

- Your Azure subscription must be registered to the `Microsoft.SaaS` resource provider. For more information, see the [Register resource provider](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) section of [Azure resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md).

- Your Azure subscription must have an associated payment method. Azure credits or free MSDN subscriptions aren't supported. For more information, see the [Purchasing requirements](/marketplace/azure-marketplace-overview#purchasing-requirements) section of [What is Azure Marketplace?](/marketplace/azure-marketplace-overview)

- Your Azure subscription must belong to a [billing account](../cost-management-billing/manage/view-all-accounts.md) in a supported geographic location defined in the [Azure Spring Apps Enterprise](https://aka.ms/ascmpoffer) offer in Azure Marketplace. For more information, see the [Supported geographic locations of billing account](#supported-geographic-locations-of-billing-account) section.

- Your region must be available. Choose an Azure region currently available. For more information, see [In which regions is Azure Spring Apps Enterprise tier available?](./faq.md#in-which-regions-is-azure-spring-apps-enterprise-tier-available) in the [Azure Spring Apps FAQ](faq.md).

- Your organization must allow Azure Marketplace purchases. For more information, see the [Enabling Azure Marketplace purchases](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases) section of [Azure Marketplace](../cost-management-billing/manage/ea-azure-marketplace.md).

- Your organization must allow acquisition of any Azure Marketplace software application as described in the [Purchase policy management](/marketplace/azure-purchasing-invoicing#purchase-policy-management) section of [Azure Marketplace purchasing](/marketplace/azure-purchasing-invoicing).

- You must accept the marketplace legal terms and privacy statements while provisioning the tier on the Azure portal, or you can use the following commands to do so in advance.

  ```azurecli
  az term accept \
      --publisher vmware-inc \
      --product azure-spring-cloud-vmware-tanzu-2 \
      --plan asa-ent-hr-mtr
  ```

## Supported geographic locations of billing account

To successfully purchase the [Azure Spring Apps Enterprise](https://aka.ms/ascmpoffer) offer on Azure Marketplace, your Azure subscription must belong to a [billing account](../cost-management-billing/manage/view-all-accounts.md) in a supported geographic location defined in the offer.

The following table lists each supported geographic location and its [ISO 3166 two-digit alpha code](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes).

| Country/Region Name  |ISO-2|
|----------------------|-----|
| Armenia              | AM  |
| Austria              | AT  |
| Belarus              | BY  |
| Belgium              | BE  |
| Bulgaria             | BG  |
| Canada               | CA  |
| Chile                | CL  |
| Colombia             | CO  |
| Croatia              | HR  |
| Cyprus               | CY  |
| Czechia              | CZ  |
| Denmark              | DK  |
| Estonia              | EE  |
| Finland              | FI  |
| France               | FR  |
| Germany              | DE  |
| Greece               | GR  |
| Hungary              | HU  |
| Iceland              | IS  |
| India                | IN  |
| Indonesia            | ID  |
| Ireland              | IE  |
| Italy                | IT  |
| Korea                | KE  |
| Latvia               | LV  |
| Liechtenstein        | LI  |
| Lithuania            | LT  |
| Luxembourg           | LU  |
| Malaysia             | MY  |
| Malta                | MT  |
| Monaco               | MC  |
| Netherlands          | NL  |
| New Zealand          | NZ  |
| Nigeria              | NG  |
| Norway               | NO  |
| Poland               | PL  |
| Portugal             | PT  |
| Puerto Rico          | PR  |
| Romania              | RO  |
| Russia               | RU  |
| Saudi Arabia         | SA  |
| Serbia               | RS  |
| Singapore            | SG  |
| Slovakia             | SK  |
| Slovenia             | SI  |
| South Africa         | ZA  |
| Spain                | ES  |
| Sweden               | SE  |
| Switzerland          | CH  |
| Taiwan               | TW  |
| Thailand             | TH  |
| Türkiye              | TR  |
| United Arab Emirates | AE  |
| United Kingdom       | GB  |
| United States        | US  |

## Troubleshooting
Below table provides the mapping from regular error messages you may meet during creating Azure Spring Apps Enterprise to the recommended actions to resolve them.

| Error Messages                                                                                                                                                                                                                                                          | Recommended Actions                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Failed to purchase on Azure Marketplace because the Microsoft.SaaS RP is not registered on the Azure subscription                                                                                                                                                       | It is because your Azure subscription does not register to the `Microsoft.SaaS` resource provider. To register the resource provider, see the [Register resource provider](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) section of [Azure resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md).                                                                                                                                                                                                |
| Failed to load catalog product vmware-inc.azure-spring-cloud-vmware-tanzu-2 in the Azure subscription market.                                                                                                                                                           | <p>It’s due to the billing account, referred by the subscription you use, doesn’t contain a valid billing address in scope of our supported geographic locations. You may follow below steps to get it fixed:</p> <ul> <li>Please figure out the billing address of the referred billing account in your subscription by following <a href="/cost-management-billing/manage/view-all-accounts#check-the-type-of-your-account">View your billing accounts in Azure portal</a>. If you can't view or manage billing accounts, you probably don't have permission to access. You can ask your billing account administrator for help.</li> <li>Check whether it’s in scope of our <a href="n#supported-geographic-locations-of-billing-account">supported geographic locations</a>. If indeed no, try either approach below:</li> <ul> <li>If doable, please try to use another billing account with a supported billing address and then try again.</li> <li>If you want to use current subscription, please raise a support ticket to us.</li> </ul> </ul>            |
| Failed to purchase on Azure Marketplace due to signature verification on Marketplace legal agreement.                                                                                                                                                                   | It is because you don't accept the marketplace legal terms and privacy statements while provisioning the tier, you can use the following commands to do so in advance. `az term accept --publisher vmware-inc --product azure-spring-cloud-vmware-tanzu-2 --plan asa-ent-hr-mtr`                                                                                                                                                                                                                                                                                                                  |
| Purchase has failed because we couldn't find a valid credit card nor a payment method associated with your Azure subscription.                                                                                                                                          | It is because your Azure subscription does not have an associated payment method. Azure credits or free MSDN subscriptions aren't supported. For more information, see the [Purchasing requirements](/marketplace/azure-marketplace-overview#purchasing-requirements) section of [What is Azure Marketplace?](/marketplace/azure-marketplace-overview)                                                                                                                                                                                                                                            |
| Plan can not be purchased on a free subscription.                                                                                                                                                                                                                       | It is because your Azure subscription does not have an associated payment method. Azure credits or free MSDN subscriptions aren't supported. For more information, see the [Purchasing requirements](/marketplace/azure-marketplace-overview#purchasing-requirements) section of [What is Azure Marketplace?](/marketplace/azure-marketplace-overview)                                                                                                                                                                                                                                            |
| Your Azure subscription which is associated with a deleted organization. The billing account is not valid to purchase the SaaS.                                                                                                                                         | It is because your Azure subscription does not have an associated payment method. Azure credits or free MSDN subscriptions aren't supported. For more information, see the [Purchasing requirements](/marketplace/azure-marketplace-overview#purchasing-requirements) section of [What is Azure Marketplace?](/marketplace/azure-marketplace-overview)                                                                                                                                                                                                                                            |
| Failed to check if marketplace is enabled for your Azure subscription.                                                                                                                                                                                                  | It is because your organization does not allow Azure Marketplace purchases. To allow the purchase, see the [Enabling Azure Marketplace purchases](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases) section of [Azure Marketplace](../cost-management-billing/manage/ea-azure-marketplace.md).                                                                                                                                                                                                                                                      |
| Purchase for Azure product azure-spring-cloud-vmware-tanzu-2 and plan Azure Spring Apps Enterprise (Public) is not allowed.                                                                                                                                             | It is because your organization does not allow Azure Marketplace purchases. To allow the purchase, see the [Enabling Azure Marketplace purchases](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases) section of [Azure Marketplace](../cost-management-billing/manage/ea-azure-marketplace.md).                                                                                                                                                                                                                                                      |
| Failed to process eligibility check with error Plan 'Azure Spring Apps Enterprise (Public)' of offer 'Azure Spring Apps Enterprise' by publisher 'VMware Inc.' is not available to you for purchase due to private marketplace settings made by your tenant’s IT admin. | It is because your organization does not allow Azure Marketplace purchases or acquisition of any Azure Marketplace software application. To allow the purchase, see the [Enabling Azure Marketplace purchases](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases) section of [Azure Marketplace](../cost-management-billing/manage/ea-azure-marketplace.md) and [Purchase policy management](/marketplace/azure-purchasing-invoicing#purchase-policy-management) section of [Azure Marketplace purchasing](/marketplace/azure-purchasing-invoicing). |
| This offer 'vmware-inc.azure-spring-cloud-vmware-tanzu-2' is not available for purchasing by subscriptions belonging to Microsoft Azure Cloud Solution Providers.                                                                                                       | Azure Cloud Solution Provider is not supported to purchase Azure Sprign Apps Enterprise yet. We are working on to enable it.                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
## Next steps

- [Launch your first app](./quickstart.md)
