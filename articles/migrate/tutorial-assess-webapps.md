---
title: Tutorial to assess web apps for migration to Azure App Service
description: Learn how to create assessment for Azure App Service in Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 07/14/2023
ms.service: azure-migrate
ms.custom: engagement-fy23
---


# Tutorial: Assess ASP.NET web apps for migration to Azure App Service

As part of your migration journey to Azure, you assess your on-premises workloads to measure cloud readiness, identify risks, and estimate costs and complexity.
This article shows you how to assess discovered ASP.NET web apps running on IIS web servers in preparation for migration to Azure App Service Code and Azure App Service Containers, using the Azure Migrate: Discovery and assessment tool.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Run an assessment based on web apps configuration data.
> * Review an Azure App Service assessment

> [!NOTE]
> Tutorials show the quickest path for trying out a scenario, and use default options where possible. 

## Prerequisites

- If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/pricing/free-trial/) before you begin.
- Before you follow this tutorial to assess your web apps for migration to Azure App Service, make sure you've discovered the web apps you want to assess using the Azure Migrate appliance, [follow this tutorial](tutorial-discover-vmware.md)
- If you want to try out this feature in an existing project, ensure that you have completed the [prerequisites](how-to-discover-sql-existing-project.md) in this article.

## Run an assessment

Run an assessment as follows:

1. On the **Get started** page > **Servers, databases and web apps**, select **Discover, assess and migrate**.
2. On **Azure Migrate: Discovery and assessment**, select **Assess** and choose the assessment type as **Web apps on Azure**.
3. In **Create assessment**, you will be able to see the assessment type pre-selected as **Web apps on Azure** and the discovery source defaulted to **Servers discovered from Azure Migrate appliance**. Select the **Scenario** as **Web apps to App Service**. 

4. Select **Edit** to review the assessment properties.

5. Here's what's included in Azure App Service assessment properties:

    **Property** | **Details**
    --- | ---
    **Target location** | The Azure region to which you want to migrate. Azure App Service configuration and cost recommendations are based on the location that you specify.
    **Environment type** | Type of environment in which it is running.
    **Offer** | The [Azure offer](https://azure.microsoft.com/support/legal/offer-details/) in which you're enrolled. The assessment estimates the cost for that offer.
    **Currency** | The billing currency for your account.
    **Discount (%)** | Any subscription-specific discounts you receive on top of the Azure offer. The default setting is 0%.
    **EA subscription** | Specifies that an Enterprise Agreement (EA) subscription is used for cost estimation. Takes into account the discount applicable to the subscription. <br/><br/> Retain the default settings for reserved instances and discount (%) properties.
    **Isolation required** | Select yes if you want your web apps to run in a private and dedicated environment in an Azure datacenter.

    - In **Savings options (compute)**, specify the savings option that you want the assessment to consider, helping to optimize your Azure compute cost. 
        - [Azure reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) (1 year or 3 year reserved) are a good option for the most consistently running resources.
        - [Azure Savings Plan](../cost-management-billing/savings-plan/savings-plan-compute-overview.md) (1 year or 3 year savings plan) provide additional flexibility and automated cost optimization. Ideally post migration, you could use Azure reservation and savings plan at the same time (reservation will be consumed first), but in the Azure Migrate assessments, you can only see cost estimates of 1 savings option at a time. 
        - When you select *None*, the Azure compute cost is based on the Pay as you go rate or based on actual ]usage.
        - You need to select pay-as-you-go in offer/licensing program to be able to use Reserved Instances or Azure Savings Plan. When you select any savings option other than 'None', the 'Discount (%)' setting is not applicable.

1. Select **Save** if you made any changes.
1. In **Create assessment**, select **Next**.
1. In **Select servers to assess** > **Assessment name**, specify a name for the assessment.
1. In **Select or create a group**, select **Create New** and specify a group name. You can also use an existing group.
1. Select the appliance and select the servers that you want to add to the group. Select **Next**.
1. In **Review + create assessment**, review the assessment details, and select **Create Assessment** to create the group and run the assessment.
1. After the assessment is created, go to **Servers, databases and web apps** > **Azure Migrate: Discovery and assessment**. Refresh the tile data by selecting the **Refresh** option on top of the tile. Wait for the data to refresh.
1. Select the number next to **Web apps on Azure** in the **Assessment** section. 
1. Select the assessment name, which you wish to view.

## Review an assessment

**To view an assessment**:

1. In **Servers, databases and web apps** > **Azure Migrate: Discovery and assessment**, select the number next to the Web apps on Azure assessment. 
2. Select the assessment name, which you wish to view. 

   The **Overview** screen contains 3 sections: Essentials, Assessed entities, and Migration scenario. 

     **Essentials**

      **Field** | **Description**
      ----- | -----
      **Group** | 
      **Status** | 
      **Discovery source** |
      **Target location** | 
      **Currency** | 

   **Assessed entities**

   This section displays the number of servers selected for the assessments, number of Azure app service in the selected servers, and the number of distinct Sprint Boot app instances that were assessed. 

   **Migration scenario**

   This section provides a pictorial representation of the number of apps that are ready, ready with conditions, and not ready. You can see two graphical representations, one for All Web applications to App Service Code and the other for All Web applications to App Service Containers. In addition, it also lists the number of apps ready to migrate and the estimated cost for the migration for the apps that are ready to migrate.  

3. Review the assessment summary. You can also edit the assessment properties or recalculate the assessment.

### Review readiness

Review the Readiness for the web apps by following these steps:

1. In Assessments, select the name of the assessment that you want to view. 
1. Select **View more details** to view more details about each app and instances. Review the Azure App service Code and Azure App service Container readiness column in the table for the assessed web apps:  
    1. If there are no compatibility issues found, the readiness is marked as **Ready** for the target deployment type.
    1. If there are non-critical compatibility issues, such as degraded or unsupported features that do not block the migration to a specific target deployment type, the readiness is marked as **Ready with conditions** (hyperlinked) with **warning** details and recommended remediation guidance.
    1. If there are any compatibility issues that may block the migration to a specific target deployment type, the readiness is marked as **Not ready** with **issue** details and recommended remediation guidance.
    1. If the discovery is still in progress or there are any discovery issues for a web app, the readiness is marked as **Unknown** as the assessment could not compute the readiness for that web app.
    1. If the assessment is not up-to-date, the status shows as **Outdated**. Select the corresponding assessment and select **Recalculate assessment**. The assessment is recalculated and the Readiness overview screen is updated with the results of the recalculated assessments.
1. Select the Readiness status to open the **Migration issues and warnings** pane with details of the cause of the issue and recommended action.  
1. Review the recommended SKU for the web apps, which is determined as per the matrix below:

    **Readiness** | **Determine size estimate** | **Determine cost estimates**
    --- | --- | ---
    Ready  | Yes | Yes
    Ready with conditions  | Yes  | Yes
    Not ready  | No | No
    Unknown  | No | No

### Review cost estimates

The assessment summary shows the estimated monthly costs for hosting your web apps.  
Select the **Cost details** tab to view a monthly cost estimate depending on the SKUs. 

## Next steps

- Learn how to [perform at-scale agentless migration of ASP.NET web apps to Azure App Service](./tutorial-modernize-asp-net-appservice-code.md).
- [Learn more](concepts-azure-webapps-assessment-calculation.md) about how Azure App Service assessments are calculated.