---
title: How to enable Java metrics for Java apps in Azure Container Apps
description: Java metrics and configuration Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.date: 04/30/2024
ms.author: cshoe
zone_pivot_groups: container-apps-portal-or-cli
---

# Java metrics for Java apps in Azure Container Apps

Java Virtual Machine (JVM) metrics are critical for monitoring the health and performance of your Java applications. The data collected includes insights into memory usage, garbage collection, thread count of your JVM. Use the following metrics to help ensure the health and stability of your applications.

## Collected metrics

| Category| Title | Description | Metric ID | Unit |
|---|---|---|---|---|
| Java | `jvm.memory.total.used` | Total amount of memory used by heap or nonheap | `JvmMemoryTotalUsed` | bytes |
| Java | `jvm.memory.total.committed` | Total amount of memory guaranteed to be available for heap or nonheap | `JvmMemoryTotalCommitted` | bytes |
| Java | `jvm.memory.total.limit` | Total amount of maximum obtainable memory for heap or nonheap | `JvmMemoryTotalLimit` | bytes |
| Java | `jvm.memory.used` | Amount of memory used by each pool | `JvmMemoryUsed` | bytes |
| Java | `jvm.memory.committed` | Amount of memory guaranteed to be available for each pool | `JvmMemoryCommitted` | bytes |
| Java | `jvm.memory.limit` | Amount of maximum obtainable memory for each pool | `JvmMemoryLimit` | bytes |
| Java | `jvm.buffer.memory.usage` | Amount of memory used by buffers, such as direct memory | `JvmBufferMemoryUsage` | bytes |
| Java | `jvm.buffer.memory.limit` | Amount of total memory capacity of buffers | `JvmBufferMemoryLimit` | bytes |
| Java | `jvm.buffer.count` | Number of buffers in the memory pool | `JvmBufferCount` | n/a |
| Java | `jvm.gc.count` | Count of JVM garbage collection actions | `JvmGcCount` | n/a |
| Java | `jvm.gc.duration` | Duration of JVM garbage collection actions | `JvmGcDuration` | milliseconds |
| Java | `jvm.thread.count` | Number of executing platform threads | `JvmThreadCount` | n/a |

## Configuration

::: zone pivot="azure-portal"

TODO: screenshots here

::: zone-end

::: zone pivot="azure-cli"

There are two CLI options related to the app runtime and Java metrics:

| Option | Description |
|---|---|
| `--runtime` | The runtime of the container app. Supported values are `generic` and `java`. |
| `--enable-java-metrics` | A boolean option that enables or disables Java metrics for the app. Only applicable for Java runtime. |

> [!NOTE]
> * The `--enable-java-metrics=<true|false>` parameter implicitly sets `--runtime=java`. The `--runtime=generic` parameter resets all java runtime info.

### Enable Java metrics

You can enable Java metrics either via the `create` or `update` commands.

# [create](#tab/create)

```azurecli
az containerapp create \
  --name <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP> \
  --image <CONTAINER_IMAGE_LOCATION> \
  --enable-java-metrics=true
```

# [update](#tab/update)

```azurecli
az containerapp update \ 
  --name <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP> \
  --enable-java-metrics=true
```

### Disable Java metrics

You can disable Java metrics using the `up` command.

```azurecli
az containerapp up \ 
  --name <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP> \
  --enable-java-metrics=false
```

> [!NOTE]
> The container app restarts when you update java metrics flag.

::: zone-end

## View Java Metrics

TODO: some sample screenshots for java metrics

You can see Java metric names on Azure Monitor, but the data sets report as empty unless you use the `--enable-java-metrics` parameter to enable Java metrics.

## Next steps

> [!div class="nextstepaction"]
> [Monitor logs with Log Analytics](log-monitoring.md)
