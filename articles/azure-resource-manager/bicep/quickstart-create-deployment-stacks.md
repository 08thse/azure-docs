---
title: Create and deploy a deployment stack with Bicep
description: Learn how to use Bicep to create and deploy a deployment stack in your Azure subscription.
ms.date: 04/28/2023
ms.topic: quickstart
ms.custom: mode-api, devx-track-azurecli, devx-track-azurepowershell, devx-track-bicep
# Customer intent: As a developer I want to use Bicep to create a deployment stack.
---

# Quickstart: Create and deploy a deployment stack with Bicep

This quickstart describes how to create a [deployment stack](deployment-stacks.md).

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell [version xxx or later](/powershell/azure/install-az-ps) or Azure CLI [version xxx or later](/cli/azure/install-azure-cli).
- [Visual Studio Code](https://code.visualstudio.com/) with the [Bicep extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep).

## Create Bicep files

Create two Bicep files to create two resource groups with one public IP address within each resource group. By deploying these Bicep files, you'll create two resource groups (ds-rg1 and ds-rg2) with one public IP address resource (publicIP1 and publicIP2, respectively) in each respective group.

The main.bicep file looks like:

```bicep
targetScope = 'subscription'

param resourceGroupName1 string = 'ds-rg1'
param resourceGroupName2 string = 'ds-rg2'
param resourceGroupLocation string = deployment().location

resource testrg1 'Microsoft.Resources/resourceGroups@2022-09-01' = {
  name: resourceGroupName1
  location: resourceGroupLocation
}

resource testrg2 'Microsoft.Resources/resourceGroups@2022-09-01' = {
  name: resourceGroupName2
  location: resourceGroupLocation
}

module firstPIP './pip.bicep' = if (resourceGroupName1 == 'ds-rg1') {
  name: 'publicIP1'
  scope: testrg1
  params: {
    location: resourceGroupLocation
    allocationMethod: 'Dynamic'
    skuName: 'Basic'
  }
}

module secondPIP './pip.bicep' = if (resourceGroupName2 == 'ds-rg2') {
  name: 'publicIP2'
  scope: testrg2
  params: {
    location: resourceGroupLocation
    allocationMethod: 'Static'
    skuName: 'Basic'
  }
}
```

The pip.bicep file looks like:

```bicep
param location string = resourceGroup().location
param allocationMethod string = 'Dynamic'
param skuName string

resource publicIP1 'Microsoft.Network/publicIPAddresses@2022-09-01' = if (allocationMethod == 'Dynamic') {
  name:  'pubIP1'
  location: location
  sku: {
    name:  'Basic'
    tier:  'Regional'
  }
  properties: {
    publicIPAllocationMethod: allocationMethod
  }
}

resource publicIP2 'Microsoft.Network/publicIPAddresses@2022-09-01' = if (allocationMethod == 'Static') {
  name:  'pubIP2'
  location: location
  sku: {
    name:  skuName
    tier:  'Regional'
  }
  properties: {
    publicIPAllocationMethod: allocationMethod
  }
}
```

Save both bicep file in the same folder.

## Create a deployment stack

In this quickstart, you'll create the deployment stack at the subscription scope.  You can also create the deployment stack at the resource group scope or the management group scope.  For more information, see [Create deployment stacks](./deployment-stacks.md#create-deployment-stack).

# [PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzSubscriptionDeploymentStack `
   -Name 'mySubStack' `
   -Location 'eastus' `
   -TemplateFile './main.bicep'
```

# [CLI](#tab/azure-cli)

```azurecli
az stack sub create \
  --name mySubStack \
  --location eastus \
  --template-file ./main.bicep
```

---

## Verify the deployment

To verify the deployment:

# [PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzSubscriptionDeploymentStack
```

The output shows four managed resource - two resource groups and two public IPs:

```output
Id                          : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Resources/deploymentStacks/mySubStack
Name                        : mySubStack
ProvisioningState           : succeeded
ResourcesCleanupAction      : detach
ResourceGroupsCleanupAction : detach
DenySettingsMode            : none
Location                    : eastus
CreationTime(UTC)           : 4/20/2023 7:53:02 PM
DeploymentId                : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Resources/deployments/mySubStack-2023-04-20-19-53-02-fdd96
Resources                   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ds-rg1
                              /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ds-rg1/providers/Microsoft.Network/publicIPAddresses/pubIP1
                              /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ds-rg2
                              /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ds-rg2/providers/Microsoft.Network/publicIPAddresses/pubIP1
```

# [CLI](#tab/azure-cli)

```azurecli
az stack sub list
```

The output shows four managed resource - two resource groups and two public IPs:

```output
[
  {
    "actionOnUnmanage": {
      "managementGroups": "detach",
      "resourceGroups": "detach",
      "resources": "detach"
    },
    "debugSetting": null,
    "deletedResources": [],
    "denySettings": {
      "applyToChildScopes": false,
      "excludedActions": null,
      "excludedPrincipals": null,
      "mode": "none"
    },
    "deploymentId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Resources/deployments/mySubStack-2023-04-20-19-53-02-fdd96",
    "deploymentScope": null,
    "description": null,
    "detachedResources": [],
    "duration": "PT21.2281952S",
    "error": null,
    "failedResources": [],
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Resources/deploymentStacks/mySubStack",
    "location": "eastus",
    "name": "mySubStack",
    "outputs": null,
    "parameters": {},
    "parametersLink": null,
    "provisioningState": "succeeded",
    "resources": [
      {
        "denyStatus": "none",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ds-rg1",
        "status": "managed"
      },
      {
        "denyStatus": "none",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ds-rg1/providers/Microsoft.Network/publicIPAddresses/pubIP1",
        "resourceGroup": "ds-rg1",
        "status": "managed"
      },
      {
        "denyStatus": "none",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ds-rg2",
        "status": "managed"
      },
      {
        "denyStatus": "none",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ds-rg2/providers/Microsoft.Network/publicIPAddresses/pubIP1",
        "resourceGroup": "ds-rg2",
        "status": "managed"
      }
    ],
    "systemData": {
      "createdAt": "2023-04-20T19:53:02.279809+00:00",
      "createdBy": "someone@microsoft.com",
      "createdByType": "User",
      "lastModifiedAt": "2023-04-20T19:53:02.279809+00:00",
      "lastModifiedBy": "someone@microsoft.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "template": null,
    "templateLink": null,
    "type": "Microsoft.Resources/deploymentStacks"
  }
]
```

---

## Delete the deployment stack

To delete the deployment stack, and the managed resources:

```azurepowershell
Remove-AzSubscriptionDeploymentStack `
  -Name mySubStack `
  -DeleteAll
```

```azurecli
az stack sub delete \
  --name mySubStack \
  --delete-all
```

If you run the delete commands without the **delete all** parameters, the managed resources will be detached but not deleted. To delete the managed resources, use the following switches:

- `DeleteAll`: delete both resource groups and the managed resources.
- `DeleteResources`: delete the managed resources only.
- `DeleteResourceGroups`: delete the resource groups only.

For more information, see [Delete deployment stacks](./deployment-stacks.md#delete-deployment-stacks).

## Next steps

> [!div class="nextstepaction"]
> [Deployment stacks](./deployment-stacks.md.md)
