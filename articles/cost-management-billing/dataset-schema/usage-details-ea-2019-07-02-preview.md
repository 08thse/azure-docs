---
title: Enterprise Agreement cost details file schema - version 2019-07-02-preview
description: Learn about the data fields available in the Enterprise Agreement cost details file for version 2019-07-02-preview.
author: bandersmsft
ms.reviewer: jojo
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 02/16/2024
ms.author: banders
---

# Enterprise Agreement cost details file schema - version 2019-07-02-preview

> [!NOTE]
> This article applies to the Enterprise Agreement cost details file schema - version 2019-07-02-preview. For other versions, see the [dataset schema index](schema-index.md).

This article lists the cost details (formerly known as usage details) fields found in cost details files. The cost details file is a data file that contains all of the cost details for the Azure services that were used.

## Cost details data file fields

Version: 2019-07-02-preview

|Fields|Description|
|------|------|
|DepartmentName|Name of the EA department or MCA invoice section.|
|AccountName|Display name of the EA enrollment account or pay-as-you-go billing account.|
|AccountOwnerId|Unique identifier for the EA enrollment account or pay-as-you-go billing account.|
|SubscriptionGuid|Unique identifier for the Azure subscription.|
|SubscriptionName|Name of the Azure subscription.|
|ResourceGroup|Name of the resource group the resource is in. Not all charges come from resources deployed to resource groups. Charges that don't have a resource group are shown as null or empty, `Others`, or `Not applicable`.|
|ResourceLocation|Datacenter location where the resource is running. See `Location`.|
|UsageDateTime|MISSING.|
|ProductName|Name of the product.|
|MeterCategory|Name of the classification category for the meter. For example, `Cloud services` and `Networking`.|
|MeterSubcategory|Name of the meter subclassification category.|
|MeterId|The unique identifier for the meter.|
|MeterName|The name of the meter.|
|MeterRegion|Name of the datacenter location for services priced based on location. See `Location`.|
|UnitOfMeasure|The unit of measure for billing for the service. For example, compute services are billed per hour.|
|UsageQuantity|MISSING.|
|ResourceRate|MISSING.|
|PreTaxCost|MISSING.|
|CostCenter|The cost center defined for the subscription for tracking costs (only available in open billing periods for MCA accounts).|
|ConsumedService|Name of the service the charge is associated with.|
|ResourceType|MISSING.|
|InstanceId|MISSING.|
|Tags|Tags assigned to the resource. Doesn't include resource group tags. Can be used to group or distribute costs for internal chargeback. For more information, see [Organize your Azure resources with tags](../../azure-resource-manager/management/tag-resources.md).|
|OfferId|Name of the offer purchased.|
|AdditionalInfo|Service-specific metadata. For example, an image type for a virtual machine.|
|ServiceInfo1|Service-specific metadata.|
|ServiceInfo2|Legacy field with optional service-specific metadata.|
|ReservationId|Unique identifier for the purchased reservation instance.|
|ReservationName|Name of the purchased reservation instance.|
|UnitPrice|The price per unit for the charge.|
|ProductOrderId|Unique identifier for the product order.|
|ProductOrderName|Unique name for the product order.|
|Term|Displays the term for the validity of the offer. For example: For reserved instances, it displays 12 months as the Term. For one-time purchases or recurring purchases, Term is one month (SaaS, Marketplace Support). Not applicable for Azure consumption.|
|PublisherType|Supported values:`Microsoft`, `Azure`, `AWS`, and `Marketplace`. Values are `Microsoft` for MCA accounts and `Azure` for EA and pay-as-you-go accounts.|
|ChargeType|Indicates whether the charge represents usage (Usage), a purchase (Purchase), or a refund (Refund).|
|ResourceName|Name of the resource. Not all charges come from deployed resources. Charges that don't have a resource type are shown as null or empty, `Others`, or `Not applicable`.|
|IsActualCost|MISSING.|
|IsAmortizedCost|MISSING.|
|Frequency|Indicates whether a charge is expected to repeat. Charges can either happen once (OneTime), repeat on a monthly or yearly basis (Recurring), or be based on usage (UsageBased).|
|Provider|MISSING.|
