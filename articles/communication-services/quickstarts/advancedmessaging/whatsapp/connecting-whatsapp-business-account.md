---
title: Register WhatsApp Business Account
titleSuffix: An Azure Communication Services concept document
description: Learn about Communication Service WhatsApp Business Accounts concepts.
author: darmour
manager: sundraman
services: azure-communication-services
ms.author: darmour
ms.date: 06/26/2023
ms.topic: quickstart
ms.service: azure-communication-services
---

# Quickstart: Register WhatsApp Business Account

Get started with the Azure Communication Services Advanced Message, which extends messaging to users on WhatsApp. This feature allows your organization to send and receive messages with WhatsApp users using a WhatsApp Business Account. The Message SDK extends your communications to interact with the large global WhatsApp community for common scenarios:

-   Receiving inquiries from your customers for product feedback or support, price quotes, and rescheduling appointments.
-   Sending your customer's notifications like appointment reminders, product discounts, transaction receipts, and one-time passcodes.

## Prerequisites

1.  [Facebook login account](https://www.facebook.com/index.php)
2.  [Get Azure Communication Service Phonenumber](../..//telephony/get-phone-number.md?tabs=windows&pivots=platform-azp)
    -  With the ability to send and receive SMS messages
    -  With an active Event subscription (SMS received and SMS delivery reports). [Learn how to handle SMS events.](../../telephony/get-phone-number.md?tabs=windows&pivots=platform-azp)
    -  With an Event Grid viewer. [Learn how to deploy an Event Grid viewer](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/).
    -  Phonenumber isn't associated with a WhatsApp Business Account
3.  Or bring your own phone number
    -  With the ability to receive SMS and calls
    -  Phonenumber isn't associated with a WhatsApp Business Account
4.  [Active Meta Business Account](https://www.facebook.com/business/tools/meta-business-suite)

## WhatsApp Business Account Sign-up
[!INCLUDE [WhatsApp Signup](./includes/register-whatsapp-account/whatsapp-signup.md)]

## Selecting a new Meta Business Account
[!INCLUDE [Add WhatsApp Business Account](./includes/register-whatsapp-account/create-new-metabusiness-account.md)]

## Selecting a WhatsApp Business Profile

1. Now that you have selected Meta Business Account, you need to **create/select** a WhatsApp Business profile. Fill out the required information.

:::image type="content" source="./media/register-whatsapp-account/whatsapp-business-account-details.png" alt-text="Screenshot that shows Providing WhatsApp Business account details.":::

2. Once you have completed the form, click **Next** to continue.

## Verify Your WhatsApp Business Number
[!INCLUDE [Verify WhatsApp Business Phonenumber](./includes/register-whatsapp-account/verify-phonenumber.md)]

## Viewing your WhatsApp Account in the Azure Communication Services Portal

You see the account and status listed in the Azure portal along with the other WhatsApp Business accounts that you have connected to Azure Communication Services. Once approved, you can use the WhatsApp Business account to send and receive messages. The status of your WhatsApp Business account is displayed in the Azure portal. Meta reviews your business’s display name. You can learn more about this review process and how to update your business account’s display name in the article this article: [About WhatsApp Business display name](https://www.facebook.com/business/help/338047025165344).

:::image type="content" source="./media/register-whatsapp-account/list-whatsapp-accounts.png" alt-text="Screenshot that shows Listing your WhatsApp accounts in the Azure portal.":::

When you no longer want to use the WhatsApp Business account with Azure Communication Services, you can select the account and click the **Disconnect** button. This option disconnects the account from Azure Communication Services but doesn't delete the account and the account can be reconnected later.

## Create New MetaBusiness Account

Provide the company details to be used in your Meta Business Account then click the **Next** button.
-  Company Name: How you want your company identified to your WhatsApp users.
-  Website: A legitimate web page that verifies your business. 
-  Business Email: You can use the email associated with your Facebook login.
-  Business Phone Number: The phone number that customers can use to contact you.
    
:::image type="content" source="../../media/register-whatsapp-account/creating-new-business-account.png" alt-text="Screenshot that shows Filling out the details of your Meta Business account.":::

> [!NOTE]
> More details on how-to and required information can be found [Here](https://www.facebook.com/business/tools/meta-business-suite)

## Next Steps

In this quickstart, you have learned, how to register your WhatsApp Business Account with Azure Communication Services. Now, you're ready to send and receive WhatsApp messages.

> [!div class="nextstepaction"]
> [Get Started With Advanced Messaging](../../../quickstarts//advancedmessaging/whatsapp/get-started.md)

You might also want to see the following articles: 

-    [WhatsApp Business Help Center](https://www.facebook.com/business/help/524220081677109?id=2129163877102343)

-    [WhatsApp Business Display Name Policy](https://www.facebook.com/business/help/338047025165344)

-    [Business Verification](https://www.facebook.com/business/help/1095661473946872?id=180505742745347) 

-    [Add more Management Accounts](https://www.facebook.com/business/help/2169003770027706)
