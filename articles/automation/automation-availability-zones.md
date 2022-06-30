---
title: Availability zones support for Azure Automation
description: This article provides an overview of Azure availability zones and regions for Azure Automation
keywords: automation availability zones.
services: automation
ms.subservice: process-automation
ms.date: 06/29/2022
ms.topic: conceptual 
---

# Availability zones support for Azure Automation

Azure Automation uses [Azure availability zones](../availability-zones/az-overview.md#availability-zones) to provide improved resiliency and high availability to a service instance in a specific Azure region.
 
[Azure availability zones](../availability-zones/az-overview.md#availability-zones) is a 
high-availability offering that protects your applications and data from data center failures.
Availability zones are unique physical locations within an Azure region and each region comprises of one or more data center(s) equipped with independent power, cooling, and networking. To ensure resiliency, there needs to be a minimum of three separate zones in all enabled regions.

A zone redundant Automation account automatically distributes traffic to the Automation account through various management operations and runbook jobs amongst the availability zones in the supported region. The replication is handled at the service level to these physically separate zones, making the service resilient to a zone failure with no impact on the availability of the Automation accounts in the same region.

In the event when a zone is down, there's no action required by you to recover from a zone failure and the service would be accessible through the other available zones. The service detects that the zone is down and automatically distributes the traffic to the available zones as needed.

## Availability zone considerations

- In all Availability zone supported regions, the zone redundancy for Automation accounts is enabled by default and it can't be disabled. It requires no action from your end as it's enabled and managed by the service.
- All new Automation accounts with basic SKU are created with zone redundancy natively.
- All exisiting Automation accounts would become zone redundant automatically. It requires no action from your end.
- In a zone-down scenario, you might expect a brief peformance degradation until the service self-healing rebalances the underlying capacity to adjust to healthy zones. This isn't dependent on zone restoration; the service self-healing state will compensate for a lost zone, using the capacity from other zones.
- In a zone-wide failure scenario, you must follow the guidance provided to set up a disaster recovery for Automation accounts in a secondary region.   
- Availability zone support for Automation accounts supports only [Process Automation](/azure/automation/overview#process-automation) feature to provide an improved resiliency for runbook automation.   

## Supported regions with availability zones

See [Regions and Availability Zones in Azure](/global-infrastructure/geographies/#geographies) for the Azure regions that have availability zones. 
Automation Accounts currently supports the following regions in preview:  
- China North 3
- Qatar Central

## Create a Zone redundant Automation account
You can create a zone redundant Automation account using:
- [Azure portal](/azure/automation/automation-create-standalone-account?tabs=azureportal)
- [Azure Resource Manager (ARM) template](/azure/automation/quickstart-create-automation-account-template)

> [!Note]
> There is no option to select or see Availability zone in the creation flow of the Automation Accounts. It’s a default setting enabled and managed at the service level.  

## Pricing

There's no additional cost associated  to enable the zone redundancy feature in Automation account.  

## Service Level Agreement

There is no change to the [Service Level Agreement](https://azure.microsoft.com/support/legal/sla/automation/v1_1/) with the support of Availability zones in Automation Account. The SLA depends on job start time with a guarantee that at least 99.9% of runbook jobs will start within 30 minutes of their planned start times. 

## Next steps

- Learn more about [regions that support availability zones](/azure/availability-zones/az-region.md).