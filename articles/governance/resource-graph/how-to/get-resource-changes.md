---
title: Get resource configuration changes (Preview)
description: Get resource configuration changes at scale
ms.date: 03/11/2024
ms.topic: how-to
---

# Get resource configuration changes (Preview)

Resources change through the course of daily use, reconfiguration, and even redeployment. Most change is by design, but sometimes it isn't. You can:

- Find when changes were detected on an Azure Resource Manager property.
- View property change details.
- Query changes at scale across your subscriptions, management group, or tenant.

This article shows how to query resource configuration changes through Resource Graph.

## Prerequisites

- To enable Azure PowerShell to query Azure Resource Graph, [add the module](../first-query-powershell.md#add-the-resource-graph-module).
- To enable Azure CLI to query Azure Resource Graph, [add the extension](../first-query-azurecli.md#add-the-resource-graph-extension).
- Review the [change resource properties spec](../concepts/changes-resource-spec.md).

## Get change events using Resource Graph

### Run a query

Try out a tenant-based Resource Graph query of the `resourcechanges` table. The query returns the first five most recent Azure resource changes with the change time, change type, target resource ID, target resource type, and change details of each change record. You can query by
[management group](../../management-groups/overview.md) or subscription with the `-ManagementGroup`
or `-Subscription` parameters respectively.

1. Run the following Azure Resource Graph query:

   # [Azure CLI](#tab/azure-cli)
     ```azurecli
     # Login first with az login if not using Cloud Shell
   
     # Run Azure Resource Graph query
     az graph query -q 'resourcechanges | project properties.changeAttributes.timestamp, properties.changeType, properties.targetResourceId, properties.targetResourceType, properties.changes | limit 5'
     ```
   
   # [PowerShell](#tab/azure-powershell)
     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell
   
     # Run Azure Resource Graph query
     Search-AzGraph -Query 'resourcechanges | project properties.changeAttributes.timestamp, properties.changeType, properties.targetResourceId, properties.targetResourceType, properties.changes | limit 5'
     ```
   
   # [Portal](#tab/azure-portal)
     1. Open the [Azure portal](https://portal.azure.com).
   
     1. Select **All services** in the left pane. Search for and select **Resource Graph Explorer**.
   
     1. In the **Query 1** portion of the window, enter the following query.
        ```kusto
        resourcechanges
        | project properties.changeAttributes.timestamp, properties.changeType, properties.targetResourceId, properties.targetResourceType, properties.changes
        | limit 5
        ```
     1. Select **Run query**.
   
     1. Review the query response in the **Results** tab. Select the **Messages** tab to see details
      about the query, including the count of results and duration of the query. Any errors are
      displayed under this tab.
   
   ---

1. Update the query to specify a more user-friendly column name for the **timestamp** property:

   # [Azure CLI](#tab/azure-cli)
      ```azurecli
      # Run Azure Resource Graph query with 'extend'
      az graph query -q 'resourcechanges | extend changeTime=todatetime(properties.changeAttributes.timestamp) | project changeTime, properties.changeType, properties.targetResourceId, properties.targetResourceType, properties.changes | limit 5'
      ```
   
   # [PowerShell](#tab/azure-powershell)
      ```azurepowershell-interactive
      # Run Azure Resource Graph query with 'extend' to define a user-friendly name for properties.changeAttributes.timestamp
      Search-AzGraph -Query 'resourcechanges | extend changeTime=todatetime(properties.changeAttributes.timestamp) | project changeTime, properties.changeType, properties.targetResourceId, properties.targetResourceType, properties.changes | limit 5'
      ```
   
   # [Portal](#tab/azure-portal)
      ```kusto
      resourcechanges
      | extend changeTime=todatetime(properties.changeAttributes.timestamp)
      | project changeTime, properties.changeType, properties.targetResourceId, properties.targetResourceType, properties.changes
      | limit 5
      ```
      Then select **Run query**.
   
   ---

1. To get the most recent changes, update the query to `order by` the user-defined **changeTime** property:

   # [Azure CLI](#tab/azure-cli)
      ```azurecli
      # Run Azure Resource Graph query with 'order by'
      az graph query -q 'resourcechanges | extend changeTime=todatetime(properties.changeAttributes.timestamp) | project changeTime, properties.changeType, properties.targetResourceId, properties.targetResourceType, properties.changes | order by changeTime desc | limit 5'
      ```
   
   # [PowerShell](#tab/azure-powershell)
      ```azurepowershell-interactive
      # Run Azure Resource Graph query with 'order by'
      Search-AzGraph -Query 'resourcechanges | extend changeTime=todatetime(properties.changeAttributes.timestamp) | project changeTime, properties.changeType, properties.targetResourceId, properties.targetResourceType, properties.changes | order by changeTime desc | limit 5'
      ```
   
   # [Portal](#tab/azure-portal)
      ```kusto
      resourcechanges
      | extend changeTime=todatetime(properties.changeAttributes.timestamp)
      | project changeTime, properties.changeType, properties.targetResourceId, properties.targetResourceType, properties.changes
      | order by changeTime desc
      | limit 5
      ```
      Then select **Run query**.
   
   ---

> [!NOTE]
> If the query does not return results from a subscription you already have access to, then the `Search-AzGraph` PowerShell cmdlet defaults to subscriptions in the default context.

Resource Graph Explorer also provides a clean interface for converting the results of some queries into a chart that can be pinned to an Azure dashboard.

### Query resource changes  

With Resource Graph, you can query the `resourcechanges` table to filter or sort by any of the change resource properties:

#### All changes in the past one day
```kusto
resourcechanges
| extend changeTime = todatetime(properties.changeAttributes.timestamp), targetResourceId = tostring(properties.targetResourceId),
changeType = tostring(properties.changeType), correlationId = properties.changeAttributes.correlationId, 
changedProperties = properties.changes, changeCount = properties.changeAttributes.changesCount
| where changeTime > ago(1d)
| order by changeTime desc
| project changeTime, targetResourceId, changeType, correlationId, changeCount, changedProperties
```

#### Resources deleted in a specific resource group
```kusto
resourcechanges
| where resourceGroup == "myResourceGroup"
| extend changeTime = todatetime(properties.changeAttributes.timestamp), targetResourceId = tostring(properties.targetResourceId),
changeType = tostring(properties.changeType), correlationId = properties.changeAttributes.correlationId
| where changeType == "Delete"
| order by changeTime desc
| project changeTime, resourceGroup, targetResourceId, changeType, correlationId
```

#### Changes to a specific property value
```kusto
resourcechanges
| extend provisioningStateChange = properties.changes["properties.provisioningState"], changeTime = todatetime(properties.changeAttributes.timestamp), targetResourceId = tostring(properties.targetResourceId), changeType = tostring(properties.changeType)
| where isnotempty(provisioningStateChange)and provisioningStateChange.newValue == "Succeeded"
| order by changeTime desc
| project changeTime, targetResourceId, changeType, provisioningStateChange.previousValue, provisioningStateChange.newValue
```

#### Latest resource configuration for resources created in the last seven days
```kusto
resourcechanges
| extend targetResourceId = tostring(properties.targetResourceId), changeType = tostring(properties.changeType), changeTime = todatetime(properties.changeAttributes.timestamp)
| where changeTime > ago(7d) and changeType == "Create"
| project  targetResourceId, changeType, changeTime
| join ( Resources | extend targetResourceId=id) on targetResourceId
| order by changeTime desc
| project changeTime, changeType, id, resourceGroup, type, properties
```

#### Changes in virtual machine size 
```kusto
resourcechanges
|extend vmSize = properties.changes["properties.hardwareProfile.vmSize"], changeTime = todatetime(properties.changeAttributes.timestamp), targetResourceId = tostring(properties.targetResourceId), changeType = tostring(properties.changeType) 
| where isnotempty(vmSize) 
| order by changeTime desc 
| project changeTime, targetResourceId, changeType, properties.changes, previousSize = vmSize.previousValue, newSize = vmSize.newValue
```

#### Count of changes by change type and subscription name
```kusto
resourcechanges  
|extend changeType = tostring(properties.changeType), changeTime = todatetime(properties.changeAttributes.timestamp), targetResourceType=tostring(properties.targetResourceType)  
| summarize count() by changeType, subscriptionId 
| join (resourcecontainers | where type=='microsoft.resources/subscriptions' | project SubscriptionName=name, subscriptionId) on subscriptionId 
| project-away subscriptionId, subscriptionId1
| order by count_ desc  
```

#### Latest resource configuration for resources created with a certain tag
```kusto
resourcechanges 
|extend targetResourceId = tostring(properties.targetResourceId), changeType = tostring(properties.changeType), createTime = todatetime(properties.changeAttributes.timestamp) 
| where createTime > ago(7d) and changeType == "Create" or changeType == "Update" or changeType == "Delete"
| project  targetResourceId, changeType, createTime 
| join ( resources | extend targetResourceId=id) on targetResourceId
| where tags ['Environment'] =~ 'prod' 
| order by createTime desc 
| project createTime, id, resourceGroup, type
```

### Query best practices

Review the following best practices before querying and analyzing changes in your resources.

- Query for change events during a specific window of time and evaluate the change details. This query works best during incident management to understand _potentially_ related changes.
- Keep an up-to-date Configuration Management Database (CMDB). Instead of refreshing all resources and their full property sets on a scheduled frequency, only receive their changes.
- Understand what other properties may have been changed when a resource changes "compliance state". Evaluation of these extra properties can provide insights into other properties that may need to be managed via an Azure Policy definition.
- The order of query commands is important. In this example, the `order by` must come before the `limit` command. This command orders the query results by the change time and then limits them to ensure that you get the five most recent results.
- Resource configuration changes support changes to resource types from the Resource Graph tables [resources](../reference/supported-tables-resources.md#resources), [resourcecontainers](../reference/supported-tables-resources.md#resourcecontainers), and [healthresources](../reference/supported-tables-resources.md#healthresources). Changes are queryable for 14 days. For longer retention, you can [integrate your Resource Graph query with Azure Logic Apps](../tutorials/logic-app-calling-arg.md) and export query results to any of the Azure data stores like [Log Analytics](../../../azure-monitor/logs/log-analytics-overview.md) for your desired retention.

## Next steps

> [!div class="nextstepaction"]
> [Access and view resource changes](../how-to/view-resource-changes.md)

## Related links

- [Starter Resource Graph query samples](../samples/starter.md)
- [Guidance for throttled requests](../concepts/guidance-for-throttled-requests.md)
- [Azure Automation's change tracking](../../../automation/change-tracking/overview.md)
- [Azure Policy's machine configuration for VMs](../../machine-configuration/overview.md)
- [Azure Resource Graph queries by category](../samples/samples-by-category.md)
