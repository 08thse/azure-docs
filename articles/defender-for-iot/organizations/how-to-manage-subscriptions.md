---
title: Manage OT plans and licenses - Microsoft Defender for IoT
description: Manage Microsoft Defender for IoT plans and licenses for OT monitoring.
ms.date: 05/17/2023
ms.topic: how-to
---

# Manage OT plans and licenses

Your Microsoft Defender for IoT deployment for OT monitoring is managed through a site-based license, purchased in the Microsoft 365 Admin center. After you've purchased your license, apply that license to your OT plan in the Azure portal.

If you're looking to manage Enterprise IoT plans, see [Manage Defender for IoT plans for Enterprise IoT security monitoring](manage-subscriptions-enterprise.md).

## Prerequisites

Before performing the procedures in this article, make sure that you have:

- An Azure subscription. If you need to, [sign up for a free account](https://azure.microsoft.com/free/).

- A [Security admin](../../role-based-access-control/built-in-roles.md#security-admin), [Contributor](../../role-based-access-control/built-in-roles.md#contributor), or [Owner](../../role-based-access-control/built-in-roles.md#owner) user role for the Azure subscription that you'll be using for the integration

### Calculate devices in your network

Unless you're working with a [trial Defender for IoT license](billing.md#free-trial), you'll need to periodically update your licenses as your network grows. Each license applies to a single site, and is based on the site size, or the number of devices monitored in that site.

**To calculate the number of devices in each site:**:

1. Collect the total number of devices in your site and add them together.

1. Remove any of the following devices, which are *not* identified as individual devices by Defender for IoT:

    - **Public internet IP addresses**
    - **Multi-cast groups**
    - **Broadcast groups**
    - **Inactive devices**: Devices that have no network activity detected for more than 60 days

For more information, see [Devices monitored by Defender for IoT](architecture.md#devices-monitored-by-defender-for-iot).

## Purchase a Defender for IoT license

This procedure describes how to purchase Defender for IoT licenses in the Microsoft 365 Admin center.

**To purchase Defender for IoT licenses**:

1. Go to the [Microsoft 365 Admin Center](https://portal.office.com/AdminPortal/Home#/catalog) and select **Marketplace**.

1. Browse to **Security and identity** products, and locate **Microsoft Defender for IoT**.

1. In the **Microsoft Defender for IoT** box, select **Details** and then select your license size, depending on the number of devices in your site.

1. Select the number of licenses you want to purchase, depending on the number of sites you want to monitor at the selected size.

1. Select **Buy**.

1. In [Defender for IoT](https://ms.portal.azure.com/#view/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/~/Getting_started), select **Plans and pricing** > **Add plan**.

1. In the **Plan settings** pane, define the following settings:

   - **Subscription**: Select the Azure subscription where you want to add a plan. You'll need a [Security admin](../../role-based-access-control/built-in-roles.md#security-admin), [Contributor](../../role-based-access-control/built-in-roles.md#contributor), or [Owner](../../role-based-access-control/built-in-roles.md#owner) role for the selected subscription.

        > [!TIP]
        > If your subscription isn't listed, check your account details and confirm your permissions with the subscription owner. Also make sure that you have the right subscriptions selected in your Azure settings > **Directories + subscriptions** page.

   - **License**: TBD

Your new plan is listed under the relevant subscription on the **Plans and pricing** > **Plans** page. For more information, see [Manage your subscriptions](how-to-manage-subscriptions.md).

## Cancel your license

When you choose to cancel a purchase or trial, users with licenses lose access to the product. The user who originally signed up for the purchase or trial subscription receives an email that says the subscription was canceled.

For more information, see the [Microsoft 365 admin center documentation](/microsoft-365/commerce/subscriptions/manage-self-service-purchases-admins#cancel-a-purchase-or-trial-subscription).

## Legacy procedures for plan management in the Azure portal

Customers with existing plans from earlier than June 1, 2023 can continue to manage their plans in the Azure portal, until the end of their plan.

For legacy customers, *committed devices* are the number of devices you're monitoring. For more information, see [Devices monitored by Defender for IoT](architecture.md#devices-monitored-by-defender-for-iot).

You might need to edit your plan to change your plan commitment or update the number of committed devices or sites. For example, you may have more devices that require monitoring if you're increasing existing site coverage, or there are network changes such as adding switches.

> [!NOTE]
> If the number of actual devices detected by Defender for IoT exceeds the number of committed devices currently listed on your subscription, you may see a warning message in the Azure portal and on your OT sensor that you have exceeded the maximum number of devices for your subscription.
>
> This warning indicates you need to update the number of committed devices on the relevant subscription to the actual number of devices being monitored. Click the link in the warning message to take you to the **Plans and pricing** page, with the **Edit plan** pane already open.

**To edit a legacy plan on the Azure portal:**

1. In the Azure portal, go to **Defender for IoT** > **Plans and pricing**.

1. On the subscription row, select the options menu (**...**) at the right > select **Edit plan**.

1. Make any of the following changes as needed:

   - Change your price plan from a trial to a monthly or annual commitment
   - Update the number of [committed devices](#calculate-devices-in-your-network)
   - Update the number of sites (annual commitments only)

1. Select the **I accept the terms and conditions** option, and then select **Purchase**.

1. After any changes are made, make sure to reactivate your sensors. For more information, see [Reactivate an OT sensor](how-to-manage-sensors-on-the-cloud.md#reactivate-an-ot-sensor).

1. If you have an on-premises management console, make sure to upload a new activation file, which reflects the changes made. For more information, see [Upload a new activation file](how-to-manage-the-on-premises-management-console.md#upload-a-new-activation-file).

Changes to your plan will take effect one hour after confirming the change. This change will appear on your next monthly statement, and you'll be charged based on the length of time each plan was in effect.

### Cancel a legacy Defender for IoT plan

You may need to cancel a Defender for IoT plan from your Azure subscription, for example, if you need to work with a new payment entity, or if you no longer need the service.

> [!IMPORTANT]
> Canceling a plan removes all Defender for IoT services from the subscription, including both OT and Enterprise IoT services. If you have an Enterprise IoT plan on your subscription, do this with care.
>
> To cancel only an Enterprise IoT plan, do so from Microsoft 365. For more information, see [Cancel your Enterprise IoT plan](manage-subscriptions-enterprise.md#cancel-your-enterprise-iot-plan).
>

**Prerequisites**: Before canceling your plan, make sure to delete any sensors that are associated with the subscription. For more information, see [Sensor management options from the Azure portal](how-to-manage-sensors-on-the-cloud.md#sensor-management-options-from-the-azure-portal).

**To cancel a Defender for IoT plan for OT networks**:

1. In the Azure portal, go to **Defender for IoT** > **Plans and pricing**.

1. On the subscription row, select the options menu (**...**) at the right and select **Cancel plan**.

1. In the cancellation dialog, select **I agree** to cancel the Defender for IoT plan from the subscription.

Your changes take effect one hour after confirmation. This change will be reflected in your upcoming monthly statement, and you'll only be charged for the time that the subscription was active.



## Next steps

For more information, see:

- [Defender for IoT subscription billing](billing.md)

- [Manage sensors with Defender for IoT in the Azure portal](how-to-manage-sensors-on-the-cloud.md)

- [Create an additional Azure subscription](../../cost-management-billing/manage/create-subscription.md)

- [Upgrade your Azure subscription](../../cost-management-billing/manage/upgrade-azure-subscription.md)
