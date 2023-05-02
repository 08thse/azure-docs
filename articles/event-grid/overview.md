---
ms.date: 04/21/2023
author: jfggdl
ms.author: jafernan
title: Overview
description: Learn about Event Grid's http and MQTT messaging capabilities.
ms.topic: conceptual
---

# What is Azure Event Grid?
<i><b style="color:#b30000; font-size: 16px">Early access reviewer</b></i>
>[!Important]
> **Early access reviewer**: please note that the following aspects of this article are still in construction. They should be completed by May 4th, 2023.
>* Regions where new features are available needs to be corrected and augmented.
>* Images might appear broken. If you get an emoji face with a pencil, that means that it not yet available and we are working on it. Event if the the image shows, please note that some the imgages may still being worked on by the Conceptual Artist given some comments that we provided.

Azure Event Grid is a highly scalable, fully managed Pub Sub message distribution service that offers flexible message consumption patterns using the MQTT and HTTP protocols. With Azure Event Grid, you can build data pipelines with device data, integrate applications, and build event-driven serverless architectures. Event Grid enables clients to publish and subscribe to messages over the MQTT v3.1.1 and v5.0 protocols to support Internet of Things (IoT) solutions. Using HTTP, Event Grid enables you to build event-driven solutions where a publisher service announces its system state changes (events) to subscriber applications. Event Grid can be configured to send events to subscribers (push delivery) or subscribers can connect to Event Grid to read events (pull delivery). Event Grid supports [CloudEvents 1.0](https://github.com/cloudevents/spec) specification to provide interoperability across systems. 

:::image type="content" source="media/overview/ic_fluent_emoji_edit_24_regular.svg" alt-text="High-level diagram of Event Grid that shows publishers and subscribers using MQTT and HTTP protocols." lightbox="media/overview/ic_fluent_emoji_edit_24_regular.svg":::

Azure Event Grid is a generally available service deployed across availability zones in all regions that support them. For a list of regions supported by Event Grid see [Products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all). 

**Note:** 

The following features have been released as public preview features in May 2023:

- MQTT v3.1.1 and v5.0 support
- Pull-style event consumption (HTTP)

The initial regions where these features are available are:

- East US 
- West Europe

## What can I do with Event Grid?

Azure Event Grid is used at different stages of data pipelines to achieve a diverse set of integration goals. 

**MQTT messaging** - <i><b style="color:#b30000; font-size: 16px">new</b></i>. IoT devices and applications can communicate with each other over MQTT. Event Grid can also be used to route MQTT messages to Azure services or custom endpoints for further data analysis, visualization, or storage. This integration with Azure services enable you to build data pipelines that start with data ingestion from your IoT devices.

**Data distribution using push and pull (<i><b style="color:#b30000; font-size: 16px">new</b></i>) delivery modes**. At any point in a data pipeline, HTTP applications can consume messages using push or pull APIs. The source of the data may include MQTT clients’ data, but also includes the following data sources that send their events over HTTP:

- Azure services
- Your custom applications
- External partner (SaaS) systems

When configuring Event Grid for push delivery, Event Grid can send data to [destinations](event-handlers.md) that include your own application webhooks and Azure services.

## Capabilities

Event Grid offers a rich mixture of features. These include:

### MQTT messaging

- **[MQTT v3.1.1 and MQTT v5.0](mqtt-mqtt-support.md)** support – use any open source MQTT client library to communicate with the service.
- **Custom topics with wildcards support** - leverage your own topic structure.
- **Publish-subscribe messaging model** - communicate efficiently using one-to-many, many-to-one, and one-to-one messaging patterns.
- **[Built-in cloud integration](mqtt-routing.md) ** - route your MQTT messages to Azure services or custom webhooks for further processing.
- **Flexible and fine-grained [access control model](mqtt-access-control.md)** - group clients and topic to simplify access control management, and use the variable support in topic templates for a fine-grained access control.
- **X.509 certificate [authentication](mqtt-client-authentication.md) ** - authenticate your devices the standard mechanism for device authentication in the IoT industry.
- **TLS 1.2 and TLS 1.3 support** - secure your client communication using robust encryption protocols.
- **Multi-session support** - connect your applications as MQTT clients in a reliable and scalable way.
- **MQTT over WebSockets** - receive MQTT data directly into a web browser.


### Event messaging (HTTP)

**Flexible event consumption model** – when using HTTP, consume events using pull or push delivery mode.

**System events** – Get up and running quickly with built-in Azure service events.

**Your own application events** - Use Event Grid to route, filter, and reliably deliver custom events from your app.

**Partner events** – Subscribe to your partner SaaS provider events and process them on Azure.

**Advanced filtering** – Filter on event type or other event attributes to make sure your event handlers or consumer apps receive only relevant events.

**Reliability** – Push delivery features a 24-hour retry mechanish with exponential backoff to make sure events are delivered. Using pull delivery, your application has full control over event consumption.

**High throughput** - Build high-volume integrated solutions with Event Grid.

## MQTT Messaging

Event Grid enables your clients to communicate on custom MQTT topics using a publish-subscribe messaging model. Event Grid supports clients that publish and subscribe to messages over MQTT v3.1.1, MQTT v3.1.1 over WebSockets, MQTT v5, and MQTT v5 over WebSockets. Event Grid authenticates clients and authorizes publish and subscribe requests according to configured permissions. Event Grid allows you to send MQTT messages to the cloud for data analysis, storage, and visualizations, among other use cases.
<br/>

:::image type="content" source="media/overview/mqtt-messaging.svg" alt-text="High-level diagram of Event Grid that shows bidirectional MQTT communication with publisher and subscriber clients." lightbox="media/overivew/mqtt-messaging.svg":::
<br/>
Event Grid’s MQTT support enables you to accomplish the following scenarios.

### Ingest IoT telemetry
:::image type="content" source="media/overview/ingest-telemetry.svg" alt-text="High-level diagram of Event Grid that shows publishers and subscribers using MQTT and HTTP protocols." lightbox="media/overivew/ingest-telemetry.svg":::

Ingest telemetry using a **many-to-one messaging** pattern on custom MQTT topics. For example, use Event Grid to send telemetry from multiple IoT devices to a cloud application. This pattern will enable the application to offload the burden of managing the high number of connections with devices to Event Grid.

### Command and control
:::image type="content" source="media/overview/command-control.svg" alt-text="High-level diagram of Event Grid that shows a cloud application sending a command message over MQTT to a device using request and response topics." lightbox="media/overview/command-control.svg":::

Control your MQTT clients using the **request-response** (one-to-one) message pattern. For example, use Event Grid to send a command from a cloud application to an IoT device. 

### Broadcast alerts
:::image type="content" source="media/overview/broadcast-alerts.svg" alt-text="High-level diagram of Event Grid that shows a cloud application sending a alert message over MQTT to several devices." lightbox="media/overview/broadcast-alerts.svg":::

Broadcast alerts to a fleet of clients using the **one-to-many** messaging pattern on custom MQTT topics. For example, use Event Grid to send an alert from a cloud application to multiple IoT devices. This pattern enables the application to publish only one message that the service replicates for every interested client.

### Integrate MQTT data
:::image type="content" source="media/overview/integrate-data.svg" alt-text="High-level diagram of Event Grid that shows several IoT devices on vehicles sending health data over MQTT to Event Grid. This in turn send those messages to Event Hubs. Finally, Event Hubs send those messages to Azure Stream Analytics." lightbox="media/overview/integrate-data.svg":::

Integrate data from your MQTT clients by routing MQTT messages to Azure services and Webhooks through the [HTTP Push delivery](pull-and-push-delivery-overview.md@push-delivery) functionality. For example, use Event Grid to route telemetry from your IoT devices to Event Hubs and then to Azure Stream Analytics to gain insights from your device telemetry.

## Push delivery of discrete events

Event Grid can be configured to send events to a diverse set of Azure services or webhooks using push event delivery. Event sources include your custom applications, Azure services, and partner (SaaS) services that publish events announcing system state changes (aka "discrete" events). In turn, Event Grid delivers those events to configured subscribers’ destinations. 

Event Grid’s push delivery allows you to realize the following use cases.

### Build event-driven serverless solutions
:::image type="content" source="media/overview/build-event-serverless.svg" alt-text="High-level diagram of Azure Functions publishing events to Event Grid using HTTP. Event Grid then sends those events to Azure Logic Apps so that it can send emails and store the events received." lightbox="media/overview/build-event-serverless.svg":::

Use Event Grid to build serverless solutions with Azure Functions Apps, Logic Apps, and API Management. Using serverless services with Event Grid affords you a level of productivity, effort economy, and integration superior to that of classical computing models where you have to procure, manage, secure, and maintain all infrastructure deployed.  

### Receive events from Azure services
:::image type="content" source="media/overview/receive-events-azure.svg" alt-text="High-level diagram of an Azure Blob Storage publishing its events to Event Grid using HTTP. Event Grid then sends those events to several event handlers, which are either webhooks or Azure services" lightbox="media/overview/receive-events-azure.svg":::

Azure services make their [events available](system-topics.md) so that you can automate your operations. For example, you can configure Event Grid to receive an event when a new bBlob has been created on a Azure Storage Account so that your downstream application can read and process its content.

### Receive events from your applications
:::image type="content" source="media/overview/receive-events-azure.svg" alt-text="High-level diagram of an customer application publishing its events to Event Grid using HTTP. Event Grid then sends those events to several event handlers, which are either webhooks or Azure services" lightbox="media/overview/receive-events-azure.svg":::

Your own service or application publishes events to Event Grid that subscriber applications process. Event Grid features [Custom Topics](custom-topics.md) to address basic integration scenarios and [Domains](event-domains.md) to offer a simple management and routing model when you need to distribute events to hundreds or thousands of different groups.

### Receive events from partner (SaaS providers)
:::image type="content" source="media/overview/receive-saas-providers.svg" alt-text="High-level diagram of external partner application (SaaS) publishing its events to Event Grid using HTTP. Event Grid then sends those events to several event handlers, which are either webhooks or Azure services" lightbox="media/overview/receive-saas-providers.svg":::

A multi-tenant SaaS provider or platform can publish their events to Event Grid through a feature called [Partner Events](partner-events-overview.md). You can [subscribe to those events](subscribe-to-partner-events.md) and automate tasks, for example. Events from the following partners are currently available:

- [Auth0](auth0-overview.md)
- [Microsoft Graph API](subscribe-to-graph-api-events.md). Through Microsoft Graph API you can get events from [Azure AD](azure-active-directory-events.md), [Microsoft Outlook](outlook-events.md), [Teams](teams-events.md), Conversations, security alerts, and Universal Print.
- [Tribal Group](subscribe-to-tribal-group-events.md)
- [SAP](subscribe-to-sap-events.md)

### Event Handlers

An event subscription is a generic configuration resource that allows you to define the event handler or destination to which events are sent using push delivery.  The following [event handlers](event-handlers.md) are supported: 

- [Webhooks](handler-webhooks.md). Azure Automation runbooks and Logic Apps are supported via webhooks.
- [Azure functions](handler-functions.md)
- [Event Hubs](handler-event-hubs.md)
- [Service Bus queues and topics](handler-service-bus.md)
- [Relay hybrid connections](handler-relay-hybrid-connections.md)
- [Storage queues](handler-storage-queues.md)

## Pull delivery of discrete events

Azure Event Grid features [pull CloudEvents delivery](pull-and-push-delivery-overview.md#push-and-pull-delivery). Using this delivery mode, clients connect to Event Grid to read events. The following use cases can be realized using pull delivery.

### Receive events at your own pace
:::image type="content" source="media/overview/pull-events-at-your-own-pace.svg" alt-text="High-level diagram of a publisher and consumer application. The publisher sends events to Event Grid at a higher pace than the subscriber's event consumption rate." lightbox="media/overview/pull-events-at-your-own-pace.svg":::

One or more clients can connect to Azure Event Grid to read messages at their own pace. Event Grid affords clients full control on events consumption. Your application can receive events at certain times of the day, for example. Your solution can also increase the rate of consumption by adding more clients that read from Event Grid.

### Consume events over a private link
:::image type="content" source="media/overview/consume-private-link-pull-api.svg" alt-text="High-level diagram of a consumer app inside a VNET reading events from Event Grid over a private endpoint inside the VNET." lightbox="media/overview/consume-private-link-pull-api.svg":::

You can configure **private links** to connect to Azure Event Grid to **publish and read** CloudEvents through a [private endpoint](../private-link/private-endpoint-overview.md) in your virtual network. Traffic between your virtual network and Event Grid travels the Microsoft backbone network.

>[!Important]
> Private links are available with pull delivery, not with push delivery. This is not a gap. Private links “…[enables you to access Azure PaaS Services](../private-link/private-link-overview.md)…” That is, private links were designed to be used used when you connect to Event Grid for publishing events or receiving events, not when Event Grid is connecting (sending events) to your webhook or Azure Service.

## How much does Event Grid cost?

Azure Event Grid uses a pay-per-event pricing model. You only pay for what you use. For the push-style delivery that is generally available, the first 100,000 operations per month are free. Examples of operations include event publication, event delivery, delivery attempts, event filter evaluations that refer to event data properties (sometimes referred as Advanced Filters), and events sent to a dead letter location. For details, see the [pricing page](https://azure.microsoft.com/pricing/details/event-grid/).

Event Grid operations involving Namespaces and its resources, including MQTT and pull HTTP delivery operations, are in public preview and are available at no charge today.

## Next Steps

### MQTT messaging

- [Overview](mqtt-overview.md) 
- [Publish and subscribe to MQTT messages](mqtt-publish-and-subscribe-portal.md)
- [Route MQTT messages to Event Hubs](mqtt-routing-to-event-hubs-portal.md)

### Data distribution using pull or push  delivery

- [Pull and push delivery overview](pull-and-push-delivery-overview.md).
- [Concepts](concepts.md)
- Quickstart: [Publish and subscribe to app events using  Namepace topics](publish-events-using-namespace-topics.md).