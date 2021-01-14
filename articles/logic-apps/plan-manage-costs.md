---
title: Plan to manage costs for Azure Logic Apps
description: Learn how to plan for and manage costs for Azure Logic Apps by using cost analysis in the Azure portal
ms.service: logic-apps
ms.reviewer: logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/31/2021
---

# Plan and manage costs for Azure Logic Apps

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

This article helps you plan and manage costs for Azure Logic Apps. Before you create or add any resources using this service, estimate your costs by using the Azure pricing calculator. After you start using Logic Apps resources, you can set budgets and monitor costs by using [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). To identify areas where you might want to act, you can also review forecasted costs and monitor spending trends.

Keep in mind that costs for Logic Apps are only part of the monthly costs in your Azure bill. Although this article explains how to estimate and manage costs for Logic Apps, you're billed for all the Azure services and resources that are used in your Azure subscription, including any third-party services. After you're familiar with managing costs for Logic Apps, you can apply similar methods to manage costs for all the Azure services used in your subscription.

## Prerequisites

<!--Note for Azure service writer: The section covers prereqs for the Cost Analysis feature. Add other prereqs needed for your service.  -->

[Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) supports most Azure account types. To view all the supported account types, see [Understand Cost Management data](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). To view cost data, you need at least read access for your Azure account.

