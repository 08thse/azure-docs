---
title: FAQs about Events in Azure Healthcare APIs
description: This document provides answers to the frequently asked questions about Events.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/14/2022
ms.author: jasteppe
---

# Frequently asked questions (FAQs) about Events

The following are some of the frequently asked questions about Events.

### Can I use Events with a different Fast Hospital Interoperability Resources (FHIR®) service other than the Azure Healthcare APIs FHIR service?

No. The Azure Healthcare APIs Events feature only currently supports the Azure Healthcare APIs FHIR service.

### What FHIR resource events does Events support?

Events are generated from the following FHIR service types:

- **FhirResourceCreated** - The event emitted after a FHIR resource gets created successfully.

- **FhirResourceCreated** - The event emitted after a FHIR resource gets created successfully.

- **FhirResourceDeleted** - The event emitted after a FHIR resource gets soft deleted successfully.

For more information about the FHIR service delete types, see [FHIR Rest API capabilities for Azure Healthcare APIs FHIR service](../../healthcare-apis/fhir/fhir-rest-api-capabilities.md)

### What is the payload of an Events message? 

For a detailed description of the Events message structure and both required and non-required elements, see  [Events troubleshooting guide](events-troubleshooting-guide.md). 

### What is the throughput for the Events messages?

The throughput of FHIR events is governed by the throughput of the FHIR service and the Event Grid. When a request made to the FHIR service is successful, it will return a 2xx HTTP status code. It will also generate a FHIR resource changing event. The current limitation is 5,000 events/second per a workspace for all FHIR service instances in it. If you require events that exceed the current limitation, [Create a support request](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). For more information about Azure support options, see [Azure support plans](https://azure.microsoft.com/support/options/#support-plans). 

### How am I charged for using Events?

There are no extra charges for using Azure Healthcare APIs Events. However, applicable charges for the [Event Grid](https://azure.microsoft.com/pricing/details/event-grid/) might be assessed against your Azure subscription.


### How do I subscribe to multiple FHIR services in the same workspace separately?

You can use the Event Grid filtering feature. There are unique identifiers in the event message payload to differentiate different accounts and workspaces. You can find a global unique identifier for workspace in the `source` field, which is the Azure Resource ID. You can locate the unique FHIR account name in that workspace in the `data.resourceFhirAccount` field. When you create a subscription, you can use the filtering operators to select the events you want to get in that subscription.

  :::image type="content" source="media\event-grid\event-grid-filters.png" alt-text="Screenshot of the Event Grid filters tab." lightbox="media\event-grid\event-grid-filters.png":::


### Can I use the same subscriber for multiple workspaces or multiple FHIR accounts?

Yes, you can. However, we recommend that you use different subscribers for each individual FHIR account to process in isolated scopes.

### Is Event Grid HIPAA and HITRUST compliant? 

Yes, Event Grid is Health Insurance Portability and Accountability Act (HIPAA) and Health Information Trust Alliance (HITRUST) compliant. For more information, see [Microsoft Azure Compliance Offerings](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/).


 ### What is the expected time to receive an Events message?

You should receive your event within 1 second after a successful HTTP request on average. 99.99% of the events should be delivered within 5 seconds unless the limitation has been met.

### Is it possible to receive duplicate Events message?

Yes. The Event Grid guarantees at least one Events message delivery with its push mode. There may be chances that the event delivery request returns with a transient failure status code for random reasons. In this situation, the Event Grid will consider that as a delivery failure and will resend the Events message. For more information, see [Azure Event Grid delivery and retry](../../event-grid/delivery-and-retry.md).


Generally, we recommend that developers ensure idempotency for the event subscriber. The event ID or the combination of all fields in the ```data``` property of the message content are unique per each event. The developer can rely on them to de-duplicate. 
   
## More frequently asked questions
[FAQs about the Azure Healthcare APIs](../healthcare-apis-faqs.md)

[FAQs about Azure Healthcare APIs FHIR service](../fhir/fhir-faq.md)

[FAQs about Azure Healthcare APIs DICOM service](../dicom/dicom-services-faqs.yml)

[FAQs about Azure Healthcare APIs IoT connector](../iot/iot-connector-faqs.md)

(FHIR&#174;) is a registered trademark of [HL7](https://hl7.org/fhir/) and is used with the permission of HL7.
