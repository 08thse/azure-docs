---
title: Custom connectors
description: Links to topics about how to create, use, share, and certify custom connectors in Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, divswa, azla
ms.topic: conceptual
ms.date: 03/18/2022
# As a developer, I want learn about the capability to create custom connectors with operations that I can use in my Azure Logic Apps workflows.
---

# Custom connectors in Azure Logic Apps

Without writing any code, you can quickly create automated integration workflows when you use the prebuilt connector operations in Azure Logic Apps. Briefly, a [*connector*](/connectors/connectors) is a proxy or a wrapper around an API, such as Office 365 or Salesforce, that helps the underlying service talk to Azure Logic Apps. Connectors help your workflows connect and access data, events, and actions across other apps, services, systems, protocols, and platforms. You can choose from [hundreds of connectors](/connectors/connector-reference/connector-reference-logicapps-connectors) available in Azure Logic Apps. Each connector offers triggers, actions, or both in the form of operations that you add to your workflows. When you use these operations, you expand the capabilities for your cloud apps and on-premises apps to work with new and existing data. Connectors are powered by the connector infrastructure that runs in Azure.

Connectors in Azure Logic Apps are either *built-in* and *managed* connectors. *Built-in* connector operations run natively on the Azure Logic Apps runtime, which means they're hosted in the same process as the runtime and provide higher throughput, low latency, and local connectivity. *Managed connector* operations are deployed, hosted, and managed by Microsoft. When you use a connector operation for the first time in a workflow, some connectors don't require that you create a connection first, but many other connectors require this step. Each connection that you create is actually a separate Azure resource that provides access to the target app, service, system, protocol, or platform.

Sometimes though, you might want to call REST APIs that aren't available as prebuilt connectors. To support more tailored scenarios, you can create your own [*custom connectors*](/connectors/custom-connectors/) to offer triggers and actions that aren't available as prebuilt operations.

This article provides an overview about custom connectors for Consumption logic app workflows and Standard logic app workflows, which are each powered by a different Azure Logic Apps runtime. For more information about connectors in Azure Logic Apps, review the following documentation:

* [About connectors in Azure Logic Apps](../connectors/apis-list.md)
* [Built-in connector operations in Azure Logic Apps](../connectors/built-in.md)
* [Managed connector operations in Azure Logic Apps](../connectors/managed.md)

<a name="custom-connector-consumption"></a>

## Custom connectors for Consumption logic apps

For Consumption logic app workflows in [multi-tenant Azure Logic Apps](logic-apps-overview.md), you can create [custom connectors](/connectors/custom-connectors/) from REST APIs. The [Connectors documentation](/connectors/connectors) provides more overview information about how to create custom connectors for Consumption logic apps, including complete basic and advanced tutorials. The following list also provides direct links to information about custom connectors for Consumption logic apps:

  * [Create an Azure Logic Apps connector](/connectors/custom-connectors/create-logic-apps-connector)
  * [Create a custom connector from an OpenAPI definition](/connectors/custom-connectors/define-openapi-definition)
  * [Create a custom connector from a Postman collection](/connectors/custom-connectors/define-postman-collection)
  * [Use a custom connector from a logic app](/connectors/custom-connectors/use-custom-connector-logic-apps)
  * [Share custom connectors in your organization](/connectors/custom-connectors/share)
  * [Submit your connectors for Microsoft certification](/connectors/custom-connectors/submit-certification)
  * [Custom connector FAQ](/connectors/custom-connectors/faq)

<a name="custom-connector-standard"></a>

## Custom connectors for Standard logic apps

For Standard logic app workflows in [single-tenant Azure Logic Apps](logic-apps-overview.md), a redesigned runtime powers Standard logic app workflows. This runtime differs from the multi-tenant Azure Logic Apps runtime that powers Consumption logic app workflows. The single-tenant runtime uses the [Azure Functions extensibility model](../azure-functions/functions-bindings-register.md), which provides a key capability for you to create and use your own built-in connectors in Standard workflows. In a Standard logic app resource, a connection definition file contains the required configuration for the connections created by connectors. When single-tenant Azure Logic Apps officially released, new built-in connectors included Azure Blob Storage, Azure Event Hubs, Azure Service Bus, and SQL Server, and over time, this list continues to grow. However, if you need to use connectors that aren't available in Standard logic app workflows, you can create your own built-in connectors with the same Azure Functions extensibility framework that's used by the built-in connectors included for Standard workflows.

<a name="built-in-connector-extensibility-model"></a>

### Built-in connector extensibility model

