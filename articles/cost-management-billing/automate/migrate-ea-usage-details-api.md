---
title: Migrate from the EA Usage Details APIs
titleSuffix: Azure Cost Management + Billing
description: This article has information to help you migrate from the EA Usage Details APIs.
author: bandersmsft
ms.author: banders
ms.date: 06/01/2022
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
---

# Migrate from EA Usage Details APIs

EA customers who were previously using the Enterprise Reporting APIs behind the *consumption.azure.com* endpoint to obtain usage details and marketplace charges need to migrate to new and improved solutions. Instructions to do this are outlined below along with contract differences between the old API and the new solutions. Please note that moving forward the dataset will be referred to as "cost details" instead of "usage details".

## New solutions generally available

The table below provides a summary of the the migration destinations that are available along with a summary of what to consider when choosing which solution is best for you.

| Solution | Description | Considerations | Onboarding info |
| --- | --- | --- | --- |
| **Exports** | Recurring data dumps to storage on a schedule | <ul><li>The most scalable solution for your workloads. <li>Can be configured to use file partitioning for bigger datasets.<li>Great for establishing and growing a cost dataset that can be integrated with your own queryable data stores.<li>Requires access to a storage account that can hold the data.</ul> | <ul><li>[Configure in Azure Portal](../costs/tutorial-export-acm-data.md)<li>[Automate Export creation with the API]()<li>[Export API Reference]()</ul> |
| **Cost Details API** | On demand download | <ul><li>Useful for small cost datasets.<li>Useful for scenarios where Exports to Azure storage are not feasible due to security or manageability concerns.</ul> | <ul><li>[Get small cost datasets on demand](get-small-usage-datasets-on-demand.md)<li>[Cost Details API Reference](../index.yml)</ul> |

Generally we recommend using [Exports](../costs/tutorial-export-acm-data.md) if you have ongoing data ingestion needs and/or a large monthly cost details dataset. For more information, see [Ingest cost details data](automation-ingest-usage-details-overview.md). If you need additional information to help you make a decision for your workload, see [Cost details best practices](usage-details-best-practices.md).

### Assign permissions to an SPN to call the APIs

If you are looking to call either the Exports or Cost Details APIs programmatically, you will need to configure a Service Principal with the correct permission. For more information, see [Assign permissions to ACM APIs](cost-management-api-permissions.md).

### Avoid the Microsoft.Consumption Usage Details API

