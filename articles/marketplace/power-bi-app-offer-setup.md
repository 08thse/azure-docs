---
title: Create a Power BI app offer on Microsoft AppSource.
description: Create a Power BI app offer on Microsoft AppSource.
ms.service: marketplace 
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/17/2020
---

# Create a Power BI app offer

This article describes how to create a Power BI app offer. <strike>This offer type is an enterprise resource planning (ERP) service that supports a wide range of business processes, including finance, operations, supply chain, CRM, project management, and electronic commerce.</strike> All offers <strike>for Dynamics 365 </strike>go through our certification process, which checks your solution for standard requirements, compatibility, and proper practices.<strike> The trial experience allows users to deploy your solution to a live Dynamics 365 environment.</strike>

Before you start, create a commercial marketplace account in [Partner Center](partner-center-portal/create-account.md) and ensure it is enrolled in the commercial marketplace program.

## Before you begin

Review [Plan a Dynamics 365 offer](marketplace-dynamics-365.md). It will explain the technical requirements for this offer and list the information and assets you’ll need when you create it.

## Create a new offer

1. Sign in to [Partner Center](https://partner.microsoft.com/dashboard/home).
2. In the left-nav menu, select **Commercial Marketplace** > **Overview**.
3. On the Overview page, select **+ New offer** > **Power BI app**.

    :::image type="content" source="media/dynamics-365/new-offer-power-bi-app.png" alt-text="The left pane menu options and the 'New offer' button.":::

> [!IMPORTANT]
> After an offer is published, any edits you make to it in Partner Center appear on Microsoft AppSource only after you republish the offer. Be sure to always republish an offer after changing it.

If **Power BI Service App** isn't shown or enabled, your account doesn't have permission to create this offer type. Please check that you've met all the [requirements](marketplace-dynamics-365.md) for this offer type, including registering for a developer account.

## New offer

Enter an **Offer ID**. This is a unique identifier for each offer in your account.

- This ID is visible to customers in the web address for the offer and in Azure Resource Manager templates, if applicable.
- Use only lowercase letters and numbers. The ID can include hyphens and underscores, but no spaces, and is limited to 50 characters. For example, if your Publisher ID is `testpublisherid` and you enter **test-offer-1**, the offer web address will be `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- The Offer ID combined with the Publisher ID must be under 50 characters in length. <font color="red">[ still true? ]</font>
- The Offer ID can't be changed after you select **Create**.

Enter an **Offer alias**. This is the name used for the offer in Partner Center.

- This name isn't used <strike>in the marketplace</strike> on AppSource. It is different from the offer name and other values shown to customers.
- This name can't be changed after you select **Create**.

Select **Create** to generate the offer. Partner Center opens the **Offer setup** page.

## Alias

Enter a descriptive name that we'll use to refer to this offer solely within Partner Center. The offer alias (pre-populated with what you entered when you created the offer) won't be used in the marketplace and is different than the offer name shown to customers. If you want to update the offer name later, see the [Offer listing](power-bi-app-offer-listing.md) page.

<!-- ## Setup details

For **How do you want potential customers to interact with this listing offer?**, select the option you'd like to use for this offer.

- **Get it now (free)** – List your offer to customers for free.
- **Free trial (listing)** – List your offer to customers with a link to a free trial. Offer listing free trials are created, managed, and configured by your service and do not have subscriptions managed by Microsoft.

    > [!NOTE]
    > The tokens your application will receive through your trial link can only be used to obtain user information through Azure Active Directory (Azure AD) to automate account creation in your app. Microsoft accounts are not supported for authentication using this token.

- **Contact me** – Collect customer contact information by connecting your Customer Relationship Management (CRM) system. The customer will be asked for permission to share their information. These customer details, along with the offer name, ID, and marketplace source where they found your offer, will be sent to the CRM system that you've configured. For more information about configuring your CRM, see [Customer leads](#customer-leads).

## Test drive

A test drive is a great way to showcase your offer to potential customers by giving them access to a preconfigured environment for a fixed number of hours. Offering a test drive results in an increased conversion rate and generates highly qualified leads. To learn more, start with [What is a test drive?](what-is-test-drive.md).

> [!TIP]
> A test drive is different from a free trial. You can offer either a test drive, free trial, or both. They both provide customers with your solution for a fixed period-of-time. But, a test drive also includes a hands-on, self-guided tour of your product’s key features and benefits being demonstrated in a real-world implementation scenario.

To enable a test drive, select the **Enable a test drive** check box and select the **Type of test drive**. You will configure the test drive later. <font color="red">[ Need to do this ]</font>Configuring a CRM (see next section) is required with test drive <font color="red">[ Applies to CE&PA too? ]</font> and when using the **Contact me** package type. To remove test drive from your offer, clear this check box.
-->
## Customer leads

[!INCLUDE [Connect lead management](includes/connect-lead-management.md)]

For more information, see [Customer leads from your commercial marketplace offer](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Select **Save draft** before continuing to the next tab in the left-nav menu, **Properties**.

## Next steps

- [Configure offer properties](power-bi-app-properties.md)
- [Offer listing best practices](gtm-offer-listing-best-practices.md)