For information about assigning access to Azure Cost Management data, see [Assign access to data](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## Understand the billing model

Azure Logic Apps runs on Azure infrastructure that [accrues costs](https://azure.microsoft.com/pricing/details/logic-apps/) when you deploy new resources. Make sure that you understand the [billing model for the Logic Apps service along with related Azure resources](logic-apps-pricing.md), and manage any additional costs due to these dependencies when you make changes to deployed resources.

### Costs that typically accrue with Azure Logic Apps

<!--Note to Azure service writer: Include any costs that aren't obvious, hidden, or otherwise might not be present in the pricing calculator or resource creation experience in the Azure portal. You might need to sync with your product team to identify hidden costs. If you're certain that costs accrue only for your service and no others, then omit this section. -->

The Logic Apps service applies different pricing models, based on the resources that you create and use:

* Logic app resources that you create and run in the multi-tenant Logic Apps service use a [consumption pricing model](../logic-apps/logic-apps-pricing.md#consumption-pricing).

* Logic app resources that you create and run in an [integration service environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) use a [fixed pricing model](../logic-apps/logic-apps-pricing.md#fixed-pricing).

Here are other resources that incur costs when you create them for use with logic apps:

* An [integration account](../logic-apps/logic-apps-pricing.md#integration-accounts) is a separate resource that you create and link to logic apps for building B2B integrations. Integration accounts use a [fixed pricing model](../logic-apps/logic-apps-pricing.md#integration-accounts) where the rate is based on the integration account type or *tier* that you use.

* An [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) is a separate resource that you create as a deployment location for logic apps that need direct access to resources in a virtual network. ISEs use a [fixed pricing model](../logic-apps/logic-apps-pricing.md#fixed-pricing) where the rate is based on the ISE SKU that you create and other settings.

* A [custom connector](../logic-apps/logic-apps-pricing.md#consumption-pricing) is a separate resource that you create for a REST API that has no prebuilt connector for you to use in your logic apps. Custom connector executions use a [consumption pricing model](../logic-apps/logic-apps-pricing.md#connectors) except when you use them in an ISE.

In the multi-tenant Logic Apps service, [data retention and storage consumption](../logic-apps/logic-apps-pricing.md#data-retention) also accrue costs using a [fixed pricing model](#data-retention). However, in an ISE, data retention and storage consumption don't incur costs.

### Costs might accrue after resource deletion

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

If you have these resources after deleting a logic app, these resources continue to exist and accrue costs until you delete them.

* Azure resources that had connections to your logic app, for example, Azure storage accounts and Azure function apps

* Integration accounts

* Integration service environments (ISEs). If you delete an ISE, the associated Azure virtual network and networking-related resources continue to exist.

### Using Monetary Credit with Azure Logic Apps

<!--Note to Azure service writer: Let the user know that most 1st party Azure service charges can be fulfilled by EA monetary commitment credit. However, charges from third party products and services including those from the Azure Marketplace cannot be paid for by EA monetary commitment credit. -->

You can pay for Azure Logic Apps charges with your EA monetary commitment credit. However, you can't use EA monetary commitment credit to pay for charges for third party products and services, including those from the Azure Marketplace.

## Estimate costs

Before you create resources with Azure Logic Apps, estimate your costs by using the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/). For more information, review [Pricing model for Azure Logic Apps](../logic-apps/logic-apps-pricing.md).

1. On the [Azure pricing calculator page](https://azure.microsoft.com/pricing/calculator/), from the left menu, select **Integration** > **Azure Logic Apps**.

   ![Screenshot that shows the Azure pricing calculator with "Azure Logic Apps" selected.](./media/plan-manage-costs-logic-apps/add-azure-logic-apps-pricing-calculator.png)

1. Scroll down the page until you can view the Azure Logic Apps pricing calculator. In the various sections for Azure resources that are directly related to Azure Logic Apps, enter the numbers of resources that you plan to use and the number of intervals over which you might use those resources.

   This screenshot shows an example cost estimate by using the calculator:

   ![Example showing estimated cost in the Azure Pricing calculator](./media/plan-manage-costs-logic-apps/example-logic-apps-pricing-calculator.png)

1. To update your cost estimates as you create and use new related resources, return to this calculator, and update those resources here.

## Create budgets and alerts

To help you proactively manage costs for your Azure account or subscription, you can create [budgets](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) and [alerts](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) by using the [Azure Cost Management and Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) service and capabilities.  Budgets and alerts are created for Azure subscriptions and resource groups, so they're useful as part of an overall cost monitoring strategy.

Based on spending compared to budget and cost thresholds, alerts automatically notify stakeholders about spending anomalies and overspending risks. If you want more granularity in your monitoring, you can also create budgets that use filters for specific resources or services in Azure. Filters help make sure that you don't accidentally create new resources that cost you additional money. For more information about the filter options, see [Group and filter options](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

### Monitor costs

After you create and use resources in Azure, you can monitor incurred costs 

Azure resource usage unit costs vary by time intervals (seconds, minutes, hours, and days) or by unit usage (bytes, megabytes, and so on). 

by using the service's cost analysis capabilities and export cost data for storage.

As soon as Logic Apps use starts, costs are incurred, and you can review these costs by using the [cost analysis](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) capabilities in [Azure Cost Management and Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

When you use cost analysis, you view Azure Logic Apps costs in graphs and tables for different time intervals. Some examples are by day, current and prior month, and year. You also view costs against budgets and forecasted costs. Switching to longer views over time can help you identify spending trends. And you see where overspending might have occurred. If you've created budgets, you can also easily see where they're exceeded.

To view <ServiceName> costs in cost analysis:

1. In the Azure portal, open the scope, and select **Cost analysis** in the menu.

   For example, go to **Subscriptions**, select a subscription from the list, and then select  **Cost analysis** in the menu. Select **Scope** to switch to a different scope in cost analysis.

1. By default, cost for services are shown in the first donut chart. Select the area in the chart labeled <ServiceName>.

Actual monthly costs are shown when you initially open cost analysis. Here's an example showing all monthly usage costs.

![Example showing accumulated costs for a subscription](../../media/contribute-how-to-write-cost-management-conceptual-article/all-costs.png)

<!-- Note to Azure service writer: This example shows costs for an example Azure subscription. You can see service costs for App Service, Storage, Backup, Virtual Networks, and Advanced Threat Protection. Replace this example image with one that shows costs for your service. Your screenshot should look like the one above. -->

- To narrow costs for a single service, like <ServiceName>, select **Add filter** and then select **Service name**. Then, select **<ServiceName>**.

Here's an example showing costs for just <ServiceName>.

![Example showing accumulated costs for ServiceName](../../media/contribute-how-to-write-cost-management-conceptual-article/service-specific-cost.png)

<!-- Note to Azure service writer: The image shows an example for Azure Storage. Replace the example image with one that shows costs for your service. -->

In the preceding example, you see the current cost for the service. Costs by Azure regions (locations) and <ServiceName> costs by resource group are also shown. From here, you can explore costs on your own.

### Export cost data

You can [export your cost data](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) to a storage account. This is helpful when you or others need to do additional data analysis for costs. For example, a finance team can analyze the data using Excel or Power BI. You can export your costs on a daily, weekly, or monthly schedule, and set a custom date range. Exporting cost data is the recommended way to retrieve cost datasets.

## Other ways to manage and reduce costs

To help you contain costs on your Logic Apps and related resources, try these tips:

<!-- Note to Azure service writer: This is an optional section. Other than using the Cost Management methods above, there are probably ways to minimize costs for your service that are specific to your service. Because customers only pay for what they use and when they use less of a resource, the result is a smaller bill. You might already have published cost-saving content. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

If your team has no cost-saving recommendations or best practice advice to reduce costs, then cut this section.
-->

## Next steps

Learn more by reviewing these topics:

* [Optimize your cloud investment with Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Manage costs by using Cost Analysis](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Prevent unexpected costs](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Take the [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guided learning course