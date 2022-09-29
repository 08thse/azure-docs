---
title: Enable partitioning in Azure Service Bus Premium namespaces
description: This article explains how to enable partitioning in Azure Service Bus Premium namespaces by using Azure portal, PowerShell, CLI, and programming languages (C#, Java, Python, and JavaScript)
ms.topic: how-to
ms.date: 08/30/2022 
ms.custom: devx-track-azurepowershell, devx-track-azurecli 
ms.devlang: azurecli
---

# Enable partitioning for an Azure Service Bus Premium namespace (Preview)
Service Bus partitions enable queues and topics, or messaging entities, to be partitioned across multiple message brokers and messaging stores. Partitioning means that the overall throughput of a partitioned entity is no longer limited by the performance of a single message broker or messaging store. In addition, a temporary outage of a messaging store, for example during an upgrade, doesn't render a partitioned queue or topic unavailable. Partitioned queues and topics can contain all advanced Service Bus features, such as support for transactions and sessions. For more information, See [Partitioned queues and topics](service-bus-partitioning.md). This article shows you different ways to enable duplicate partitioning for a Service Bus Premium namespace. All entities in this namespace will be partitioned.

> [!IMPORTANT]
> - Partitioning is available at entity creation for namespaces in the Premium SKU. Any previously existing partitioned entities in Premium namespaces continue to work as expected.
> - It's not possible to change the partitioning option on any existing namespace. You can only set the option when you create a namespace.
> - The assigned messaging units are always a multiplier of the amount of partitions in a namespace, and are equally distributed across the partitions. For example, in a namespace with 16MU and 4 partitions, each partition will be assigned 4MU.

> [!NOTE]
> Some limitations may be encountered during public preview, which will be resolved before going into GA. 
> - It is currently not possible to use JMS on partitioned entities. 
> - Metrics are currently only available on an aggregated namespace level, not for individual partitions.

## Using Azure portal
When creating a **namespace** in the Azure portal, set the **Partitioning** to **Enabled** and choose the number of partitions, as shown in the following image. 
:::image type="content" source="./media/enable-partitions/create-namespace.png" alt-text="Enable partitioning at the time of the namespace creation":::

## Using Azure Resource Manager template
To **create a namespace with partitioning enabled**, set `partitions` to a number larger than 1 in the namespace properties section. In the example below a partitioned namespace is created with 4 partitions, and 1 messaging unit assigned to each partition. For more information, see [Microsoft.ServiceBus namespaces template reference](/azure/templates/microsoft.servicebus/namespaces?tabs=json). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2022-10-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Premium",
        "capacity": 4      
      },
      "properties": {
        "partitions": 4
      }
    }
  ]
}
```

## Next steps
Try the samples in the language of your choice to explore Azure Service Bus features. 

- [Azure Service Bus client library samples for .NET (latest)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Azure Service Bus client library samples for Java (latest)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus client library samples for Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus client library samples for JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus client library samples for TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

Find samples for the older .NET and Java client libraries below:
- [Azure Service Bus client library samples for .NET (legacy)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Azure Service Bus client library samples for Java (legacy)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)
