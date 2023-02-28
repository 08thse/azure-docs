---
title: Monitor Azure Cache for Redis data using diagnostic settings
titleSuffix: Azure Cache for Redis
description: Learn how to use diagnostic settings to monitor connected ip addresses to your Azure Cache for Redis.
author: flang-msft
ms.author: franlanglois
ms.service: cache
ms.topic: how-to 
ms.date: 11/3/2021
ms.custom: template-how-to, devx-track-azurecli 
ms.devlang: azurecli
---

# Monitor Azure Cache for Redis data using diagnostic settings

Diagnostic settings in Azure are used to collect resource logs. Azure resource Logs are emitted by a resource and provide rich, frequent data about the operation of that resource. These logs are captured per request and they're also referred to as "data plane logs". The content of these logs varies by resource type. Two options are available to log:

- **Cache Metrics** (i.e. "AllMetrics") which can be used to [log metrics from Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)  
- **Connection Logs** which logs connections to the cache for security and diagnostic purposes. 

## Scope of availability

|Tier     | Basic, Standard, and Premium  | Enterprise and Enterprise Flash  |
|---------|---------|---------|
|Cache Metrics  | Yes         | Yes  |
|Connection Logs | Yes | Yes (preview) |

## Cache Metrics

Azure Cache for Redis emmits [many metrics](cache-how-to-monitor.md#list-of-metrics) such as _Server Load_ and _Connections per Second_ that are useful to log. Selecting the **AllMetrics** option allows these and other cache metrics to be logged. You can configure how long the metrics are retained. See [here for an example of exporting cache metrics to a storage account](cache-how-to-monitor.md#use-a-storage-account-to-export-cache-metrics). 

## Connection Logs

Azure Cache for Redis uses Azure diagnostic settings to log information on client connections to your cache. Logging and analyzing this diagnostic setting helps you understand who is connecting to your caches and the timestamp of those connections. The log data could be used to identify the scope of a security breach and for security auditing purposes.

## Differences Between Azure Cache for Redis Tiers

Implementation of connection logs is slightly different between tiers:
- **Basic, Standard, and Premium-tier caches** polls client connections by IP address, including the number of connections originating from each unique IP address. These logs aren't cumulative. They represent point-in-time snapshots taken at 10-second intervals. Authentication events (successful and failed) and disconnection events are not logged in these tiers.  
- **Enterprise and Enterprise Flash-tier caches** use the [audit connection events](https://docs.redis.com/latest/rs/security/audit-events/) functionality built-into Redis Enterprise. This allows every connection, disconnection, and authentication event to be logged, including failed authentication events. 

The connection logs produced look similar between the tiers, but have some differences. The two formats are shown in more detail below.  

> [!IMPORTANT]
> The connection logging in the Basic, Standard, and Premium tiers _polls_ the current client connections in the cache. The same client IP addresses will appear over and over again. Logging in the Enterprise and Enterprise Flash tiers is focused on each connection _event_. Logs will only occur when the actual event occured for the first time.
>

## Prequisites/Limitations of Connection Logging

### Basic, Standard, and Premium tiers
- Because connection logs in these tiers consist of point-in-time snapshots taken every 10 seconds, connections that are established and removed inbetween 10-second intervals will not be logged.
- Authentication events are not logged
- All diagnostic settings may take up to [90 minutes](../azure-monitor/essentials/diagnostic-settings.md#time-before-telemetry-gets-to-destination) to start flowing to your selected destination. 
- Enabling connection logs may cause a small performance degredation to the cache instance.

### Enterprise and Enterprise Flash tiers
- When using **OSS Cluster Policy**, logs will be emitted from each data node. When using **Enterprise Cluster Policy**, only the node being used as a proxy will emit logs. Both versions will still cover all connections to the cache. This is just an architectural difference.  
- Data loss (i.e. missing a connection event) is rare, but possible. It is typically caused by networking issues. 
- Disconnection logs do not appear in a few cases:
   - For clients using the redis-cli and redis-benchmark tools to connect to a cache instance.
   - Some disconnection logs may be missing when using the StackExchange.Redis library when using SSL
   - Disconnection logs may get missed udring a failover event.
- Because connection logs on the Enterprise tiers are event-based, be careful of your retention policies. For instance, if retention is set up to be 10 days, and a connection event occured 15 days ago, that connection will still exist but the log for that connection will not have been retained.
- If using [active geo-replication](cache-how-to-active-geo-replication.md), logging must be configured for each cache instance in the geo-replication group individually.
- All diagnostic settings may take up to [90 minutes](../azure-monitor/essentials/diagnostic-settings.md#time-before-telemetry-gets-to-destination) to start flowing to your selected destination. 
- Enabling connection logs may cause a small performance degredation to the cache instance.


> [!NOTE]
> It is always possible to use the [INFO](https://redis.io/commands/info/) or [CLIENT LIST](https://redis.io/commands/client-list/) commands to check who is connected to a cache instance on-demand.
>

## Log Destinations

You can turn on diagnostic settings for Azure Cache for Redis instances and send resource logs to the following destinations:

- **Log Analytics workspace** - doesn't need to be in the same region as the resource being monitored.
- **Storage account** - must be in the same region as the cache.
- **Event hub** - diagnostic settings can't access event hub resources when virtual networks are enabled. Enable the **Allow trusted Microsoft services to bypass this firewall?** setting in event hubs to grant access to your event hub resources. The event hub must be in the same region as the cache.
- **Partner Solution** - a list of potential partner logging solutions can be found [here](../partner-solutions/partners.md)

For more information on diagnostic requirements, see [diagnostic settings](../azure-monitor/essentials/diagnostic-settings.md?tabs=CMD).

You'll be charged normal data rates for storage account and event hub usage when you send diagnostic logs to either destination. You're billed under Azure Monitor not Azure Cache for Redis. When sending logs to **Log Analytics**, you're only charged for Log Analytics data ingestion.

For more pricing information, [Azure Monitor pricing](https://azure.microsoft.com/pricing/details/monitor/).

## Enable connection logging using the Azure Portal

### [Portal with Basic, Standard, and Premium tiers](#tab/basic-standard-premium)

1. Sign into the [Azure portal](https://portal.azure.com).

1. Navigate to your Azure Cache for Redis account. Open the **Diagnostic settings** pane under the **Monitoring section** on the left. Then, select **Add diagnostic setting**.

   :::image type="content" source="media/cache-monitor-diagnostic-settings/cache-monitor-diagnostic-setting.png" alt-text="Select diagnostics":::

1. In the **Diagnostic settings** pane, select **ConnectedClientList** from **Categories**.

   For more detail on the data logged, see below [Contents of the Connection Logs](#contents-of-the-connection-logs).

1. Once you select **ConnectedClientList**, send your logs to your preferred destination. Select the information on the right.

    :::image type="content" source="media/cache-monitor-diagnostic-settings/diagnostics-resource-specific.png" alt-text="Select enable resource-specific":::

### [Portal with Enterprise and Enterprise Flash tiers (preview)](#tab/enterprise-enterprise-flash)

1. Sign into the [Azure portal](https://portal.azure.com).

1. Navigate to your Azure Cache for Redis account. Open the **Diagnostic Settings - Auditing** pane under the **Monitoring section** on the left. Then, select **Add diagnostic setting**.

  **FRAN--need a new picture here**

1. In the **Diagnostic Setting - Auditing** pane, select **Connection events** from **Categories**.

   For more detail on the data logged, see below [Contents of the Connection Logs](#contents-of-the-connection-logs).

1. Once you select **Connection events**, send your logs to your preferred destination. Select the information on the right.

    **FRAN--need a new picture here**
    
---

## Enable connection logging using the REST API

### [REST API with Basic, Standard, and Premium tiers](#tab/basic-standard-premium)

Use the Azure Monitor REST API for creating a diagnostic setting via the interactive console. For more information, see [Create or update](/rest/api/monitor/diagnostic-settings/create-or-update).

#### Request

```http
PUT https://management.azure.com/{resourceUri}/providers/Microsoft.Insights/diagnosticSettings/{name}?api-version=2017-05-01-preview
```

#### Headers

   | Parameters/Headers | Value/Description |
   |---------|---------|
   | `name` | The name of your diagnostic setting. |
   | `resourceUri` | subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Cache/Redis/{CACHE_NAME} |
   | `api-version` | 2017-05-01-preview |
   | `Content-Type` | application/json |

#### Body

```json
{
    "properties": {
      "storageAccountId": "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/apptest/providers/Microsoft.Storage/storageAccounts/appteststorage1",
      "eventHubAuthorizationRuleId": "/subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/eventhubs/myeventhub/authorizationrules/myrule",
      "eventHubName": "myeventhub",
      "workspaceId": "/subscriptions/4b9e8510-67ab-4e9a-95a9-e2f1e570ea9c/resourceGroups/insights-integration/providers/Microsoft.OperationalInsights/workspaces/myworkspace",
      "logs": [
        {
          "category": "ConnectedClientList",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
}
```

### [REST API with Enterprise and Enterprise Flash tiers (preview)](#tab/enterprise-enterprise-flash)

Use the Azure Monitor REST API for creating a diagnostic setting via the interactive console. For more information, see [Create or update](/rest/api/monitor/diagnostic-settings/create-or-update).

#### Request

```http
PUT https://management.azure.com/{resourceUri}/providers/Microsoft.Insights/diagnosticSettings/{name}?api-version=2017-05-01-preview
```

#### Headers

   | Parameters/Headers | Value/Description |
   |---------|---------|
   | `name` | The name of your diagnostic setting. |
   | `resourceUri` | subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Cache/RedisEnterprise/{CACHE_NAME}/databases/default |
   | `api-version` | 2017-05-01-preview |
   | `Content-Type` | application/json |

#### Body

```json
{ 
    "properties": {
      "storageAccountId": "/subscriptions/4a1c78c6-5cb1-422c-a34e-0df7fcb9bd0b/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/auditinglogstorage1",
      "logs": [
        {
          "category": "ConnectionEvents",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
}

```

---

## Enable Connection Logging using Azure CLI

### [Azure CLI with Basic, Standard, and Premium tiers](#tab/basic-standard-premium)

Use the `az monitor diagnostic-settings create` command to create a diagnostic setting with the Azure CLI. For more for information on command and parameter descriptions, see [Create diagnostic settings to send platform logs and metrics to different destinations](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). This example shows how to use the Azure CLI to stream data to four different endpoints:

```azurecli
az monitor diagnostic-settings create 
    --resource /subscriptions/{subscriptionID}/resourceGroups/{resourceGroupname}/providers/Microsoft.Cache/Redis/{cacheName}
    --name {logName}
    --logs '[{"category": "ConnectedClientList","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'    
    --event-hub {eventHubName}
    --event-hub-rule /subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.eventhub/namespaces/{eventHubNamespace}/authorizationrule/{ruleName}
    --storage-account /subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
    --workspace /subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{logAnalyticsWorkspaceName}
    --marketplace-partner-id/subscriptions/{subscriptionID}/resourceGroups{resourceGroupname}/proviers/Microsoft.Datadog/monitors/mydatadog
```

### [Azure CLI with Enterprise and Enterprise Flash tiers (preview)](#tab/enterprise-enterprise-flash)

Use the `az monitor diagnostic-settings create` command to create a diagnostic setting with the Azure CLI. For more for information on command and parameter descriptions, see [Create diagnostic settings to send platform logs and metrics to different destinations](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). This example shows how to use the Azure CLI to stream data to four different endpoints:

```azurecli
az monitor diagnostic-settings create 
    --resource /subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Cache/redisenterprise/{cacheName}/databases/default
    --name {logName}
    --logs '[{"category": "ConnectionEvents","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'
    --event-hub {eventHubName}
    --event-hub-rule /subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.eventhub/namespaces/{eventHubNamespace}/authorizationrule/{ruleName}
    --storage-account /subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
    --workspace /subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{logAnalyticsWorkspaceName}
    --marketplace-partner-id/subscriptions/{subscriptionID}/resourceGroups{resourceGroupname}/proviers/Microsoft.Datadog/monitors/mydatadog
```

---

## Contents of the Connection Logs

### Basic, Standard, and Premium tiers
These fields and properties appear in the `ConnectedClientList` log category. In **Azure Monitor**, logs are collected in the `ACRConnectedClientList` table under the resource provider name of `MICROSOFT.CACHE`.

| Azure Storage field or property | Azure Monitor Logs property | Description |
| --- | --- | --- |
| `time` | `TimeGenerated` | The timestamp of when the log was generated in UTC. |
| `location` | `Location` | The location (region) the Azure Cache for Redis instance was accessed in. |
| `category` | n/a | Available log categories: `ConnectedClientList`. |
| `resourceId` | `_ResourceId` | The Azure Cache for Redis resource for which logs are enabled.|
| `operationName` | `OperationName` | The Redis operation associated with the log record. |
| `properties` | n/a | The contents of this field are described in the rows that follow. |
| `tenant` | `CacheName` | The name of the Azure Cache for Redis instance. |
| `roleInstance` | `RoleInstance` | The role instance that logged the client list. |
| `connectedClients.ip` | `ClientIp` | The Redis client IP address. |
| `connectedClients.privateLinkIpv6` | `PrivateLinkIpv6` | The Redis client private link IPv6 address (if applicable). |
| `connectedClients.count` | `ClientCount` | The number of Redis client connections from the associated IP address. |

#### Sample storage account log

If you send your logs to a storage account, the contents of the logs look like this.

```json
{
    "time": "2021-08-05T21:04:58.0466086Z",
    "location": "canadacentral",
    "category": "ConnectedClientList",
    "properties": {
        "tenant": "mycache", 
        "connectedClients": [
            {
                "ip": "192.123.43.36", 
                "count": 86
            },
            {
                "ip": "10.1.1.4",
                "privateLinkIpv6": "fd40:8913:31:6810:6c31:200:a01:104", 
                "count": 1
            }
        ],
        "roleInstance": "1"
    },
    "resourceId": "/SUBSCRIPTIONS/E6761CE7-A7BC-442E-BBAE-950A121933B5/RESOURCEGROUPS/AZURE-CACHE/PROVIDERS/MICROSOFT.CACHE/REDIS/MYCACHE", 
    "Level": 4,
    "operationName": "Microsoft.Cache/ClientList"
}
```

### Enterprise and Enterprise Flash tiers (preview)
These fields and properties appear in the `Connection events` log category. In **Azure Monitor**, logs are collected in the `REDConnectionEvents` table under the resource provider name of `MICROSOFT.CACHE`.

| Azure Storage field or property | Azure Monitor Logs property | Description |
| --- | --- | --- |
| `time` | `TimeGenerated` | The timestamp of when the log was generated in UTC. |
| `location` | `Location` | The location (region) the Azure Cache for Redis instance was accessed in. |
| `category` | n/a | Available log categories: `ConnectionEvents`. |
| `resourceId` | `_ResourceId` | The Azure Cache for Redis resource for which logs are enabled.|
| `operationName` | `OperationName` | The Redis operation associated with the log record. |
| `properties` | n/a | The contents of this field are described in the rows that follow. |
| `eventEpochTime` | `EventEpochTime` | The timestamp in UNIX epoch format |
| `clientIP` | `ClientIP` | The Redis client IP address. (if using Azure storage, this will either be in IPv4 or IPv6 format based on cache type) |
| `privateLinkIpv6` | `PrivateLinkIPv6` | The Redis client private link IPv6 address (only emmited if applicable and using log analytics). |
| `id` | `ConnectionId` | Unique connection ID assigned by Redis. |
| `eventType` |  `EventType` | Type of connection event (new_conn, auth, or close_conn). |
| `eventStatus` |  `EventStatus` | Results of an authentication request as a status code (only applicable for authentication event). |

#### Sample storage account log

If you send your logs to a storage account, a log for a connection event will look like this:

```json
    {
        "time": "2023-01-24T10:00:02.3680050Z",
        "resourceId": "/SUBSCRIPTIONS/4A1C78C6-5CB1-422C-A34E-0DF7FCB9BD0B/RESOURCEGROUPS/TEST/PROVIDERS/MICROSOFT.CACHE/REDISENTERPRISE/AUDITING-SHOEBOX/DATABASES/DEFAULT",
        "category": "ConnectionEvents",
        "location": "westus",
        "operationName": "Microsoft.Cache/redisEnterprise/databases/ConnectionEvents/Read",
        "properties": {
            "eventEpochTime": 1674554402,
            "id": 6185063009002,
            "clientIP": "20.228.16.39",
            "eventType": "new_conn"
        }
    }
```

And the log for an auth event will look like this:

```json
 {
        "time": "2023-01-24T10:00:02.3680050Z",
        "resourceId": "/SUBSCRIPTIONS/4A1C78C6-5CB1-422C-A34E-0DF7FCB9BD0B/RESOURCEGROUPS/TEST/PROVIDERS/MICROSOFT.CACHE/REDISENTERPRISE/AUDITING-SHOEBOX/DATABASES/DEFAULT",
        "category": "ConnectionEvents",
        "location": "westus",
        "operationName": "Microsoft.Cache/redisEnterprise/databases/ConnectionEvents/Read",
        "properties": {
            "eventEpochTime": 1674554402,
            "id": 6185063009002,
            "clientIP": "20.228.16.39",
            "eventType": "auth",
            "eventStatus": 8
        }
    }
```

## Log Analytics Queries

Here are some basic queries to use as models.

### [Queries for Basic, Standard, and Premium tiers](#tab/basic-standard-premium)

- Azure Cache for Redis client connections per hour within the specified IP address range:

```kusto
let IpRange = "10.1.1.0/24";
ACRConnectedClientList
// For particular datetime filtering, add '| where TimeGenerated between (StartTime .. EndTime)'
| where ipv4_is_in_range(ClientIp, IpRange)
| summarize ConnectionCount = sum(ClientCount) by TimeRange = bin(TimeGenerated, 1h)
```

- Unique Redis client IP addresses that have connected to the cache:

```kusto
ACRConnectedClientList
| summarize count() by ClientIp
```

### [Queries for Enterprise and Enterprise Flash tiers (preview)](#tab/enterprise-enterprise-flash)

- Azure Cache for Redis connections per hour within the specified IP address range:

```kusto
REDConnectionEvents
// For particular datetime filtering, add '| where EventTime between (StartTime .. EndTime)'
// For particular IP range filtering, add '| where ipv4_is_in_range(ClientIp, IpRange)'
// IP range can be defined like this 'let IpRange = "10.1.1.0/24";' at the top of query.
| extend EventTime = unixtime_seconds_todatetime(EventEpochTime)
| where EventType == "new_conn"
| summarize ConnectionCount = count() by TimeRange = bin(EventTime, 1h)
```

- Azure Cache for Redis authentication requests per hour within the specified IP address range:

```kusto
REDConnectionEvents
| extend EventTime = unixtime_seconds_todatetime(EventEpochTime)
// For particular datetime filtering, add '| where EventTime between (StartTime .. EndTime)'
// For particular IP range filtering, add '| where ipv4_is_in_range(ClientIp, IpRange)'
// IP range can be defined like this 'let IpRange = "10.1.1.0/24";' at the top of query.
| where EventType == "auth"
| summarize AuthencationRequestsCount = count() by TimeRange = bin(EventTime, 1h)
```

- Unique Redis client IP addresses that have connected to the cache:

```kusto
REDConnectionEvents
// https://docs.redis.com/latest/rs/security/audit-events/#status-result-codes
// EventStatus :
// 0    AUTHENTICATION_FAILED    -    Invalid username and/or password.
// 1    AUTHENTICATION_FAILED_TOO_LONG    -    Username or password are too long.
// 2    AUTHENTICATION_NOT_REQUIRED    -    Client tried to authenticate, but authentication isn’t necessary.
// 3    AUTHENTICATION_DIRECTORY_PENDING    -    Attempting to receive authentication info from the directory in async mode.
// 4    AUTHENTICATION_DIRECTORY_ERROR    -    Authentication attempt failed because there was a directory connection error.
// 5    AUTHENTICATION_SYNCER_IN_PROGRESS    -    Syncer SASL handshake. Return SASL response and wait for the next request.
// 6    AUTHENTICATION_SYNCER_FAILED    -    Syncer SASL handshake. Returned SASL response and closed the connection.
// 7    AUTHENTICATION_SYNCER_OK    -    Syncer authenticated. Returned SASL response.
// 8    AUTHENTICATION_OK    -    Client successfully authenticated.
| where EventType == "auth" and EventStatus == 2 or EventStatus == 8 or EventStatus == 7
| summarize count() by ClientIp
```

- Unsuccessful authentication attempts to the cache

```kusto
REDConnectionEvents
// https://docs.redis.com/latest/rs/security/audit-events/#status-result-codes
// EventStatus : 
// 0    AUTHENTICATION_FAILED    -    Invalid username and/or password.
// 1    AUTHENTICATION_FAILED_TOO_LONG    -    Username or password are too long.
// 2    AUTHENTICATION_NOT_REQUIRED    -    Client tried to authenticate, but authentication isn’t necessary.
// 3    AUTHENTICATION_DIRECTORY_PENDING    -    Attempting to receive authentication info from the directory in async mode.
// 4    AUTHENTICATION_DIRECTORY_ERROR    -    Authentication attempt failed because there was a directory connection error.
// 5    AUTHENTICATION_SYNCER_IN_PROGRESS    -    Syncer SASL handshake. Return SASL response and wait for the next request.
// 6    AUTHENTICATION_SYNCER_FAILED    -    Syncer SASL handshake. Returned SASL response and closed the connection.
// 7    AUTHENTICATION_SYNCER_OK    -    Syncer authenticated. Returned SASL response.
// 8    AUTHENTICATION_OK    -    Client successfully authenticated.
| where EventType == "auth" and EventStatus != 2 and EventStatus != 8 and EventStatus != 7
| project ClientIp, EventStatus, ConnectionId
```
---

## Next steps

For detailed information about how to create a diagnostic setting by using the Azure portal, CLI, or PowerShell, see [create diagnostic setting to collect platform logs and metrics in Azure](../azure-monitor/essentials/diagnostic-settings.md) article.
