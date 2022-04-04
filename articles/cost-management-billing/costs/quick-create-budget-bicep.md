---
title: Quickstart - Create a budget with Bicep
description: Quickstart showing how to Create a budget with Bicep.
author: schaffererin
ms.author: v-eschaffer
tags: azure-resource-manager
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: quickstart
ms.date: 04/04/2022
ms.custom: subject-armqs, devx-track-azurepowershell, mode-arm
---

# Quickstart: Create a budget with Bicep

Budgets in Cost Management help you plan for and drive organizational accountability. With budgets, you can account for the Azure services you consume or subscribe to during a specific period. They help you inform others about their spending to proactively manage costs and monitor how spending progresses over time. When the budget thresholds you've created are exceeded, notifications are triggered. None of your resources are affected and your consumption isn't stopped. You can use budgets to compare and track spending as you analyze costs. This quickstart shows you how to create a budget using Bicep.

[!INCLUDE [About Bicep](../../../includes/resource-manager-quickstart-bicep-introduction.md)]

## Prerequisites

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

If you have a new subscription, you can't immediately create a budget or use other Cost Management features. It might take up to 48 hours before you can use all Cost Management features.

Budgets are supported for the following types of Azure account types and scopes:

- Azure role-based access control (Azure RBAC) scopes
    - Management groups
    - Subscription
- Enterprise Agreement scopes
    - Billing account
    - Department
    - Enrollment account
- Individual agreements
    - Billing account
- Microsoft Customer Agreement scopes
    - Billing account
    - Billing profile
    - Invoice section
    - Customer
- AWS scopes
    - External account
    - External subscription

To view budgets, you need at least read access for your Azure account.

For Azure EA subscriptions, you must have read access to view budgets. To create and manage budgets, you must have contributor permission.

The following Azure permissions, or scopes, are supported per subscription for budgets by user and group. For more information about scopes, see [Understand and work with scopes](understand-work-scopes.md).

- Owner – Can create, modify, or delete budgets for a subscription.
- Contributor and Cost Management contributor – Can create, modify, or delete their own budgets. Can modify the budget amount for budgets created by others.
- Reader and Cost Management reader – Can view budgets that they have permission to.

For more information about assigning permission to Cost Management data, see [Assign access to Cost Management data](assign-access-acm-data.md).

## Review and deploy the Bicep file

## [No filter](#tab/no-filter)

### Review the Bicep file

The Bicep file used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/create-budget-simple).

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.consumption/create-budget-simple/main.bicep" :::

One Azure resource is defined in the Bicep file:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Create an Azure budget.

### Deploy the Bicep file

1. Save the Bicep file as **main.bicep** to your local computer.
1. Deploy the Bicep file using either Azure CLI or Azure PowerShell.

    # [CLI](#tab/CLI)

    ```azurecli
    az deployment sub create --name demoSubDeployment --location centralus --template-file main.bicep --parameters startDate=<start-date> endDate=<end-date> contactEmails=[]
    ```

    # [PowerShell](#tab/PowerShell)

    ```azurepowershell
    New-AzSubscriptionDeployment -Name demoSubDeployment -Location centralus -TemplateFile ./main.bicep -startDate "<start-date>" -endDate "<end-date>" -contactEmails "[]"
    ```

    ---

    > [!NOTE]
    > Replace **\<start-date\>** with the start date. It must be the first of the month in YYYY-MM-DD format. A future start date shouldn't be more than three months in the future. A past start date should be selected within the timegrain period. Replace **\<end-date\>** with the end date in YYYY-MM-DD format. If not provided, it defaults to ten years from the start date. **[]** with the list of email addresses to send the budget notification to when the threshold is exceeded.

    When the deployment finishes, you should see a message indicating the deployment succeeded.

### [One filter](#tab/one-filter)

### Review the Bicep file

The Bicep file used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/create-budget-onefilter).

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.consumption/create-budget-onefilter/main.bicep" :::

One Azure resource is defined in the Bicep file:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Create an Azure budget.

### Deploy the Bicep file

