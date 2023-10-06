---
title: Automate function app resource deployment to Azure
description: Learn how to build a Bicep file or an Azure Resource Manager template that deploys your function app.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 10/02/2023
ms.custom: fasttrack-edit, devx-track-bicep, devx-track-arm-template
zone_pivot_groups: functions-hosting-plan
---

# Automate resource deployment for your function app in Azure Functions

You can use a Bicep file or an Azure Resource Manager template to deploy a function app. This article outlines the required resources and parameters for doing so. You might need to deploy other resources, depending on the [triggers and bindings](functions-triggers-bindings.md) in your function app. For more information about creating Bicep files, see [Understand the structure and syntax of Bicep files](../azure-resource-manager/bicep/file.md). For more information about creating templates, see [Authoring Azure Resource Manager templates](../azure-resource-manager/templates/syntax.md).

For sample Bicep files and ARM templates, see:

- [ARM templates for function app deployment](https://github.com/Azure-Samples/function-app-arm-templates)
- [Function app on Consumption plan]
- [Function app on Elastic Premium plan](#premium)
- [Function app on Azure App Service plan]

>[!IMPORTANT]
>Deployment code depends on how your function app is hosted and whether you are deploying code or a containerized function app. This article supports the three Functions hosting plans (Consumption, Elastic Premium, and Dedicated), as well as container deployments to both Azure Container Apps and Azure Arc.  
> Choose your desired hosting at the top of the article.

An Azure Functions deployment typically consists of these resources:
:::zone pivot="premium-plan,dedicated-plan"  
| Resource  | Requirement | Syntax and properties reference |
|------|-------|----|
| A [storage account](#storage-account) | Required | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| An [Application Insights](#application-insights) component | Optional<sup>1</sup> | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)|  
| A [hosting plan](#hosting-plan)| Required<sup>2</sup> | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms) |
| A [function app](#function-app) | Required | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)  |
:::zone-end  
:::zone pivot="consumption-plan"  
| Resource  | Requirement | Syntax and properties reference |
|------|-------|----|
| A [storage account](#storage-account) | Required | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| An [Application Insights](#application-insights) component | Optional<sup>1</sup> | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)|  
| A [function app](#function-app) | Required | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)  |
:::zone-end  
:::zone pivot="container-apps"  
| Resource  | Requirement | Syntax and properties reference |
|------|-------|----|
| A [storage account](#storage-account) | Required | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| An [Application Insights](#application-insights) component | Optional<sup>1</sup> | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)|  
| A [managed environment](#managed-environment) | Required | [Microsoft.App/managedEnvironments](/azure/templates/microsoft.app/managedenvironments) |
| A [function app](#function-app) | Required | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)  |
:::zone-end  
:::zone pivot="azure-arc"  
| Resource  | Requirement | Syntax and properties reference |
|------|-------|----|
| A [storage account](#storage-account) | Required | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| An [Application Insights](#application-insights) component | Optional<sup>1</sup> | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)|  
| An [App Service Kubernetes environment](#kubernetes-environment) | Required | [Microsoft.ExtendedLocation/customLocations](/azure/templates/microsoft.extendedlocation/customlocations)<br/> |
| A [function app](#function-app) | Required | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)  |
:::zone-end 

<sup>1</sup>While not required, you should configure Application Insights for your app.  
:::zone pivot="premium-plan,dedicated-plan" 
<sup>2</sup>An explicit hosting plan isn't required when you choose to host your function app in a [Consumption plan](./consumption-plan.md).
:::zone-end  
:::zone pivot="container-apps,azure-arc"  
## Prerequisites
:::zone-end  
:::zone pivot="container-apps" 
This article assumes that you have already created a managed environment in Azure Container Apps. You need both the name and the ID of the managed environment to create a function app hosted on Container Apps.  
:::zone-end  
:::zone pivot="azure-arc" 
This article assumes that you have already created an [App Service-enabled custom location](../app-service/overview-arc-integration.md) on an [Azure Arc-enabled Kubernetes cluster](../azure-arc/kubernetes/overview.md). managed environment in Azure Container Apps. You need both the custom location ID and the Kubernetes environment ID to create a function app hosted in an Azure Arc custom location.  
:::zone-end  
<a name="storage"></a>
## Storage account

A storage account is required for a function app. You need a general purpose account that supports blobs, tables, queues, and files. For more information, see [Azure Functions storage account requirements](storage-considerations.md#storage-account-requirements).

[!INCLUDE [functions-storage-access-note](../../includes/functions-storage-access-note.md)]

### [Bicep](#tab/bicep)

```bicep
resource storageAccountName 'Microsoft.Storage/storageAccounts@2022-05-01' = {
  name: storageAccountName
  location: location
  kind: 'StorageV2'
  sku: {
    name: storageAccountType
  }
  properties: {
    supportsHttpsTrafficOnly: true
    defaultToOAuthAuthentication: true
  }
}
```

For a complete example, see [main.bicep](https://github.com/Azure-Samples/function-app-arm-templates/blob/main/function-app-linux-consumption/main.bicep#L37) in the templates repository.

### [JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2022-05-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    "kind": "StorageV2",
    "sku": {
      "name": "[parameters('storageAccountType')]"
    },
    "properties": {
      "supportsHttpsTrafficOnly": true,
      "defaultToOAuthAuthentication": true
    }
  }
]
```

For a complete example, see [azuredeploy.json](https://github.com/Azure-Samples/function-app-arm-templates/blob/main/function-app-linux-consumption/azuredeploy.json#L77) in the templates repository.

---

You need to set the connection string of this storage account as the `AzureWebJobsStorage` app setting, which is required by Functions. The templates in this article construct this connection string value based on the created storage account. For more information, see [Application settings](#application-settings).  

### Storage logs

Because the storage account is used for important function app data, you may want to monitor for modification of that content. To do this, you need to configure Azure Monitor resource logs for Azure Storage. In the following example, a Log Analytics workspace named `myLogAnalytics` is used as the destination for these logs. This same workspace can be used for the Application Insights resource defined later.

#### [Bicep](#tab/bicep)

```bicep
resource blobService 'Microsoft.Storage/storageAccounts/blobServices@2021-09-01' existing = {
  name:'default'
  parent:storageAccountName
}

resource storageDataPlaneLogs 'Microsoft.Insights/diagnosticSettings@2021-05-01-preview' = {
  name: '${storageAccountName}-logs'
  scope: blobService
  properties: {
    workspaceId: myLogAnalytics.id
    logs: [
      {
        category: 'StorageWrite'
        enabled: true
      }
    ]
    metrics: [
      {
        category: 'Transaction'
        enabled: true
      }
    ]
  }
}
```

#### [JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Insights/diagnosticSettings",
    "apiVersion": "2021-05-01-preview",
    "scope": "[format('Microsoft.Storage/storageAccounts/{0}/blobServices/default', parameters('storageAccountName'))]",
    "name": "[parameters('storageDataPlaneLogsName')]",
    "properties": {
        "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('myLogAnalytics'))]",
        "logs": [
          {
            "category": "StorageWrite",
            "enabled": true
          }
        ],
        "metrics": [
          {
            "category": "Transaction",
            "enabled": true
          }
        ]
    }
  }
]
```

---

See [Monitoring Azure Storage](../storage/blobs/monitor-blob-storage.md) for instructions on how to work with these logs.

## Application Insights

Application Insights is recommended for monitoring your function app executions. The Application Insights resource is defined with the type `Microsoft.Insights/components` and the kind `web`:

### [Bicep](#tab/bicep)

:::code language="bicep" source="~/function-app-arm-templates/function-app-linux-consumption/main.bicep" range="60-70":::

For a complete example, see [main.bicep](https://github.com/Azure-Samples/function-app-arm-templates/blob/main/function-app-linux-consumption/main.bicep#L60) in the templates repository.

### [JSON](#tab/json)

:::code language="json" source="~/function-app-arm-templates/function-app-linux-consumption/azuredeploy.json" range="102-114":::

For a complete example, see [azuredeploy.json](https://github.com/Azure-Samples/function-app-arm-templates/blob/main/function-app-linux-consumption/azuredeploy.json#L102) in the templates repository.

---

The connection  also needs to be provided to the function app using the `APPINSIGHTS_INSTRUMENTATIONKEY` application setting. This property is specified in the `appSettings` collection in the `siteConfig` object:

You need to set the connection string for this Application Insights instance as the `APPLICATIONINSIGHTS_CONNECTION_STRING` app setting. The templates in this article obtain the connection string value for the created instance. Older template versions may have instead used the `APPINSIGHTS_INSTRUMENTATIONKEY` to set the instrumentation key, which is no longer recommended. For more information, see [Application settings](#application-settings).

:::zone pivot="premium-plan,dedicated-plan"  
## Hosting plan

Apps hosted in an Azure Functions [Premium plan](./functions-premium-plan.md) or [Dedicated (App Service) plan](./dedicated-plan.md) must have the hosting plan explicitly defined.
::: zone-end
:::zone pivot="premium-plan" 
The Premium plan offers the same scaling as the Consumption plan but includes dedicated resources and extra capabilities. To learn more, see [Azure Functions Premium Plan](functions-premium-plan.md).

A Premium plan is a special type of `serverfarm` resource. You can specify it by using either `EP1`, `EP2`, or `EP3` for the `Name` property value in the `sku` property. The way that you define the Functions hosting plan depends on whether your function app runs on Windows or on Linux: 

### [Linux](#tab/linux/bicep)

To run your app on Linux, you must also set property `"reserved": true` for the serverfarms resource:

```bicep
resource hostingPlan 'Microsoft.Web/serverfarms@2022-03-01' = {
  name: hostingPlanName
  location: location
  sku: {
    name: 'EP1'
    tier: 'ElasticPremium'
    family: 'EP'
  }
  kind: 'elastic'
  properties: {
    maximumElasticWorkerCount: 20
    reserved: true
  }
}
```

### [Linux](#tab/linux/json)

To run your app on Linux, you must also set property `"reserved": true` for the serverfarms resource:

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2022-03-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[parameters('location')]",
    "sku": {
      "name": "EP1",
      "tier": "ElasticPremium",
      "family": "EP",
    },
    "kind": "elastic",
    "properties": {
      "maximumElasticWorkerCount": 20,
      "reserved": true
    }
  }
]
```

### [Windows](#tab/windows/bicep)

```bicep
resource hostingPlan 'Microsoft.Web/serverfarms@2022-03-01' = {
  name: hostingPlanName
  location: location
  sku: {
    name: 'EP1'
    tier: 'ElasticPremium'
    family: 'EP'
  }
  kind: 'elastic'
  properties: {
    maximumElasticWorkerCount: 20
  }
}
```

### [Windows](#tab/windows/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2022-03-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[parameters('location')]",
    "sku": {
      "name": "EP1",
      "tier": "ElasticPremium",
      "family": "EP"
    },
    "kind": "elastic",
    "properties": {
      "maximumElasticWorkerCount": 20
    }
  }
]
```

---  
::: zone-end
:::zone pivot="dedicated-plan" 
In the Dedicated (App Service) plan, your function app runs on dedicated VMs on Basic, Standard, and Premium SKUs in App Service plans, similar to web apps. For more information, see [Dedicated plan](./dedicated-plan.md).

For a sample Bicep file/Azure Resource Manager template, see [Function app on Azure App Service plan]

In Functions, the Dedicated plan is just a regular App Service plan, which is defined by a `serverfarm` resource. The way that you define the hosting plan depends on whether your function app runs on Windows or on Linux: 

### [Linux](#tab/linux/bicep)

```bicep
resource hostingPlan 'Microsoft.Web/serverfarms@2022-03-01' = {
  name: hostingPlanName
  location: location
  sku: {
    tier: 'Standard'
    name: 'S1'
    size: 'S1'
    family: 'S'
    capacity: 1
  }
  properties: {
    reserved: true
  }
}
```

### [Linux](#tab/linux/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2022-03-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[parameters('location')]",
    "sku": {
      "tier": "Standard",
      "name": "S1",
      "size": "S1",
      "family": "S",
      "capacity": 1
    },
    "properties": {
      "reserved": true
    }
  }
]
```

### [Windows](#tab/windows/bicep)

```bicep
resource hostingPlanName 'Microsoft.Web/serverfarms@2022-03-01' = {
  name: hostingPlanName
  location: location
  sku: {
    tier: 'Standard'
    name: 'S1'
    size: 'S1'
    family: 'S'
    capacity: 1
  }
}
```

### [Windows](#tab/windows/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2022-03-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[parameters('location')]",
    "sku": {
      "tier": "Standard",
      "name": "S1",
      "size": "S1",
      "family": "S",
      "capacity": 1
    }
  }
]
```

---

::: zone-end
:::zone pivot="consumption-plan"
>[!NOTE]  
>Explicitly defining a hosting plan isn't required for apps running in a Consumption plan. If you skip this section of the template, a plan is automatically either created or selected on a per-region basis when you create the function app resource itself.

The Consumption plan is a special type of `serverfarm` resource. You can specify it by using the `Dynamic` value for the `computeMode` and `sku` properties. The way that you can explicitly define a hosting plan depends on whether your function app runs on Windows or on Linux: 

### [Linux](#tab/linux/bicep)

To run your app on Linux, you must also set the property `"reserved": true` for the `serverfarms` resource:

```bicep
resource hostingPlan 'Microsoft.Web/serverfarms@2022-03-01' = {
  name: hostingPlanName
  location: location
  sku: {
    name: 'Y1'
    tier: 'Dynamic'
    size: 'Y1'
    family: 'Y'
    capacity: 0
  }
  properties: {
    computeMode: 'Dynamic'
    reserved: true
  }
}
```

### [Linux](#tab/linux/json)

To run your app on Linux, you must also set the property `"reserved": true` for the `serverfarms` resource:

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2022-03-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[parameters('location')]",
    "sku": {
      "name": "Y1",
      "tier": "Dynamic",
      "size": "Y1",
      "family": "Y",
      "capacity":0
    },
    "properties": {
      "computeMode": "Dynamic",
      "reserved": true
    }
  }
]
```

### [Windows](#tab/windows/bicep)

```bicep
resource hostingPlan 'Microsoft.Web/serverfarms@2022-03-01' = {
  name: hostingPlanName
  location: location
  sku: {
    name: 'Y1'
    tier: 'Dynamic'
    size: 'Y1'
    family: 'Y'
    capacity: 0
  }
  properties: {
    computeMode: 'Dynamic'
  }
}
```

### [Windows](#tab/windows/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2022-03-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[parameters('location')]",
    "sku": {
      "name": "Y1",
      "tier": "Dynamic",
      "size": "Y1",
      "family": "Y",
      "capacity": 0
    },
    "properties": {
      "computeMode": "Dynamic"
    }
  }
]
```

---
 
::: zone-end
:::zone pivot="container-apps" 
## Managed environment

`<<TBD>>`

::: zone-end
:::zone pivot="azure-arc" 
## Kubernetes environment
Azure Functions can be deployed to [Azure Arc-enabled Kubernetes](../app-service/overview-arc-integration.md). This process largely follows [deploying to an App Service plan](#deploy-on-app-service-plan), with a few differences to note.

To create the app and plan resources, you must have already [created an App Service Kubernetes environment](../app-service/manage-create-arc-environment.md) for an Azure Arc-enabled Kubernetes cluster. These examples assume you have the resource ID of the custom location and App Service Kubernetes environment that you're deploying to. For most Bicep files/ARM templates, you can supply these values as parameters.

# [Bicep](#tab/bicep)

```bicep
param kubeEnvironmentId string
param customLocationId string
```

# [JSON](#tab/json)

```json
"parameters": {
  "kubeEnvironmentId" : {
    "type": "string"
  },
  "customLocationId" : {
    "type": "string"
  }
}
```

---

Both sites and plans must reference the custom location through an `extendedLocation` field. This block sits outside of `properties`, peer to `kind` and `location`:

# [Bicep](#tab/bicep)

```bicep
resource hostingPlan 'Microsoft.Web/serverfarms@2022-03-01' = {
  ...
  {
    extendedLocation: {
      name: customLocationId
    }
  }
}
```

# [JSON](#tab/json)

```json
{
  "type": "Microsoft.Web/serverfarms",
  ...
  {
    "extendedLocation": {
      "name": "[parameters('customLocationId')]"
    },
  }
}
```

---

The plan resource should use the Kubernetes (K1) SKU, and its `kind` field should be `linux,kubernetes`. Within `properties`, `reserved` should be `true`, and `kubeEnvironmentProfile.id` should be set to the App Service Kubernetes environment resource ID. An example plan might look like:

# [Bicep](#tab/bicep)

```bicep
resource hostingPlan 'Microsoft.Web/serverfarms@2022-03-01' = {
  name: hostingPlanName
  location: location
  kind: 'linux,kubernetes'
  sku: {
    name: 'K1'
    tier: 'Kubernetes'
  }
  extendedLocation: {
    name: customLocationId
  }
  properties: {
    kubeEnvironmentProfile: {
      id: kubeEnvironmentId
    }
    reserved: true
  }
}
```

# [JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2022-03-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[parameters('location')]",
    "kind": "linux,kubernetes",
    "sku": {
      "name": "K1",
      "tier": "Kubernetes"
    },
    "extendedLocation": {
      "name": "[parameters('customLocationId')]"
    },
    "properties": {
      "kubeEnvironmentProfile": {
        "id": "[parameters('kubeEnvironmentId')]"
      },
      "reserved": true
    }
  }
]
```

---

::: zone-end

## Function app configuration

Functions provides the following options for configuring your app: 

| Configuration | `Microsoft.Web/sites` property |  
| ---- | ---- | ---- |
| Application settings | `siteConfig.appSettings` collection |
| Site settings | `siteConfig` |

The following application settings are required for a given operating system and hosting option:

### [Linux](#tab/linux)

+ [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey)<sup>1</sup>

+ [`APPLICATIONINSIGHTS_CONNECTION_STRING`](functions-app-settings.md#applicationinsights_connection_string)

+ [`AzureWebJobsStorage`](functions-app-settings.md#azurewebjobsstorage)

+ [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version)

+ [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime)
:::zone pivot="consumption-plan,premium-plan"  
+ [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring)

+ [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare)
::: zone-end  
:::zone pivot="dedicated-plan,premium-plan"  
+ [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package)
::: zone-end  

<sup>1</sup>`APPINSIGHTS_INSTRUMENTATIONKEY` is deprecated. Use `APPLICATIONINSIGHTS_CONNECTION_STRING` instead.  

### [Windows](#tab/windows)

+ [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey)<sup>1</sup>

+ [`APPLICATIONINSIGHTS_CONNECTION_STRING`](functions-app-settings.md#applicationinsights_connection_string)

+ [`AzureWebJobsStorage`](functions-app-settings.md#azurewebjobsstorage)

+ [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version)

+ [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime)
:::zone pivot="consumption-plan,premium-plan"  
+ [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring)

+ [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare)
::: zone-end
:::zone pivot="consumption-plan,premium-plan,dedicated-plan"   
+ [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package)

+ [`WEBSITE_NODE_DEFAULT_VERSION`](functions-app-settings.md#website_node_default_version)<sup>2</sup>
::: zone-end  
<sup>1</sup>`APPINSIGHTS_INSTRUMENTATIONKEY` is deprecated. Use `APPLICATIONINSIGHTS_CONNECTION_STRING` instead.  
<sup>2</sup>Supported only for Node.js deployments on Windows.

---

The following site settings may be required on the `siteConfig` property:

### [Linux](#tab/linux)

+ [`linuxFxVersion`](functions-app-settings.md#linuxfxversion) 

+ [`netFrameworkVersion`](functions-app-settings.md#netframeworkversion)<sup>*</sup>


### [Windows](#tab/windows)

+ [`netFrameworkVersion](functions-app-settings.md#netframeworkversion)<sup>*</sup>

---

<sup>*</sup>Only required for .NET (C#) apps.

## Function app

The function app resource is defined by a resource of type **Microsoft.Web/sites** and kind of at leasdt **functionapp**.
:::zone pivot="consumption-plan" 
<a name="consumption"></a>
The Consumption plan automatically allocates compute power when your code is running, scales-out as necessary to handle load, and then scales-in when code isn't running. You don't have to pay for idle VMs, and you don't have to reserve capacity in advance. To learn more, see [Consumption plan](consumption-plan.md).

The way that you define a function app depends on whether you are hosting on Linux or on Windows:

### [Linux](#tab/linux)

When running on Linux, you must set `"kind": "functionapp,linux"` and `"reserved": true` for the function app. Linux apps must also include a `linuxFxVersion` property under `siteConfig`. If you're just deploying code, the value for this property is determined by your desired runtime stack in the format of `<runtime>|<runtimeVersion>`. For more information, see the [linuxFxVersion site setting](functions-app-settings.md#linuxfxversion) reference.

For a list of application settings required when running on Linux in a Consumption plan, see [Function app configuration](#function-app-configuration).

For a sample Bicep file/Azure Resource Manager template, see [Azure Function App Hosted on Linux Consumption Plan](https://github.com/Azure-Samples/function-app-arm-templates/tree/main/function-app-linux-consumption).

### [Windows](#tab/windows)

For a list of application settings required when running on Windows in a Consumption plan, see [Function app configuration](#function-app-configuration).

For a sample Bicep file/Azure Resource Manager template, see [Azure Function App Hosted on Windows Consumption Plan](https://github.com/Azure-Samples/function-app-arm-templates/tree/main/function-app-windows-consumption).

---

>[!NOTE]  
>If you choose to optionally define your Consumption plan, you must set the `serverFarmId` property on the app so that it points to the resource ID of the plan. Make sure that the function app has a `dependsOn` setting that also references the plan. IF you didn't explicitly define a plan, it gets created for you. 

The settings required by a function app running in Consumption plan also differ between Windows and Linux. For a comprehensive list, see [Function app configuration](#function-app-configuration).

### [Linux](#tab/linux/bicep)

```bicep
resource functionApp 'Microsoft.Web/sites@2022-03-01' = {
  name: functionAppName
  location: location
  kind: 'functionapp,linux'
  properties: {
    reserved: true
    serverFarmId: hostingPlan.id
    siteConfig: {
      linuxFxVersion: 'node|14'
      appSettings: [
        {
          name: 'APPINSIGHTS_INSTRUMENTATIONKEY'
          value: applicationInsights.properties.InstrumentationKey
        }
        {
          name: 'AzureWebJobsStorage'
          value: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};EndpointSuffix=${environment().suffixes.storage};AccountKey=${storageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'FUNCTIONS_EXTENSION_VERSION'
          value: '~4'
        }
        {
          name: 'FUNCTIONS_WORKER_RUNTIME'
          value: 'node'
        }
      ]
    }
  }
}
```

### [Linux](#tab/linux/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/sites",
    "apiVersion": "2022-03-01",
    "name": "[parameters('functionAppName')]",
    "location": "[parameters('location')]",
    "kind": "functionapp,linux",
    "dependsOn": [
      "[resourceId('Microsoft.Insights/components', parameters('applicationInsightsName'))]",
      "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
    ],
    "properties": {
      "reserved": true,
      "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "siteConfig": {
        "linuxFxVersion": "node|14",
        "appSettings": [
          {
            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
            "value": "[reference(resourceId('Microsoft.Insights/components', parameters('applicationInsightsName')), '2020-02-02).InstrumentationKey]"
          },
          {
            "name": "AzureWebJobsStorage",
            "value": "[format('DefaultEndpointsProtocol=https;AccountName={0};EndpointSuffix={1};AccountKey={2}', parameters('storageAccountName'), environment().suffixes.storage, listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-09-01').keys[0].value)]"
          },
          {
            "name": "FUNCTIONS_EXTENSION_VERSION",
            "value": "~4"
          },
          {
            "name": "FUNCTIONS_WORKER_RUNTIME",
            "value": "node"
          }
        ]
      }
    }
  }
]
```

### [Windows](#tab/windows/bicep)

```bicep
resource functionApp 'Microsoft.Web/sites@2022-03-01' = {
  name: functionAppName
  location: location
  kind: 'functionapp'
  properties: {
    serverFarmId: hostingPlan.id
    siteConfig: {
      appSettings: [
        {
          name: 'APPINSIGHTS_INSTRUMENTATIONKEY'
          value: applicationInsights.properties.InstrumentationKey
        }
        {
          name: 'AzureWebJobsStorage'
          value: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};EndpointSuffix=${environment().suffixes.storage};AccountKey=${storageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'WEBSITE_CONTENTAZUREFILECONNECTIONSTRING'
          value: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};EndpointSuffix=${environment().suffixes.storage};AccountKey=${storageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'WEBSITE_CONTENTSHARE'
          value: toLower(functionAppName)
        }
        {
          name: 'FUNCTIONS_EXTENSION_VERSION'
          value: '~4'
        }
        {
          name: 'FUNCTIONS_WORKER_RUNTIME'
          value: 'node'
        }
        {
          name: 'WEBSITE_NODE_DEFAULT_VERSION'
          value: '~14'
        }
      ]
    }
  }
}
```

### [Windows](#tab/windows/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/sites",
    "apiVersion": "2022-03-01",
    "name": "[parameters('functionAppName')]",
    "location": "[parameters('location')]",
    "kind": "functionapp",
    "dependsOn": [
      "[resourceId('Microsoft.Insights/components', parameters('applicationInsightsName'))]",
      "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
    ],
    "properties": {
      "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "siteConfig": {
        "appSettings": [
          {
            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
            "value": "[reference(resourceId('Microsoft.Insights/components', parameters('applicationInsightsName')), '2020-02-02').InstrumentationKey]"
          },
          {
            "name": "AzureWebJobsStorage",
            "value": "[format('DefaultEndpointsProtocol=https;AccountName={0};EndpointSuffix={1};AccountKey={2}', parameters('storageAccountName'), environment().suffixes.storage, listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-09-01').keys[0].value)]"
          },
          {
            "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
            "value": "[format('DefaultEndpointsProtocol=https;AccountName={0};EndpointSuffix={1};AccountKey={2}', parameters('storageAccountName'), environment().suffixes.storage, listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-09-01').keys[0].value)]"
          },
          {
            "name": "WEBSITE_CONTENTSHARE",
            "value": "[toLower(parameters('functionAppName'))]"
          },
          {
            "name": "FUNCTIONS_EXTENSION_VERSION",
            "value": "~4"
          },
          {
            "name": "FUNCTIONS_WORKER_RUNTIME",
            "value": "node"
          },
          {
            "name": "WEBSITE_NODE_DEFAULT_VERSION",
            "value": "~14"
          }
        ]
      }
    }
  }
]
```


---
:::zone-end  
:::zone pivot="premium-plan"
For function app on a Premium plan, you'll need to set the `serverFarmId` property on the app so that it points to the resource ID of the plan. You should ensure that the function app has a `dependsOn` setting for the plan as well.

A Premium plan requires another settings in the site configuration: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) and [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare). This property configures the storage account where the function app code and configuration are stored, which are used for dynamic scale.

For a sample Bicep file/Azure Resource Manager template, see [Azure Function App Hosted on Premium Plan](https://github.com/Azure-Samples/function-app-arm-templates/tree/main/function-app-premium-plan).

The settings required by a function app running in Premium plan differ between Windows and Linux. You can choose your operating system at the [top of this article](#top).

# [Bicep](#tab/bicep)

```bicep
resource functionAppName_resource 'Microsoft.Web/sites@2022-03-01' = {
  name: functionAppName
  location: location
  kind: 'functionapp'
  properties: {
    serverFarmId: hostingPlanName.id
    siteConfig: {
      appSettings: [
        {
          name: 'APPINSIGHTS_INSTRUMENTATIONKEY'
          value: applicationInsightsName.properties.InstrumentationKey
        }
        {
          name: 'AzureWebJobsStorage'
          value: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};EndpointSuffix=${environment().suffixes.storage};AccountKey=${storageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'WEBSITE_CONTENTAZUREFILECONNECTIONSTRING'
          value: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};EndpointSuffix=${environment().suffixes.storage};AccountKey=${storageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'WEBSITE_CONTENTSHARE'
          value: toLower(functionAppName)
        }
        {
          name: 'FUNCTIONS_EXTENSION_VERSION'
          value: '~4'
        }
        {
          name: 'FUNCTIONS_WORKER_RUNTIME'
          value: 'node'
        }
        {
          name: 'WEBSITE_NODE_DEFAULT_VERSION'
          value: '~14'
        }
      ]
    }
  }
}
```

# [JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/sites",
    "apiVersion": "2022-03-01",
    "name": "[parameters('functionAppName')]",
    "location": "[parameters('location')]",
    "kind": "functionapp",
    "dependsOn": [
      "[resourceId('Microsoft.Insights/components', parameters('applicationInsightsName'))]",
      "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
    ],
    "properties": {
      "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "siteConfig": {
        "appSettings": [
          {
            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
            "value": "[reference(resourceId('Microsoft.Insights/components', parameters('applicationInsightsName')), '2020-02-02').InstrumentationKey]"
          },
          {
            "name": "AzureWebJobsStorage",
            "value": "[format('DefaultEndpointsProtocol=https;AccountName={0};EndpointSuffix={1};AccountKey={2}', parameters('storageAccountName'), environment().suffixes.storage, listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-09-01').keys[0].value)]"
          },
          {
            "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
            "value": "[format('DefaultEndpointsProtocol=https;AccountName={0};EndpointSuffix={1};AccountKey={2}', parameters('storageAccountName'), environment().suffixes.storage, listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-09-01').keys[0].value)]"
          },
          {
            "name": "WEBSITE_CONTENTSHARE",
            "value": "[toLower(parameters('functionAppName'))]"
          },
          {
            "name": "FUNCTIONS_EXTENSION_VERSION",
            "value": "~4"
          },
          {
            "name": "FUNCTIONS_WORKER_RUNTIME",
            "value": "node"
          },
          {
            "name": "WEBSITE_NODE_DEFAULT_VERSION",
            "value": "~14"
          }
        ]
      }
    }
  }
]
```

---

> [!IMPORTANT]
> You don't need to set the [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) setting because it's generated for you when the site is first created.


The function app must have set `"kind": "functionapp,linux"`, and it must have set property `"reserved": true`. Linux apps should also include a `linuxFxVersion` property under siteConfig. If you're just deploying code, the value for this property is determined by your desired runtime stack in the format of runtime|runtimeVersion. For example: `python|3.7`, `node|14` and `dotnet|3.1`.

# [Bicep](#tab/bicep)

```bicep
resource functionApp 'Microsoft.Web/sites@2021-02-01' = {
  name: functionAppName
  location: location
  kind: 'functionapp,linux'
  properties: {
    reserved: true
    serverFarmId: hostingPlan.id
    siteConfig: {
      linuxFxVersion: 'node|14'
      appSettings: [
        {
          name: 'APPINSIGHTS_INSTRUMENTATIONKEY'
          value: applicationInsightsName.properties.InstrumentationKey
        }
        {
          name: 'AzureWebJobsStorage'
          value: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};EndpointSuffix=${environment().suffixes.storage};AccountKey=${storageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'WEBSITE_CONTENTAZUREFILECONNECTIONSTRING'
          value: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};EndpointSuffix=${environment().suffixes.storage};AccountKey=${storageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'WEBSITE_CONTENTSHARE'
          value: toLower(functionAppName)
        }
        {
          name: 'FUNCTIONS_EXTENSION_VERSION'
          value: '~4'
        }
        {
          name: 'FUNCTIONS_WORKER_RUNTIME'
          value: 'node'
        }
      ]
    }
  }
}
```

# [JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/sites",
    "apiVersion": "2021-02-01",
    "name": "[parameters('functionAppName')]",
    "location": "[parameters('location')]",
    "kind": "functionapp,linux",
    "dependsOn": [
      "[resourceId('Microsoft.Insights/components', parameters('applicationInsightsName'))]",
      "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
    ],
    "properties": {
      "reserved": true,
      "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "siteConfig": {
        "linuxFxVersion": "node|14",
        "appSettings": [
          {
            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
            "value": "[reference(resourceId('Microsoft.Insights/components', parameters('applicationInsightsName')), '2020-02-02').InstrumentationKey]"
          },
          {
            "name": "AzureWebJobsStorage",
            "value": "[format('DefaultEndpointsProtocol=https;AccountName={0};EndpointSuffix={1};AccountKey={2}', parameters('storageAccountName'), environment().suffixes.storage, listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-09-01').keys[0].value)]"
          },
          {
            "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
            "value": "[format('DefaultEndpointsProtocol=https;AccountName={0};EndpointSuffix={1};AccountKey={2}', parameters('storageAccountName'), environment().suffixes.storage, listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-09-01').keys[0].value)]"
          },
          {
            "name": "WEBSITE_CONTENTSHARE",
            "value": "[toLower(parameters('functionAppName'))]"
          },
          {
            "name": "FUNCTIONS_EXTENSION_VERSION",
            "value": "~4"
          },
          {
            "name": "FUNCTIONS_WORKER_RUNTIME",
            "value": "node"
          }
        ]
      }
    }
  }
]
```

---

:::zone-end
:::zone pivot="dedicated-plan"
When creating a function app on a Dedicated (App Service) plan, set the `serverFarmId` property on the app so that it points to the resource ID of the plan and make sure that the function app has a `dependsOn` setting that also references the plan.

The settings required by a function app running in Dedicated plan differ between Windows and Linux. 

The function app must have set `"kind": "functionapp,linux"`, and it must have set property `"reserved": true`. Linux apps should also include a `linuxFxVersion` property under `siteConfig`. If you're just deploying code, the value for this property is determined by your desired runtime stack in the format of runtime|runtimeVersion. Examples of `linuxFxVersion` property include:  `python|3.7`, `node|14` and `dotnet|3.1`.

# [Bicep](#tab/bicep)

```bicep
resource functionApp 'Microsoft.Web/sites@2022-03-01' = {
  name: functionAppName
  location: location
  kind: 'functionapp,linux'
  properties: {
    reserved: true
    serverFarmId: hostingPlan.id
    siteConfig: {
      alwaysOn: true
      linuxFxVersion: 'node|14'
      appSettings: [
        {
          name: 'APPINSIGHTS_INSTRUMENTATIONKEY'
          value: applicationInsightsName.properties.InstrumentationKey
        }
        {
          name: 'AzureWebJobsStorage'
          value: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};EndpointSuffix=${environment().suffixes.storage};AccountKey=${storageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'FUNCTIONS_EXTENSION_VERSION'
          value: '~4'
        }
        {
          name: 'FUNCTIONS_WORKER_RUNTIME'
          value: 'node'
        }
      ]
    }
  }
}
```

# [JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/sites",
    "apiVersion": "2022-03-01",
    "name": "[parameters('functionAppName')]",
    "location": "[parameters('location')]",
    "kind": "functionapp,linux",
    "dependsOn": [
      "[resourceId('Microsoft.Insights/components', parameters('applicationInsightsName'))]",
      "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
    ],
    "properties": {
      "reserved": true,
      "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "siteConfig": {
        "alwaysOn": true,
        "linuxFxVersion": "node|14",
        "appSettings": [
          {
            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
            "value": "[reference(resourceId('Microsoft.Insights/components', parameters('applicationInsightsName')), '2020-02-02').InstrumentationKey]"
          },
          {
            "name": "AzureWebJobsStorage",
            "value": "[format('DefaultEndpointsProtocol=https;AccountName={0};EndpointSuffix={1};AccountKey={2}', parameters('storageAccountName'), environment().suffixes.storage, listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-09-01').keys[0].value)]"
          },
          {
            "name": "FUNCTIONS_EXTENSION_VERSION",
            "value": "~4"
          },
          {
            "name": "FUNCTIONS_WORKER_RUNTIME",
            "value": "node"
          }
        ]
      }
    }
  }
]
```

---

# [Bicep](#tab/bicep)

```bicep
resource functionApp 'Microsoft.Web/sites@2022-03-01' = {
  name: functionAppName
  location: location
  kind: 'functionapp'
  properties: {
    serverFarmId: hostingPlan.id
    siteConfig: {
      alwaysOn: true
      appSettings: [
        {
          name: 'APPINSIGHTS_INSTRUMENTATIONKEY'
          value: applicationInsightsName.properties.InstrumentationKey
        }
        {
          name: 'AzureWebJobsStorage'
          value: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};EndpointSuffix=${environment().suffixes.storage};AccountKey=${storageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'FUNCTIONS_EXTENSION_VERSION'
          value: '~4'
        }
        {
          name: 'FUNCTIONS_WORKER_RUNTIME'
          value: 'node'
        }
        {
          name: 'WEBSITE_NODE_DEFAULT_VERSION'
          value: '~14'
        }
      ]
    }
  }
}
```

# [JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/sites",
    "apiVersion": "2022-03-01",
    "name": "[parameters('functionAppName')]",
    "location": "[parameters('location')]",
    "kind": "functionapp",
    "dependsOn": [
      "[resourceId('Microsoft.Insights/components', parameters('applicationInsightsName'))]",
      "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
    ],
    "properties": {
      "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
          {
            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
            "value": "[reference(resourceId('Microsoft.Insights/components', parameters('applicationInsightsName')), '2020-02-02').InstrumentationKey]"
          },
          {
            "name": "AzureWebJobsStorage",
            "value": "[format('DefaultEndpointsProtocol=https;AccountName={0};EndpointSuffix={1};AccountKey={2}', parameters('storageAccountName'), environment().suffixes.storage, listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-09-01').keys[0].value)]"
          },
          {
            "name": "FUNCTIONS_EXTENSION_VERSION",
            "value": "~4"
          },
          {
            "name": "FUNCTIONS_WORKER_RUNTIME",
            "value": "node"
          },
          {
            "name": "WEBSITE_NODE_DEFAULT_VERSION",
            "value": "~14"
          }
        ]
      }
    }
  }
]
```

---

When creating a function app in a Dedicated plan, keep the following considerations in mind:

+ Enable the `"alwaysOn": true` setting under site config so that your function app runs correctly. On an App Service plan, the functions runtime goes idle after a few minutes of inactivity, so only HTTP triggers will "wake up" your functions. Without this setting your non-HTTP trigger functions, including Timer trigger, won't run correctly.
+ Don't include [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) and [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) settings. These dynamic scale settings aren't supported on a Dedicated plan.

:::zone-end
:::zone pivot="dedicated-plan,premium-plan"
## Containerized function apps

If you're [deploying containerized function app](./functions-how-to-custom-container.md) to an Azure Functions Premium or Dedicated plan, you must set the [`linuxFxVersion`](functions-app-settings.md#linuxfxversion) site setting with the identifier of your container image. You also need to set these application settings:

| Setting | Value | Description |
|----|----|----|
| [`DOCKER_REGISTRY_SERVER_URL`](../app-service/reference-app-settings.md#custom-containers) | Container service URL | Use with a private container registry service, like Azure Container Registry. |
| [`DOCKER_REGISTRY_SERVER_USERNAME](../app-service/reference-app-settings.md#custom-containers) | username | Username of the private container registry service account. |
| [`DOCKER_REGISTRY_SERVER_PASSWORD`](../app-service/reference-app-settings.md#custom-containers) | password | Password for the private container registry service account. |
 [`WEBSITES_ENABLE_APP_SERVICE_STORAGE`](../app-service/reference-app-settings.md#custom-containers) | `false` | Don't use the root since your app content is provided in the container itself. |

#### [Bicep](#tab/bicep)

```bicep
resource functionApp 'Microsoft.Web/sites@2022-03-01' = {
  name: functionAppName
  location: location
  kind: 'functionapp'
  properties: {
    serverFarmId: hostingPlan.id
    siteConfig: {
      appSettings: [
        {
          name: 'AzureWebJobsStorage'
          value: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};AccountKey=${storageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'FUNCTIONS_WORKER_RUNTIME'
          value: 'node'
        }
        {
          name: 'WEBSITE_NODE_DEFAULT_VERSION'
          value: '~14'
        }
        {
          name: 'FUNCTIONS_EXTENSION_VERSION'
          value: '~4'
        }
        {
          name: 'DOCKER_REGISTRY_SERVER_URL'
          value: dockerRegistryUrl
        }
        {
          name: 'DOCKER_REGISTRY_SERVER_USERNAME'
          value: dockerRegistryUsername
        }
        {
          name: 'DOCKER_REGISTRY_SERVER_PASSWORD'
          value: dockerRegistryPassword
        }
        {
          name: 'WEBSITES_ENABLE_APP_SERVICE_STORAGE'
          value: 'false'
        }
      ]
      linuxFxVersion: 'DOCKER|myacr.azurecr.io/myimage:mytag'
    }
  }
  dependsOn: [
    storageAccount
  ]
}
```

#### [JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/sites",
    "apiVersion": "2022-03-01",
    "name": "[parameters('functionAppName')]",
    "location": "[parameters('location')]",
    "kind": "functionapp",
    "dependsOn": [
      "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
    ],
    "properties": {
      "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "siteConfig": {
        "appSettings": [
          {
            "name": "AzureWebJobsStorage",
            "value": "[format('DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}', parameters('storageAccountName'), listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-09-01').keys[0].value)]"
          },
          {
            "name": "FUNCTIONS_WORKER_RUNTIME",
            "value": "node"
          },
          {
            "name": "WEBSITE_NODE_DEFAULT_VERSION",
            "value": "~14"
          },
          {
            "name": "FUNCTIONS_EXTENSION_VERSION",
            "value": "~4"
          },
          {
            "name": "DOCKER_REGISTRY_SERVER_URL",
            "value": "[parameters('dockerRegistryUrl')]"
          },
          {
            "name": "DOCKER_REGISTRY_SERVER_USERNAME",
            "value": "[parameters('dockerRegistryUsername')]"
          },
          {
            "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
            "value": "[parameters('dockerRegistryPassword')]"
          },
          {
            "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
            "value": "false"
          }
        ],
        "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
      }
    }
  }
]
```

---

::: zone-end
:::zone pivot="azure-arc"
When deploying functions to Azure Arc, the value you set for the `kind` field of the function app resource depends on the type of deployment:

| Deployment type | `kind` field value |
|----|----|
| Code-only deployment | `functionapp,linux,kubernetes` |
| Container deployment | `functionapp,linux,kubernetes,container` |  

You must also set the `customLocationId` as you did for the [hosting plan resource](#hosting-plan).

The definition of a containerized .NET 6.0 function app might look like this example:

# [Bicep](#tab/bicep)

```bicep
resource functionApp 'Microsoft.Web/sites@2022-03-01' = {
  name: functionAppName
  kind: 'kubernetes,functionapp,linux,container'
  location: location
  extendedLocation: {
    name: customLocationId
  }
  properties: {
    serverFarmId: hostingPlanName
    siteConfig: {
      linuxFxVersion: 'DOCKER|mcr.microsoft.com/azure-functions/dotnet:3.0-appservice-quickstart'
      appSettings: [
        {
          name: 'FUNCTIONS_EXTENSION_VERSION'
          value: '~4'
        }
        {
          name: 'AzureWebJobsStorage'
          value: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};AccountKey=${storageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'APPINSIGHTS_INSTRUMENTATIONKEY'
          value: applicationInsightsName.properties.InstrumentationKey
        }
      ]
      alwaysOn: true
    }
  }
  dependsOn: [
    storageAccount
    hostingPlan
  ]
}
```

# [JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Web/sites",
    "apiVersion": "2022-03-01",
    "name": "[parameters('functionAppName')]",
    "kind": "kubernetes,functionapp,linux,container",
    "location": "[parameters('location')]",
    "extendedLocation": {
      "name": "[parameters('customLocationId')]"
    },
    "dependsOn": [
      "[resourceId('Microsoft.Insights/components', parameters('applicationInsightsName'))]",
      "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
      "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
    ],
    "properties": {
      "serverFarmId": "[parameters('hostingPlanName')]",
      "siteConfig": {
        "linuxFxVersion": "DOCKER|mcr.microsoft.com/azure-functions/dotnet:3.0-appservice-quickstart",
        "appSettings": [
          {
            "name": "FUNCTIONS_EXTENSION_VERSION",
            "value": "~4"
          },
          {
            "name": "AzureWebJobsStorage",
            "value": "[format('DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}', parameters('storageAccountName'), listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-09-01').keys[0].value)]"
          },
          {
            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
            "value": "[reference(resourceId('Microsoft.Insights/components', parameters('applicationInsightsName')), '2020-02-02').InstrumentationKey]"
          }
        ],
        "alwaysOn": true
      }
    }
  }
]
```

---

::: zone-end

## Customize deployments

A function app has many child resources that you can use in your deployment, including app settings and source control options. You also might choose to remove the **sourcecontrols** child resource, and use a different [deployment option](functions-continuous-deployment.md) instead.


Considerations for custom deployments: 

+ To successfully deploy your application by using Azure Resource Manager, it's important to understand how resources are deployed in Azure. In the following example, top-level configurations are applied by using `siteConfig`. It's important to set these configurations at a top level, because they convey information to the Functions runtime and deployment engine. Top-level information is required before the child **sourcecontrols/web** resource is applied. Although it's possible to configure these settings in the child-level **config/appSettings** resource, in some cases your function app must be deployed *before* **config/appSettings** is applied. For example, when you're using functions with [Logic Apps](.logic-apps/index.yml), your functions are a dependency of another resource.

    # [Bicep](#tab/bicep)
    
    ```bicep
    resource functionApp 'Microsoft.Web/sites@2022-03-01' = {
      name: functionAppName
      location: location
      kind: 'functionapp'
      properties: {
        serverFarmId: hostingPlan.id
        siteConfig: {
          alwaysOn: true
          appSettings: [
            {
              name: 'FUNCTIONS_EXTENSION_VERSION'
              value: '~4'
            }
            {
              name: 'Project'
              value: 'src'
            }
          ]
        }
      }
      dependsOn: [
        storageAccount
      ]
    }
    
    resource config 'Microsoft.Web/sites/config@2022-03-01' = {
      parent: functionApp
      name: 'appsettings'
      properties: {
        AzureWebJobsStorage: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};AccountKey=${storageAccount.listKeys().keys[0].value}'
        AzureWebJobsDashboard: 'DefaultEndpointsProtocol=https;AccountName=${storageAccountName};AccountKey=${storageAccount.listKeys().keys[0].value}'
        FUNCTIONS_EXTENSION_VERSION: '~4'
        FUNCTIONS_WORKER_RUNTIME: 'dotnet'
        Project: 'src'
      }
      dependsOn: [
        sourcecontrol
        storageAccount
      ]
    }
    
    resource sourcecontrol 'Microsoft.Web/sites/sourcecontrols@2022-03-01' = {
      parent: functionApp
      name: 'web'
      properties: {
        repoUrl: repoUrl
        branch: branch
        isManualIntegration: true
      }
    }
    ```
    
    # [JSON](#tab/json)
    
    ```json
    "resources": [
      {
        "type": "Microsoft.Web/sites",
        "apiVersion": "2022-03-01",
        "name": "[variables('functionAppName')]",
        "location": "[parameters('location')]",
        "kind": "functionapp",
        "properties": {
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
          "siteConfig": {
            "alwaysOn": true,
            "appSettings": [
              {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~4"
              },
              {
                "name": "Project",
                "value": "src"
              }
            ]
          }
        },
        "dependsOn": [
          "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ]
      },
      {
        "type": "Microsoft.Web/sites/config",
        "apiVersion": "2022-03-01",
        "name": "[format('{0}/{1}', variables('functionAppName'), 'appsettings')]",
        "properties": {
          "AzureWebJobsStorage": "[format('DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}', variables('storageAccountName'), listKeys(variables('storageAccountName'), '2021-09-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[format('DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}', variables('storageAccountName'), listKeys(variables('storageAccountName'), '2021-09-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~4",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        },
        "dependsOn": [
          "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
          "[resourceId('Microsoft.Web/sites/sourcecontrols', variables('functionAppName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ]
      },
      {
        "type": "Microsoft.Web/sites/sourcecontrols",
        "apiVersion": "2022-03-01",
        "name": "[format('{0}/{1}', variables('functionAppName'), 'web')]",
        "properties": {
          "repoUrl": "[parameters('repoURL')]",
          "branch": "[parameters('branch')]",
          "isManualIntegration": true
        },
        "dependsOn": [
          "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
        ]
      }
    ]
    ```
    
    ---

+ The previous Bicep file and ARM template use the [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) application settings value, which sets the base directory in which the Functions deployment engine (Kudu) looks for deployable code. In our repository, our functions are in a subfolder of the **src** folder. So, in the preceding example, we set the app settings value to `src`. If your functions are in the root of your repository, or if you're not deploying from source control, you can remove this app settings value.

+ When updating application settings using Bicep or ARM, make sure that you include all existing settings. You must do this because the underlying REST APIs calls replace the existing application settings when the update APIs are called. 

## Validate your template

When you manually create your deployment template file, it's important to validate your template before deployment. All deployment methods validate your template syntax and raise a `validation failed` error message as shown in the following JSON formatted example:

```json
{"error":{"code":"InvalidTemplate","message":"Deployment template validation failed: 'The resource 'Microsoft.Web/sites/func-xyz' is not defined in the template. Please see https://aka.ms/arm-template for usage details.'.","additionalInfo":[{"type":"TemplateViolation","info":{"lineNumber":0,"linePosition":0,"path":""}}]}}
```

The following methods can be used to validate your template before deployment:

### [Azure Pipelines](#tab/devops)

Tbe following [Azure resource group deployment v2 task](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops) with `deploymentMode: 'Validation'` instructs Azure Pipelines to validate the template. 

```yml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    subscriptionId: # Required subscription ID
    action: 'Create Or Update Resource Group'
    resourceGroupName: # Required resource group name
    location: # Required when action == Create Or Update Resource Group
    templateLocation: 'Linked artifact'
    csmFile: # Required when  TemplateLocation == Linked Artifact
    csmParametersFile: # Optional
    deploymentMode: 'Validation'
```

### [Azure CLI](#tab/azure-cli)

You can use the [`az deployment group validate`](/cli/azure/deployment/group#az-deployment-group-validate) command to validate your template, as shown in the following example:

```azurecli-interactive
az deployment group validate --resource-group <resource-group-name> --template-file <template-file-location> --parameters functionAppName='<function-app-name>' packageUri='<zip-package-location>'
```

### [Visual Studio Code](#tab/vs-code)

On [Visual Studio Code](https://code.visualstudio.com/), install the latest [Azure Resource Manager Tools extension](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).

This extension reports syntactic errors in your templates before you try to deploy them. For some examples of errors, see the [Fix validation error](../azure-resource-manager/troubleshooting/quickstart-troubleshoot-arm-deployment.md#fix-validation-error) section of the troubleshooting article.

---

You can also create a test resource group to find [preflight](../azure-resource-manager/troubleshooting/quickstart-troubleshoot-arm-deployment.md?tabs=azure-cli#fix-preflight-error) and [deployment](../azure-resource-manager/troubleshooting/quickstart-troubleshoot-arm-deployment.md?tabs=azure-cli#fix-deployment-error) errors.

## Deploy your template

You can use any of the following ways to deploy your Bicep file and template:

### [Bicep](#tab/bicep)

- [Azure CLI](../azure-resource-manager/bicep/deploy-cli.md)
- [PowerShell](../azure-resource-manager/bicep/deploy-powershell.md)

### [JSON](#tab/json)

- [Azure portal](../azure-resource-manager/templates/deploy-portal.md)
- [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

---

### Deploy to Azure button

> [!NOTE]
> This method doesn't support deploying Bicep files currently.

Replace ```<url-encoded-path-to-azuredeploy-json>``` with a [URL-encoded](https://www.bing.com/search?q=url+encode) version of the raw path of your `azuredeploy.json` file in GitHub.

Here's an example that uses markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Here's an example that uses HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### Deploy using PowerShell

The following PowerShell commands create a resource group and deploy a Bicep file/ARM template that creates a function app with its required resources. To run locally, you must have [Azure PowerShell](/powershell/azure/install-azure-powershell) installed. Run [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) to sign in.

#### [Bicep](#tab/bicep)

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile main.bicep  -Verbose
```

#### [JSON](#tab/json)

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile azuredeploy.json  -Verbose
```

---

To test out this deployment, you can use a [template like this one](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.web/function-app-create-dynamic) that creates a function app on Windows in a Consumption plan.

## Next steps

Learn more about how to develop and configure Azure Functions.

- [Azure Functions developer reference](functions-reference.md)
- [How to configure Azure function app settings](functions-how-to-use-azure-function-app-settings.md)
- [Create your first Azure function](functions-get-started.md)

<!-- LINKS -->

[Function app on Consumption plan]: https://azure.microsoft.com/resources/templates/function-app-create-dynamic/
[Function app on Azure App Service plan]: https://azure.microsoft.com/resources/templates/function-app-create-dedicated/
