---
title: Cost Management API latency and rate limits
titleSuffix: Azure Cost Management + Billing
description: This article explains why the Cost Management API has latency and rate limits.
author: bandersmsft
ms.author: banders
ms.date: 05/26/2022
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
---

# Cost Management API latency and rate limits

The Cost Management APIs are very resource intensive due to the large size of the underlying cost details dataset. We recommend calling the APIs as needed to ingest data but advise against building workloads that pull data more often than once per day. Cost Management data is refreshed every four hours as new cost data is received from Azure resource providers and other sources. Calling more frequently doesn't provide more data. Instead, it creates increased load. To learn more about how often data changes and how data latency is handled, see [Understand cost management data](../costs/understand-cost-mgt-data.md).

This article outlines rate limits for the Cost Management APIs. The rate limits outlined below are not exhaustive and more limits will be added in the future. It is important to design services that abide by the rate limits and back off call frequency when throttling occurs. The Cost Management APIs provide a suggested time period to wait when throttling occurs in the `retry-after` header. In order to enforce the API fair use policy, we reserve the right to lower rate limits as needed. We also reserve the right to block customers temporarily should issues occur due to abusive call patterns.

## Error code 429 - Call count has exceeded rate limits

To enable a consistent experience for all Cost Management subscribers, Cost Management APIs are rate limited. When you reach the limit, you receive the HTTP status code `429: Too many requests`. Please wait for the period of time specific in the `retry-after` header before calling again.

## Cost Details API rate limits

Here are the rate limits for the [Cost Details API - UNPUBLISHED](../index.yml).

| Program | Scope | Limit | Rate Limit | Comments |
| --- | --- | --- | --- | --- |
| EA/MCA | All | Per distinct scope | 2 calls/minute, 10 calls/hour, 50 calls/day | See the following example URIs. |
| EA/MCA - CostDetailsOperation | All | Per Operation ID | 2 calls/minute | See the following example URIs. |
| EA/MCA | Subscription Scope | Per Tenant ID | 10 calls/minute | Across all subscriptions at a unique tenant ID level. See the following example URIs. |
| EA/MCA | Subscription Scope | Per Billing Account | 10 calls/minute | Across all subscriptions at a unique billing account level. See the following example URIs. |
| EA/MCA | All Non-subscription scope | Per Billing Account | 5 calls/minute | Across all non-subscription scope calls (including billing profile and your scope calls) at a unique billing account level. See the following example URIs. |
| EA/MCA | All | Per client application ID | 30 calls/minute | Across all calls made by the client application. |

### API call pattern guidance per scenario

The cost details dataset is often multiple GBs or more month over month as your costs increase. Your data ingestion workloads need to use call patterns that meet your scenarios while also abiding by the API rate limits. Use the following guidance about how to call the API relating to the rate limits.

If your call is throttled, review the `retry-after` header that's shown in the error response to determine when to call the API next.

#### Month-to-date datasets under a single scope

You might need to pull down a large multi GB dataset for a single scope, such as a Billing Account or Subscription. If so, we recommend placing multiple API calls for smaller date ranges in order to pull down all the data.

If you need to chunk the data per day to have reasonably sized file partitions, it might take three hours to fully download the dataset for the month across all partitions.

#### Dataset requiring calls across multiple scopes

You might need to pull data across many different scopes or subscopes. Examples include:

* *A large enterprise needs to pull down data across N different subscriptions that they manage.*
  * With rate limiting in place, you'll be able to ingest data for 600 subscriptions per hour. If you need to pull down data faster, we recommend using a scope that is above subscription.
* *A partner needs to pull down data for N customers under their billing account.*
  * In this instance, we recommend placing a call per customer. With rate limiting in place, you'll be able to ingest data for 300 customers per hour. If you need to chunk your requests by date range due to the dataset size, the number will be less.
* *A cloud solutions provider needs to pull down data across N Billing Accounts that reside in different tenants.*
  * We recommend using the same Client Application ID for all calls. API calls will be rate limited across both the application used and the other calls being made to the existing scopes by either the provider or the end customer. With rate limiting in place, provider client applications will be able to make 30 total calls per minute. If data needs to be chunked or multiple calls need to be made per tenant, then data ingestion time will increase accordingly.

#### Multi-month one-time historical dataset download

For example, customers need to seed a 13 month historical dataset for a given scope, such as a Billing Account. If so, we recommend placing a call for data 1 month at a time. If your dataset is too large, then we recommend reducing the date range until the data size is manageable for your workloads. Decreasing your date range will affect the time it will take for you to fully ingest the dataset. If you need to chunk your data per day, then it will take about eight days for you to fully ingest your data. If you have a 30-GB month-over-month dataset, then a 13 month historical dataset will be around 390 GB.

### Rate-limited URI examples

Examples below show which API calls would apply to the rate limits outlined in the rate limit table above.