In single-tenant Azure Logic Apps, the built-in connector extensibility model uses the Azure Functions extensibility model, which enables the capability to add built-in connector implementations, such as Azure Functions extensions. You can use this capability to create, build, and package your own built-in connectors as Azure Functions extensions that anyone can use.

In this built-in connector extensibility model, you have to implement the following operation parts:

* Operation descriptions

  Operation descriptions are metadata about the operations implemented by your custom built-in connector. The workflow designer primarily uses these descriptions to drive the authoring and monitoring experiences for your connector's operations.

  For example, the designer uses operation descriptions to understand the input parameters required by a specific operation and to facilitate generating the outputs' property tokens, based on the schema for the operation's outputs.

* Operation invocations

  At runtime, the Azure Logic Apps runtime uses these implementations to call the specified operation in the workflow definition.

When you're done, you also have to register custom built-in connector with the [Azure Functions runtime extension](../azure-functions/functions-bindings-register.md). For the steps, review [Register your connector as an Azure Functions extension](#register-connector).

<a name="service-provider-interface-implementation"></a>

### Service provider interface implementation

A custom built-in connector is also called a *service provider*, which can connect to the underlying service in multiple ways, such as a connection string, Azure Active Directory (Azure AD), and a managed identity. The **Microsoft.Azure.Workflows.WebJobs.Extension** NuGet package that you add to your class library project provides the  service provider interface that's named **IServiceOperationsTriggerProvider**, which your custom built-in connector has to implement. As part of the operation descriptions, this **IServiceOperationsTriggerProvider** interface provides the following methods that your custom built-in connector has to implement:

* **GetOperations()**
* **GetService()**

The workflow designer uses these methods to describe the triggers and actions that the custom built-in connector provides and shows on the designer surface. The **GetService()** method also specifies the connection's input parameters that are required by the designer.

For any action operations, your custom built-in connector has to implement the method that's named **InvokeActionOperation()**, which is invoked during action execution. If you want to use the Azure Functions binding that's used for the managed Azure connector triggers, you have to provide the connection information and trigger bindings as required by Azure Functions. Your custom built-in connector has to implement the following methods for the Azure Functions binding:

* **GetBindingConnectionInformation()**: If you want to use the Azure Functions trigger type, this method provides the required connection parameters information to the Azure Functions trigger binding.

* **GetTriggerType()**: If you want to use an Azure Functions built-in trigger as a custom built-in connector trigger, this method returns the string that's the same as the **type** parameter in the Azure Functions trigger binding.

The following diagram shows the method implementation that's required by the Azure Logic Apps designer and runtime:

![Conceptual diagram showing method implementation required by the Azure Logic Apps designer and runtime.](./media/create-built-in-custom-connector-standard/service-provider-interface-model.png)

The following table has more information about the methods that require implementation:

| Operation method | Method details | Example |
|------------------|----------------|---------|
| **GetOperations()** | The Azure Logic Apps designer requires this method, which provides a high-level description for your service, including the service descriptions, brand color, icon URL, connection parameters, capabilities, and so on. | ![Screenshot showing class library file with "GetOperations()" method implementation.](./media/create-built-in-custom-connector-standard/get-operations-method.png) |
| **GetService()** | The Azure Logic Apps designer requires this method, which gets the list of operations that are implemented by your service. This operations list is based on Swagger schema. | ![Screenshot showing class library file with GetService() method implementation.](./media/create-built-in-custom-connector-standard/get-service-method.png) |
| **InvokeOperation()** | This method is invoked for each action operation that executes during runtime. You can use any client, such as FTPClient, HTTPClient, and so on, as required by your custom built-in connector actions. If you're only implementing the trigger as in this example, you don't have to implement this method. | ![Screenshot showing class library file with "InvokeOperation()" method implementation.](./media/create-built-in-custom-connector-standard/invoke-operation-method.png) |
| **GetBindingConnectionInformation()** | If you want to use the Azure Functions trigger type, this method provides the required connection parameters information to the Azure Functions trigger binding. | ![Screenshot showing class library file with "GetBindingConnectionInformation()" method implementation.](./media/create-built-in-custom-connector-standard/get-binding-connection-information-method.png) |
| **GetFunctionTriggerType()** | If you want to use an Azure Functions built-in trigger as a custom built-in connector trigger, you have to return the string that's the same as the **type** parameter in the Azure Functions trigger binding, for example, `"type": "cosmosDBTrigger"`. | ![Screenshot showing class library file with "GetFunctionTriggerType()" method implementation.](./media/create-built-in-custom-connector-standard/get-function-trigger-type-method.png) |
||||
