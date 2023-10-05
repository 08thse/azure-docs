---
title: Resiliency policies powered by Dapr
titleSuffix: Azure Container Apps
description: Learn how to apply container app to container app resiliency in Azure Container Apps.
services: container-apps
author: hhunter-ms
ms.service: container-apps
ms.topic: conceptual
ms.date: 08/31/2023
ms.author: hannahhunter
ms.custom: ignite-fall-2023
# Customer Intent: As a developer, I'd like to learn how to make my container apps resilient using Azure Container Apps.
---

# Resiliency policies powered by Dapr

[Dapr-driven resiliency policies](https://docs.dapr.io/operations/resiliency/) empower developers to detect, mitigate, and recover from network failures using retries, timeouts, circuit breakers, and connections pools on container applications and Dapr components.

In the context of Azure Container Apps, resiliency policies are configured as child resources on a [container app](./container-app-resiliency.md) or [Dapr component](./dapr-resiliency.md). When an application initiates a network request, the callee (either the container app or Dapr component associated with the resiliency policies) dictates how timeouts, retries, and other resiliency policies are applied.

You can [define resiliency policies](#defining-resiliency-policies) using either Bicep, the Azure CLI, and the Azure Portal.

## Resiliency policy example

The following example demonstrates a basic resiliency policy. You can learn mor

```bicep
resource myPolicyDoc 'Microsoft.App/containerApps/appResiliencyPolicy@2023-08-01-preview' = {
  name: 'myResiliencyPolicy'
  parent: '${appName}'
  properties: {
    target: 'aca-app-name'
    timeoutPolicy: {
        responseTimeoutInSeconds: 15
        connectionTimeoutInSeconds: 5
    }
    rateLimitPolicy: {}
    httpRetryPolicy: {
        maxRetries: 5
        retryBackOff: {
          initialDelayInMilliseconds: 1000
          maxIntervalInMilliseconds: 10000
        }
        matches: {
            headers: [
                {
                    headerMatch: {
                        header: 'X-Content-Type'
                        match: { 
                            prefixMatch: 'GOATS'
                        }
                    }
                }
            ]
            httpStatusCodes: [
                502
                503
            ]
            errors: [
                5xx
                connect-failure
                reset          
            ]
        }
    } 
    tcpRetryPolicy: {
        maxConnectAttempts: 3
    }
    circuitBreakerPolicy: {
        consecutiveErrors: 5
        intervalInSeconds: 10
    }
    tcpConnectionPool: {
        maxConnections: 100
    }
    httpConnectionPool: {
        http1MaxPendingRequests: 1024
        http2MaxRequests: 1024
    }
  }
}
```

| Metadata | Description |
| ------ | ----------- |
| `name` | The name of your resiliency policy. This name will be tied to container apps and Dapr components. |
| `parent` | The resiliency policy scope, either your container app or Dapr component. | 
| `target` | The target application of the resiliency configuration (the application being called). |

## Next steps

Take a closer look at resiliency policies and how to define them for direct container app communication and Dapr component service invocation:

- [Container app to container app resiliency](./container-app-resiliency.md)
- [Dapr component resiliency](./dapr-resiliency.md)