Please note that the [Consumption Usage Details API]() is another endpoint that currently supports EA customers. Do not migrate to this API. Migrate to either Exports or the Cost Details API, as outlined earlier in this document. The Consumption Usage Details API will be deprecated in the future and is located behind the endpoint below.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2021-10-01
```

This API is a synchronous endpoint and will be unable to scale as both your spending and the size of your month over month cost dataset increases. If you are currently using the Consumption Usage Details API, we recommend migrating off of it to either Exports of the Cost Details API as soon as possible. A formal deprecation announcement will be made at a future date and a timeline for retirement will be provided. To learn more about migrating away from Consumption Usage Details, see [Migrate from Consumption Usage Details API]().

## Migration benefits

Our new solutions provide many benefits over the EA Reporting Usage Details APIs. Here's a summary:

- **Security and stability** - New solutions require Service Principal and/or user tokens in order to access data. This is much more secure than the API keys that are used for authenticating to the EA Reporting APIs. Keys in these legacy APIs are valid for 6 months and can expose sensitive financial data if leaked. Additionally, if keys are not renewed and integrated into workloads prior to their 6 month expiry data access is revoked. This breaks customer workloads.
- **Scalability** - The EA Reporting APIs are not built to scale well as your Azure usage increases. The usage details dataset can get extremely large as you deploy more resources into the cloud. The new solutions are asynchronous and have extensive infrastructure enhancements behind them to ensure successful downloads for any size dataset.
- **Single dataset for all usage details** - Azure and Azure Marketplace usage details have been merged into one dataset in the new solutions. This reduces the number of APIs that you need to call to see all your charges.
- **Purchase amortization** - Customers who purchase Reservations can see an Amortized view of their costs using the new solutions.
- **Schema consistency** - Each solution that is available provides files with matching fields. This allows you to easily move between solutions based on your scenario.
- **Cost Allocation integration** - Enterprise Agreement and Microsoft Customer Agreement customers can use the new solution to view charges in relation to the cost allocation rules that they have configured. For more information about cost allocation, see [Allocate costs](../costs/allocate-costs.md).
- **Go forward improvements** - The new solutions are being actively developed moving forward. They will receive all new features as they are released.

## Enterprise Usage APIs to migrate off

The table below summarizes the different APIs that you may be using today to ingest cost details data. If you are using one of the APIs below you will need to migrate to one of the new solutions outlined above. Please note that all APIs below are behind the *https://consumption.azure.com* endpoint.

| Endpoint | API Comments | 
| --- | ---|
| /v3/enrollments/{enrollmentNumber}/usagedetails/download?billingPeriod={billingPeriod} | <ul><li>API method: GET<li>Synchronous (non polling)<li>Data format: Csv</ul> |
| /v3/enrollments/{enrollmentNumber}/usagedetails/download?startTime=2017-01-01&endTime=2017-01-10 | <ul><li>API method: GET<li>Synchronous (non polling)<li>Data format: Csv</ul> |
| /v3/enrollments/{enrollmentNumber}/usagedetails | <ul><li>API method: GET<li>Synchronous (non polling)<li>Data format: Json</ul> | 
| /v3/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails | <ul><li>API method: GET<li>Synchronous (non polling)<li>Data format: Json</ul> |
| /v3/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10 | <ul><li>API method: GET<li>Synchronous (non polling)<li>Data format: Json</ul> |
| /v3/enrollments/{enrollmentNumber}/usagedetails/submit?billingPeriod={billingPeriod} | <ul><li>API method: POST<li>Asynchronous (polling based)<li>Data format: Csv</ul> |
| /v3/enrollments/{enrollmentNumber}/usagedetails/submit?startTime=2017-04-01&endTime=2017-04-10 | <ul><li>API method: POST<li>Asynchronous (polling based)<li>Data format: Csv</ul> |

## Enterprise Marketplace Store Charge APIs to migrate off

In addition to the usage details APIs outlined above, you will need to migrate off the [Enterprise Marketplace Store Charge APIs](). All Azure and Marketplace charges have been merged into a single file that is available through the new solutions. You can identify which charges are "Azure" versus "Marketplace" charges by using the *PublisherType* field that is available in the new dataset. The table below outlines the APIs that are applicable. Please note that all APIs below are behind the *https://consumption.azure.com* endpoint.

| Endpoint | API Comments | 
| --- | --- |
| /v3/enrollments/{enrollmentNumber}/marketplacecharges | <ul><li>API method: GET<li>Synchronous (non polling)<li>Data format: Json</ul> |
| /v3/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/marketplacecharges | <ul><li>API method: GET<li>Synchronous (non polling)<li>Data format: Json</ul> |
| /v3/enrollments/{enrollmentNumber}/marketplacechargesbycustomdate?startTime=2017-01-01&endTime=2017-01-10 | <ul><li>API method: GET<li>Synchronous (non polling)<li>Data format: Json</ul> | 

## Data field mapping

The table below provides a summary of the old fields available in the solutions you are currently using along with the field to use in the new solutions.

| Old field | New field | Comments |
| --- | ---| -- |
| serviceName | MeterCategory |  |
| serviceTier | MeterSubCategory |  |
| location | ResourceLocation |  |
| chargesBilledSeparately | isAzureCreditEligible | Note that these properties are opposites. If isAzureCreditEnabled is true, ChargesBilledSeparately would be false. |
| partNumber | PartNumber |  |
| resourceGuid | MeterId |  |
| offerId | OfferId |  |
| cost | CostInBillingCurrency |  |
| accountId | AccountId |  |
| productId |  | **WHAT IS EQUIVALENT????** |
| resourceLocationId |  | Not available. |
| consumedServiceId | ConsumedService |  |
| departmentId | InvoiceSectionId |  |
| accountOwnerEmail | AccountOwnerId |  |
| accountName | AccountName |  |
| subscriptionId | SubscriptionId |  |
| subscriptionGuid | SubscriptionId |  |
| subscriptionName | SubscriptionName |  |
| date | Date |  |
| product | ProductName |  |
| meterId | MeterId |  |
| meterCategory | MeterCategory |  |
| meterSubCategory | MeterSubCategory |  |
| meterRegion | MeterRegion |  |
| meterName | MeterName |  |
| consumedQuantity | Quantity |  |
| resourceRate | EffectivePrice |  |
| resourceLocation | ResourceLocation |  |
| consumedService | ConsumedService |  |
| instanceId | ResourceId |  |
| serviceInfo1 | ServiceInfo1 |  |
| serviceInfo2 | ServiceInfo2 |  |
| additionalInfo | AdditionalInfo |  |
| tags | Tags |  |
| storeServiceIdentifier |  | Not available. |
| departmentName | InvoiceSectionName |  |
| costCenter | CostCenter |  |
| unitOfMeasure | UnitOfMeasure |  |
| resourceGroup | ResourceGroup |  |
| isRecurringCharge |  | Not available. |
| extendedCost | CostInBillingCurrency |  |
| planName | PlanName |  |
| publisherName | PublisherName |  |
| orderNumber |  | Not available. |
| usageStartDate | Date |  |
| usageEndDate | Date |  |

## Next steps

- Read the [Migrate from EA Reporting to ARM APIs overview](migrate-ea-reporting-arm-apis-overview.md) article.
