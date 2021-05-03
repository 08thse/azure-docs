---
title: Monitoring Azure App Configuration
description: Start here to learn how to monitor App Configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: subject-monitoring
ms.date: 4/1/2021
---
# Monitoring App Configuration
When you have critical applications and business processes relying on Azure resources, you want to monitor those resources for their availability, performance, and operation. 
This article describes the monitoring data generated by App Configuration. App Configuration uses  [Azure Monitor](/azure/azure-monitor/overview). If you are unfamiliar with the features of Azure Monitor common to all Azure services that use it, read [Monitoring Azure resources with Azure Monitor](/azure/azure-monitor/essentials/monitor-azure-resource).

## Monitoring overview page in Azure portal
The **Overview** page in the Azure portal for each configuration store includes a brief view of the resource usage, such as the total number of requests, number of  throttled requests, and request duration. This information is useful, but only a small amount of the monitoring data is available. Some of this data is collected automatically and is available for analysis as soon as you create the resource. You can enable additional types of data collection with some configuration.

## Monitoring data 
App Configuration collects the same kinds of monitoring data as other Azure resources that are described in [Monitoring data from Azure resources](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources). See [Monitoring App Configuration data reference](monitor-app-configuration-reference.md) for detailed information on the metrics and logs metrics created by App Configuration.

## Collection and routing
Platform metrics and the Activity log are collected and stored automatically, but can be routed to other locations by using a diagnostic setting.
Resource Logs are not collected and stored until you create a diagnostic setting and route them to one or more locations. For example, to view logs and metrics for a configuration store in near real-time in Azure Monitor, collect the resource logs in a Log Analytics workspace. Follow these steps to create and enable a diagnostic setting. 

 #### [Portal](#tab/portal)

1. Sign in to the Azure portal.

1. Navigate to your App Configuration store.

1. In the **Monitoring** section, click ** Diagnostic settings **, then click ** +Add diagnostic setting **. 
    ![Add a diagnostic setting](./media/diagnostic-settings-add.png)

1. In the **Diagnostic setting** page, enter a name for your setting, then select **HttpRequest** and choose the destination to send your logs to. To send them to a Log Analytics workspace, choose **Send to Log Analytics workspace**. 
    ![Details of the diagnostic settings](./media/monitoring-diagnostic-settings-details.png)
1. Enter the name of your Subscription and Log Analytics Workspace. 
1. Click **Save** and verify that the Diagnostic Settings page now lists your new diagnostic setting. 
    

 ### [Azure CLI](#tab/cli)
    
1. Open the Azure Cloud Shell, or if you've installed the Azure CLI locally, open a command console application such as Windows PowerShell.

1. If your identity is associated with more than one subscription, then set your active subscription to subscription of the storage account that you want to enable logs for.

    ```Azure CLI
    az account set --subscription <your-subscription-id>
    ```

1. Enable logs by using the az monitor [diagnostic-settings create command](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_create).

    ```Azure CLI
    az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <app-configuration-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
    ```

 ### [Powershell](#tab/powershell)
    
1. Open a Windows PowerShell command window, and sign in to your Azure subscription by using the Connect-AzAccount command. Then, follow the on-screen directions.

    ```powershell
    Connect-AzAccount
    ```

1. Set your active subscription to subscription of the App Configuration account that you want to enable logging for.

    ```powershell
    Set-AzContext -SubscriptionId <subscription-id>
    ```
    