1. Save the Bicep file as **main.bicep** to your local computer.
1. Deploy the Bicep file using either Azure CLI or Azure PowerShell.

    # [CLI](#tab/CLI)

    ```azurecli
    az deployment sub create --name demoSubDeployment --location centralus --template-file main.bicep --parameters startDate=<start-date> endDate=<end-date> contactEmails=[] resourceGroupFilterValues=[]
    ```

    # [PowerShell](#tab/PowerShell)

    ```azurepowershell
    New-AzSubscriptionDeployment -Name demoSubDeployment -Location centralus -TemplateFile ./main.bicep -startDate "<start-date>" -endDate "<end-date>" -contactEmails "[]" -resourceGroupFilterValues="[]"
    ```

    ---

    > [!NOTE]
    > Replace **\<start-date\>** with the start date. It must be the first of the month in YYYY-MM-DD format. A future start date shouldn't be more than three months in the future. A past start date should be selected within the timegrain period. Replace **\<end-date\>** with the end date in YYYY-MM-DD format. If not provided, it defaults to ten years from the start date. Replace **[]** with the list of email addresses to send the budget notification to when the threshold is exceeded. Replace **[]** with the set of values for the resource group filter.

    When the deployment finishes, you should see a message indicating the deployment succeeded.

### [Two or more filters](#tab/two-filters)

### Review the Bicep file

The Bicep file used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.consumption/create-budget/main.bicep" :::

One Azure resource is defined in the Bicep file:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Create an Azure budget.

### Deploy the Bicep file

1. Save the Bicep file as **main.bicep** to your local computer.
1. Deploy the Bicep file using either Azure CLI or Azure PowerShell.

    # [CLI](#tab/CLI)

    ```azurecli
    az deployment sub create --name demoSubDeployment --location centralus --template-file main.bicep --parameters startDate=<start-date> endDate=<end-date> contactEmails=[] contactGroups=[] resourceGroupFilterValues=[] meterCategoryFilterValues=[]
    ```

    # [PowerShell](#tab/PowerShell)

    ```azurepowershell
    New-AzSubscriptionDeployment -Name demoSubDeployment -Location centralus -TemplateFile ./main.bicep -startDate "<start-date>" -endDate "<end-date>" -contactEmails "[]" -contactGroups "[]" -resourceGroupFilterValues="[]" -meterCategoryFilterValues="[]"
    ```

    ---

    > [!NOTE]
    > Replace **\<start-date\>** with the start date. It must be the first of the month in YYYY-MM-DD format. A future start date shouldn't be more than three months in the future. A past start date should be selected within the timegrain period. Replace **\<end-date\>** with the end date in YYYY-MM-DD format. If not provided, it defaults to ten years from the start date. Replace **[]** with the list of email addresses to send the budget notification to when the threshold is exceeded. Replace **[]** with the list of action groups to send the budget notification to when the threshold is exceeded. Replace **[]** with the set of values for the resource group filter. Replace **[]** with the set of values for the meter category filter.

    When the deployment finishes, you should see a message indicating the deployment succeeded.

## Review deployed resources

Use the Azure portal, Azure CLI, or Azure PowerShell to list the deployed resources in the resource group.

# [CLI](#tab/CLI)

```azurecli-interactive
az resource list --resource-group exampleRG
```

# [PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzResource -ResourceGroupName exampleRG
```

---

## Clean up resources

When no longer needed, use the Azure portal, Azure CLI, or Azure PowerShell to delete the VM and all of the resources in the resource group.

# [CLI](#tab/CLI)

```azurecli-interactive
az group delete --name exampleRG
```

# [PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name exampleRG
```

---

## Next steps

In this quickstart, you created an Azure budget and deployed it using Bicep. To learn more about Cost Management and Billing and Bicep, continue on to the articles below.

- Read the [Cost Management and Billing](../cost-management-billing-overview.md) overview
- [Create budgets](tutorial-acm-create-budgets.md) in the Azure portal
- Learn more about [Azure Resource Manager](../../azure-resource-manager/bicep/overview.md)
