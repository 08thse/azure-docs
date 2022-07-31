---
title: FAQs for Enterprise IoT networks - Microsoft Defender for IoT
description: Find answers to the most frequently asked questions about Microsoft Defender for IoT Enterprise IoT networks.
ms.topic: conceptual
ms.date: 07/07/2022
---

# Enterprise IoT networks frequently asked questions

This article provides a list of frequently asked questions and answers about Enterprise IoT networks in Defender for IoT.

## How can I start using Enterprise IoT?

To get started, you'll need to:

1. Add a Defender for IoT plan with Enterprise IoT to your Azure subscription from [Microsoft Defender for Endpoint](/microsoft-365/security/defender-endpoint/enable-microsoft-defender-for-iot-integration).
1. [Set up a Defender for IoT network sensor](tutorial-getting-started-eiot-sensor.md).

If you’re a Defender for Endpoint customer, when adding your Defender for IoT plan, take care to exclude any devices already managed by Defender for Endpoint from your count of committed devices.

For more information, see: 
- [Tutorial: Get started with Enterprise IoT](tutorial-getting-started-eiot-sensor.md) 
- [Defender for IoT integration](/microsoft-365/security/defender-endpoint/enable-microsoft-defender-for-iot-integration)

## How can I use the Enterprise IoT network sensor?

The Enterprise IoT network sensor is currently in Public Preview and can be used by all customers without additional charge. Add a Defender for IoT plan with Enterprise IoT, and then set up your Enterprise IoT network sensor.

For more information, see [Tutorial: Get started with Enterprise IoT](tutorial-getting-started-eiot-sensor.md).

## What permissions do I need to add a Defender for IoT plan? Can I use any Azure subscription? 

Azure users with the **Security admin**, **Subscription owner** or **Subscription contributor** roles can add, edit, and cancel Defender for IoT plans. For more information, see [Permissions](getting-started.md#permissions).

Defender for Endpoint users with the **Global admin** role can add or cancel plans.

## Which devices are billable?

Devices are listed in the Defender for IoT device inventory based on a unique IP and MAC address coupling. Charges are based on the number of committed devices you provide when adding a Defender for IoT plan.

If you're a Defender for Endpoint customer, devices (seats) that are managed by Defender for Endpoint aren't included in the number of devices counted as committed devices.

For more information, see [Defender for IoT committed devices](how-to-manage-subscriptions.md#defender-for-iot-committed-devices).

## How should I estimate the number of committed devices?

We suggest using existing resources in your environment, for example Meraki, CMDB and other sources to get that estimation, as well as the device inventories in Defender for Endpoint and Defender for IoT. Once you have onboarded Defender for IoT, discovered devices will begin to populate in the device inventory and then you can update the number of your committed devices accordingly. A device would be a set combination of IP address and a MAC address. For more information, see [Defender for IoT committed devices](how-to-manage-subscriptions.md#defender-for-iot-committed-devices).

## Can I edit information in Defender for IoT about a discovered device?

You can edit several properties for devices, and even delete devices from the Defender for IoT **Device inventory** page. For more information, see [Edit device details](how-to-manage-device-inventory-for-organizations.md#edit-device-details).

## How does the integration between Microsoft Defender for Endpoint and Microsoft Defender for IoT work? 

Integration between the two products takes place seamlessly, once you have:
- Added a Defender for IoT plan with Enterprise IoT to an Azure subscription in Defender for Endpoint
- Set up an Enterprise IoT or OT sensor from Defender for IoT in the Azure portal

Once these requirements are met, discovered devices can be viewed in both Defender for IoT and Defender for Endpoint. For more information, see [Defender for IoT integration](/microsoft-365/security/defender-endpoint/enable-microsoft-defender-for-iot-integration).

## Can I change the subscription I’m using for Defender for IoT?

To change the subscription you're using for your Defender for IoT plan, you'll need to cancel your plan on the existing subscription, and then add a new plan to a new subscription. Your existing data won't be migrated to the new subscription. For more information, see [Move existing sensors to a different subscription](how-to-manage-subscriptions.md#move-existing-sensors-to-a-different-subscription).

## How can I cancel Enterprise IoT?

To remove only Enterprise IoT from your plan, cancel your plan from Microsoft Defender for Endpoint. For more information, see [Cancel your Defender for IoT plan](/microsoft-365/security/defender-endpoint/enable-microsoft-defender-for-iot-integration#cancel-your-defender-for-iot-plan). 

To cancel the plan and remove all Defender for IoT services from the associated subscription, cancel the plan in Defender for IoT in the Azure portal. For more information, see [Cancel a Defender for IoT plan from a subscription](how-to-manage-subscriptions.md#cancel-a-defender-for-iot-plan-from-a-subscription).

## What happens when the 30-day trial ends? 

If you haven't changed your plan from a trial to a monthly commitment by the time your trial ends, your plan is automatically canceled, and you’ll lose access to Defender for IoT security features. 

To change your plan from a trial to a monthly or annual commitment, you'll need to cancel your trial plan and onboard a new plan in Defender for Endpoint. For more information, see [Defender for IoT integration](/microsoft-365/security/defender-endpoint/enable-microsoft-defender-for-iot-integration).

## How is pricing for Defender for IoT affected now that support for Enterprise IoT networks is in General Availability?

For more information, see the [Microsoft Defender for IoT pricing](https://azure.microsoft.com/pricing/details/iot-defender/) page.

> [!NOTE]
> The Enterprise IoT network sensor is currently in Public Preview.

## How can I resolve billing issues associated with my Defender for IoT plan?

For any billing or technical issues, create a support request in the Azure portal.

## Next steps

For more information on getting started with Enterprise IoT, see:

- [Tutorial: Get started with Enterprise IoT monitoring](tutorial-getting-started-eiot-sensor.md)
- [Manage Defender for IoT plans](how-to-manage-subscriptions.md)
- [Defender for IoT integration](/microsoft-365/security/defender-endpoint/enable-microsoft-defender-for-iot-integration)