1. To enable logs for a Log Analytics Workspace, use the [Set-AzDiagnosticSetting PowerShell](https://docs.microsoft.com/previous-versions/azure/mt631625(v=azure.100)?redirectedfrom=MSDN) cmdlet. 

    ```powershell
    Set-AzDiagnosticSetting -ResourceId <app-configuration-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true
    ```
1. Verify that the that your diagnostic setting is correctly set and log categories are enabled. 

    ```powershell
    Get-AzureRmDiagnosticSetting -ResourceId <app-configuration-resource-id> 
    ```

See [Create diagnostic setting to collect platform logs and metrics in Azure](/azure/azure-monitor/platform/diagnostic-settings) for further information on creating a diagnostic setting using the Azure portal, CLI, or PowerShell. When you create a diagnostic setting, you specify which categories of logs to collect. For further information on the categories of logs for App Configuration, please reference [App Configuration monitoring data reference](monitor-service-reference.md#resource-logs).

## Analyzing metrics

You can analyze metrics for App Configuration with metrics from other Azure services using metrics explorer by opening **Metrics** from the **Azure Monitor** menu. See [Getting started with Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) for details on using this tool. 

This screenshot shows how to view the **Http Incoming Request Count** at the configuration store level.

![How to use App Config Metrics](./media/monitoring-analyze-metrics.png)

For a list of the platform metrics collected for App Configuration, see [Monitoring App Configuration data reference metrics](monitor-service-reference#metrics). For reference, you can see a list of [all resource metrics supported in Azure Monitor](/azure/azure-monitor/platform/metrics-supported).

## Analyzing logs
Data in Azure Monitor Logs is stored in tables where each table has its own set of unique properties.  
All resource logs in Azure Monitor have the same fields followed by service-specific fields. The common schema is outlined in [Azure Monitor resource log schema](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema). The schema for App Configuration resource logs is found in the [App Configuration Data Reference](monitor-service-reference#schemas).
The [Activity log](/azure/azure-monitor/platform/activity-log is a platform login Azure that provides insight into subscription-level events. You can view it independently or route it to Azure Monitor Logs, where you can do much more complex queries using Log Analytics.  
For a list of the types of resource logs collected for App Configuration, see [Monitoring App Configuration data reference](monitor-service-reference#logs)  
For a list of the tables used by Azure Monitor Logs and queryable by Log Analytics, see [Monitoring App Configuration data reference](monitor-service-reference#azuremonitorlogstables)  

> [!IMPORTANT]
> When you select **Logs** from the App Configuration menu, Log Analytics is opened with the query scope set to the current configuration store. This means that log queries will only include data from that resource. If you want to run a query that includes data from other configuration or data from other Azure services, select **Logs** from the **Azure Monitor** menu. See [Log query scope and time range in Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) for details.

Following are queries that you can use to help you monitor your App Configuration resource. 

* List all Http Requests in the last 3 days 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
    ```

* List all throttled requests (returned Http status code 429 for too many requests) in the last 3 days 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | where StatusCode == "429"
    ```

* List the number of requests sent in the last 3 days by IP Address 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount= count() by ClientIPAddress
        | order by requestCount desc 
    ```

* Create a pie chart of the types of status codes received in the last 3 days
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount=count() by StatusCode
        | order by requestCount desc 
        | render piechart 
    ```

* List the number of requests sent by day for the last 14 days
    ```Kusto
    AACHttpRequest
        | where TimeGenerated > ago(124d)
        | extend Day = startofday(TimeGenerated)
        | summarize requestcount=count() by Day
        | order by Day desc  
    ```

## Alerts

Azure Monitor alerts proactively notify you when important conditions are found in your monitoring data. They allow you to identify and address issues in your system before your customers notice them. You can set alerts on [metrics](/azure/azure-monitor/platform/alerts-metric-overview), [logs](/azure/azure-monitor/platform/alerts-unified-log), and the [activity log](/azure/azure-monitor/platform/activity-log-alerts). Different types of alerts have benefits and drawbacks
The following table lists common and recommended alert rules for App Configuration.

| Alert type | Condition | Description  |
|:---|:---|:---|
|Rate Limit on Http Requests | Status Code often returns 429 response | The configuration store has exceeded the [hourly request quota](/faq#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration). Upgrade to a standard store or follow the [best practices](/howto-best-practices#reduce-requests-made-to-app-configuration) to optimize your usage. |
| Unexpected Http Responses | Verify StatusCode for Requests| For further information on StatusCode, please reference [the HTTP Status Code Guide](./rest/api/searchservice/http-status-codes)|

## Next steps

- See [Monitoring App Configuration data reference](monitor-service-reference.md) for a reference of the metrics, logs, and other important values created by App Configuration.
*>.
- See [Monitoring Azure resources with Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource) for details on monitoring Azure resources.



