---
title: Quickstart - Handle Advanced Messaging and delivery report events
titleSuffix: Azure Communication Services
description: "In this quickstart, you learn how to subscribe for AdvancedMessaging for WhatsApp events."
author: shamkh
manager: camilo.ramirez
ms.author: shamkh
ms.service: azure-communication-services
ms.topic: quickstart 
ms.date: 07/03/2023
ms.custom: template-quickstart 
---

# Quickstart: Handle Advance Messaging Events

Azure Communication Services now enables you to send and receive WhatsApp messages using the Advanced Messaging SDK. Get started with setting up Event Grid events for receiving WhatsApp messages send/receive status reports. Completing this quickstart incurs a small cost of a few USD cents or less in your Azure account.

## Prerequisites

- [Azure account with an active subscription](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Register Event Grid Resource Provider](../../sms/handle-sms-events.md#register-an-event-grid-resource-provider).
- [Create an Azure Communication Services resource](../../create-communication-resource.md).

## About Event Grid

[Event Grid](../../../../event-grid/overview.md) is a cloud-based eventing service. In this article, you learn how to subscribe to [communication service events](../../../../event-grid/event-schema-communication-services.md), and trigger an event to view the result. Typically, you send events to an endpoint that processes the event data and takes actions. In this article, we send the events to a web app that collects and displays the messages.

## [Setup Event Grid Viewer]

The Event Grid Viewer is a sample site that allows you to view incoming events from Event Grid.

1.  Go to this Link [Azure Event Grid Viewer - Code Samples \| Microsoft Learn](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/). Deploy the Event Grid Viewer sample by clicking the "Deploy to Azure" button.
    
    :::image type="content" source="./media/handle-advanced-messaging-events/eventgrid-viewer.png" alt-text="Screenshot that shows the Event Grid Viewer Sample Page with Deploy To Azure option.":::

2.  After clicking the "Deploy to Azure" button, fill in the required fields (since the site name needs to be globally unique as it creates a DNS entry, it's recommended to include your alias in the name for this step).

    :::image type="content" source="./media/handle-advanced-messaging-events/customdeployment.png" alt-text="Screenshot that shows Custom deployment of Events Viewer web app and properties you need to provide to successfully deploy.":::

3.  Then select **Review + Create**.

4.  After the deployment completes, select on the App Service resource to open it.

    :::image type="content" source="./media/handle-advanced-messaging-events/eventviewer-webapp.png" alt-text="Screenshot that shows Events Viewer web app.":::

5.  On the resource overview page, select on the copy button next to the "Default Domain" property.

    :::image type="content" source="./media/handle-advanced-messaging-events/defaultdomain.png" alt-text="Screenshot that shows URL of Events Viewer web app.":::

6.  The URL for the Event Grid Viewer is the Site Name you used to create the deployment with the path "/api/update" appended.
    For example: "https://{{site-name}}.azurewebsites.net/api/updates". You'll need it in the next step and during the creation of the demo app.

## [Subscribe to Advanced Messaging Events]

1.  Open your Communication Services resource in the Azure portal, navigate to the **Events** option in left panel, and select **+Event Subscription**.
    
    :::image type="content" source="./media/handle-advanced-messaging-events/eventsubscription.png" alt-text="Screenshot that shows Azure Communication Services Events subscription option and allows you to subscribe to Advanced Messaging events.":::

2.  Fill in the details for the new event subscription:

    1.  Subscription name.

    1.  System topic name--Enter a unique name, unless this name is already prefilled with a topic from your subscription.

    1.  Event types-Select the two new Advanced messaging events from the list.

        :::image type="content" source="./media/handle-advanced-messaging-events/createeventssubcription.png" alt-text="Screenshot that shows create event subscription properties.":::

    1.  For endpoint type, select **"Webhook"** and enter the URL for the Event Grid Viewer we created in the "Setup Event Grid Viewer" step with the path "/api/updates" appended. For example: https://{{site-name}}.azurewebsites.net/api/updates.

        :::image type="content" source="./media/handle-advanced-messaging-events/eventwebhookdetails.png" alt-text="Screenshot that shows to update webhook url of event subscription to receive events.":::

    1.  select **Create**.

3.  Now if you navigate back to the "Events" option in left panel of your ACS resource, you should be able to see the new event subscription with the Advanced Messaging events.

    :::image type="content" source="./media/handle-advanced-messaging-events/verifyevents.png" alt-text="Screenshot that shows two Advanced messaging events subscribed.":::

## Clean up resources

If you want to clean up and remove a Communication Services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it. Learn more about [cleaning up resources](../../create-communication-resource.md#clean-up-resources).

## Next steps

Advance to the next article to learn how to use Advanced Messaging SDK for WhatsApp messaging.
> [!div class="nextstepaction"]
> [Advanced Messaging SDK Getting Started](./get-started.md).

