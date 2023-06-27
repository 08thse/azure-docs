---
title: Using Azure Functions for Azure Cache for Redis
description: Learn how to use Azure Functions Azure Cache for Redis
author: flang-msft
zone_pivot_groups: programming-languages-set-functions-lang-workers

ms.author: franlanglois
ms.service: cache
ms.topic: conceptual
ms.date: 06/26/2023

---

# Overview of Azure functions for Azure Cache for Redis

This article describes how to use Azure Cache for Redis with Azure Functions to create optimized serverless and event-driven architectures.

Azure Cache for Redis can be used as a trigger for Azure Functions, allowing Redis to initiate a serverless workflow. This functionality can be highly useful in data architectures like a write-behind cache, or any event-based architectures.

Azure Functions is an event-driven programming where triggers and bindings are key features, with which you can easily build event-driven serverless applications. Azure Cache for Redis provides a set of building blocks and best practices for building distributed applications, including microservices, state management, pub/sub messaging, and more.

With the integration between Azure Cache for Redis and Functions, you can build functions that react to events from Azure Cache for Redis or external systems.

| Action  | Direction | Type |
|---------|-----------|------|
| Triggers on Redis pubsub messages   | N/A | [RedisPubSubTrigger](functions-bindings-cache-trigger-redispubsub.md) |
| Triggers on Redis lists | N/A | [RedisListsTrigger](functions-bindings-cache-trigger-redislists.md)  |
| Triggers on Redis streams | N/A | [RedisStreamsTrigger](functions-bindings-cache-trigger-redisstreams.md) |

## Scope of availability for functions triggers

|Tier     | Basic | Standard, Premium  | Enterprise, Enterprise Flash  |
|---------|:---------:|:---------:|:---------:|
|Pub/Sub  | Yes  | Yes  |  Yes  |
|Lists | Yes  | Yes   |  Yes  |
|Streams | Yes  | Yes  |  Yes  |

> [!IMPORTANT]
> Redis triggers are not currently supported on consumption functions.
>

::: zone pivot="programming-language-csharp"

## Install extension

Client Library

You need to install `Microsoft.Azure.WebJobs.Extensions.Redis`, which is the extension that allows Redis keyspace notifications to be used as triggers in Azure Functions.

Install these packages by going to the terminal tab in VS Code and entering the following commands:

```dos
dotnet add package Microsoft.Azure.WebJobs.Extensions.Redis
```

::: zone-end

::: zone pivot="programming-language-javascript,programming-language-python,programming-language-java,programming-language-powershell"

## Install bundle

Presently, the extension has not been added to the Microsoft.Azure.Functions.ExtensionBundle.

Install the Redis Extension manually for now following this procedure.

1. Install the .Net SDK.

1. Create a Java function project. You could use Maven:
   `mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8`

1. Remove `extensionBundle` from `host.json`

1. Run `func extensions install --package Microsoft.Azure.WebJobs.Extensions.Redis --version <version>`
   - `<version>` should be the lateste version of the extenstion from NuGet

1. Add the Java library for Redis bindings to the `pom.xml` file:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.functions</groupId>
      <artifactId>azure-functions-java-library-redis</artifactId>
      <version>[0.0.0,)</version>
    </dependency>
    ```

1. Replace the existing `Function.java` file with the following code:

    ```java
    import com.microsoft.azure.functions.*;
    import com.microsoft.azure.functions.tation.*;
    import com.microsoft.azure.functions.s.annotation.*;
    public class Function {
      @FunctionName("PubSubTrigger")
      public void PubSubTrigger(
        @RedisPubSubTrigger(
          name = "message",
          connectionStringSetting = "Redis",
          channel = "pubsubTest")
          String message,
        final ExecutionContext context) {
        context.getLogger().info("Java tion triggered on pub/sub age '" + message + "' from nel 'pubsubTest'.");
      }
    }
    ```

::: zone-end

## Next steps
- [Introduction to Azure Functions](/azure/azure-functions/functions-overview)
- [Get started with Azure Functions triggers in Azure Cache for Redis](/azure/azure-cache-for-redis/cache-tutorial-functions-getting-started)
- [Using Azure Functions and Azure Cache for Redis to create a write-behind cache](/azure/azure-cache-for-redis/cache-tutorial-write-behind)