#### Legacy/Modern - all scopes - per distinct scope
```http
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/CostDetails?api-version=2022-05-01
```

```http
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/providers/Microsoft.CostManagement/CostDetails?api-version=2022-05-01
```

```http
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/customers/{customerId}/providers/Microsoft.CostManagement/CostDetails?api-version=2022-05-01
```

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.CostManagement/CostDetails?api-version=2022-05-01
```

#### Legacy/Modern - CostDetailsOperation - all scopes - per operation ID

```http
https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.CostManagement/CostDetailsOperationStatus/{operationId}?api-version=2022-05-01
```

#### Legacy/Modern - subscription scope - per tenant ID

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.CostManagement/CostDetails?api-version=2022-05-01
```

#### Legacy/Modern - subscription scope - per billing account

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.CostManagement/CostDetails?api-version=2022-05-01
```

#### Legacy/Modern - all non-subscription scope - per billing account

```http
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/CostDetails?api-version=2022-05-01
```

```http
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/providers/Microsoft.CostManagement/CostDetails?api-version=2022-05-01
```

```http
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/customers/{customerId}/providers/Microsoft.CostManagement/CostDetails?api-version=2022-05-01
```

## Consumption Usage Details API rate limits

Rate limits for the [Consumption Usage Details API](/rest/api/consumption/usage-details) are outlined below. The API should only be used by legacy pay-as-you-go users. If you're an Enterprise or Microsoft Customer Agreement user, you should use the [Cost Details API - UNPUBLISHED](../index.yml) or [Exports](../costs/tutorial-export-acm-data.md).

|Program|Scope|Limit|Rate Limit|Comments|
|----|----|----|----|----|
|EA/MCA|Subscription Scope|Per Tenant ID|10 calls/minute |Across all subscriptions at a unique tenant ID level|
|EA/MCA|Subscription Scope|Per Billing Account|10 calls/minute |Across all subscriptions at a unique billing account level|
|MCA|All Non-subscription scope|Per Billing Account|6 calls/minute |Across all non-subscription scope calls (including billing profile and customer scope calls) at a unique billing account level|
|EA|Management Group|Per management group|2 calls/minute | |
|EA|Per Billing Account|Per Billing Account|6 calls/minute | |
|EA|Per Department|Per Department|2 calls/minute | |
|EA|Per EnrollmentAccount|Per EnrollmentAccount|2 calls/minute | |
|EA/MCA|All|Per unique skipToken|2 calls/minute |Usage Details provide next link with a URI including skip token. Here unique skip token calls are being considered next page calls that have unique skip token. Every next link URI is considered as unique skip token call.|
|EA/MCA|Subscription Scope|Per Tenant ID|10 calls/minute |Across all subscriptions at a unique tenant ID level|

## Charges API rate limits

Rate limits for the [Charges API](/rest/api/consumption/charges) are outlined below.

| Program | Scope | Limit | Rate Limit | Comments |
| --- | --- | --- | --- | --- |
| EA/MCA | All scopes | Per tenant | 125 calls / minute |   |
| EA/MCA | Billing account | Per billing account | 20 calls / minute |  |

## Price Sheet API rate limits

Rate limits for the [Price Sheet API](/rest/api/consumption/price-sheet) are outlined below.

| Program | Scope | Limit | Rate Limit | Comments |
| --- | --- | --- | --- | --- |
| EA/MCA | Billing account and subscription | Per billing account | 30 calls / minute |  |
| EA/MCA | Billing account and subscription | Per subscription | 150 calls / minute |  |

## Reservation Utilization Summaries API rate limits

Rate limits for the [Reservation Utilization Summaries API](/rest/api/consumption/reservations-summaries) are outlined below.

| Program | Scope | Limit | Rate Limit | Comments |
| --- | --- | --- | --- | --- |
| EA/MCA |  | Per distinct URL | 10 calls / minute | Across all scopes for a given URL + user (tenantID + objectID) |

## Reservation Utilization Details API rate limits

Rate limits for the [Reservation Utilization Details API](/rest/api/consumption/reservations-details) are outlined below.

| Program | Scope | Limit | Rate Limit | Comments |
| --- | --- | --- | --- | --- |
| EA/MCA |  | Per distinct URL | 10 calls / minute | Across all scopes for a given URL + user (tenantID + objectID) |

## Reservation Transactions API rate limits

Rate limits for the [Reservation Transactions API](/rest/api/consumption/reservation-transactions) are outlined below.

| Program | Scope | Limit | Rate Limit | Comments |
| --- | --- | --- | --- | --- |
| EA/MCA |  | Per distinct URL | 10 calls / minute | Across all scopes for a given URL + user (tenantID + objectID) |

## Next steps

- Learn more about Cost Management + Billing automation at [Cost Management automation overview](automation-overview.md).
