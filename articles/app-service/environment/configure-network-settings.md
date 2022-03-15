---
title: Configure App Service Environment v3 network settings
description: Configure network settings that apply to the entire Azure App Service environment. Learn how to do it with Azure Resource Manager templates.
author: madsd

ms.topic: tutorial
ms.date: 03/20/2022
ms.author: madsd
---

# Network configuration settings for App Service Environments v3

Because App Service Environments are isolated to a single customer, there are certain configuration settings that can be applied exclusively to App Service Environments. This article documents the various specific network customizations that are available for App Service Environment v3.

> [!NOTE]
> This article is about App Service Environment v3, which is used with isolated v2 App Service plans.

If you do not have an App Service Environment, see [How to Create an App Service Environment v3](./creation.md).

App Service Environment network customizations are stored in a sub-resource of the *hostingEnvironments* Azure Resource Manager entity called networking.

The following abbreviated Resource Manager template snippet shows the **networking** resource:

```json
"resources": [
{
    "apiVersion": "2021-03-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": "[parameter('aseName')]"
    "location": ...,
    "properties": {
        "internalLoadBalancingMode": ...,
        etc...
    },    
    "resources": [
        {
            "type": "configurations",
            "apiVersion": "2021-03-01",
            "name": "networking",
            "dependsOn": [
                "[resourceId('Microsoft.Web/hostingEnvironments', parameters('aseName'))]"
            ],
            "properties": {
                "remoteDebugEnabled": true,
                "ftpEnabled": true,
                "allowNewPrivateEndpointConnections": true
            }
        }
    ]
}
```

The **networking** resource can be included in a Resource Manager template to update the App Service Environment.

## Use Azure Resource Explorer to update an App Service Environment
Alternatively, you can update the App Service Environment by using [Azure Resource Explorer](https://resources.azure.com).  

1. In Resource Explorer, go to the node for the App Service Environment (**subscriptions** > **{your Subscription}** > **resourceGroups** > **{your Resource Group}** > **providers** > **Microsoft.Web** > **hostingEnvironments** > **App Service Environment name** > **configurations** > **networking**).
2. Click **Read/Write** in the upper toolbar to allow interactive editing in Resource Explorer.  
3. Click the blue **Edit** button to make the Resource Manager template editable.
4. Modify one or more of the settings ftpEnabled, remoteDebugEnabled, allowNewPrivateEndpointConnnections, that you want to change.  
5. Click the green **PUT** button that's located at the top of the right pane to commit the change to the App Service Environment.
6. You many need to click the green **GET** button again to see the changed values.

The change takes effect within a minute.

## Allow new private endpoint connnections

For apps hosted on both ILB and External App Service Environment you can allow creation of private endpoints. The setting is default disabled. If private endpoint were created while the setting was enabled, they will not be deleted and will continue to work. The setting only prevents new private endpoints from being created.

The following Azure cli command will enable allowNewPrivateEndpointConnections:

```azurecli
ASE_NAME="[myAseName]"
RESOURCE_GROUP_NAME="[myResourceGroup]"
az resource update --name $ASE_NAME/configurations/networking --set properties.allowNewPrivateEndpointConnections=true -g $RESOURCE_GROUP_NAME --resource-type "Microsoft.Web/hostingEnvironments/networkingConfiguration"

az resource show --name $ASE_NAME/configurations/networking -g $RESOURCE_GROUP_NAME --resource-type "Microsoft.Web/hostingEnvironments/networkingConfiguration" --query properties.allowNewPrivateEndpointConnections
```

## FTP access

This ftpEnabled setting allow you to allow or deny FTP connetions are the App Service Environment level. Individual apps will still need to configure FTP access. If you enable FTP at the App Service Environment level, you may want to [enforce FTPS](../deploy-ftp.md?tabs=cli#enforce-ftps) at the individual app level. The setting is default disabled.

If you want to enable FTP access, you can run the following Azure cli command:

```azurecli
ASE_NAME="[myAseName]"
RESOURCE_GROUP_NAME="[myResourceGroup]"
az resource update --name $ASE_NAME/configurations/networking --set properties.ftpEnabled=true -g $RESOURCE_GROUP_NAME --resource-type "Microsoft.Web/hostingEnvironments/networkingConfiguration"

az resource show --name $ASE_NAME/configurations/networking -g $RESOURCE_GROUP_NAME --resource-type "Microsoft.Web/hostingEnvironments/networkingConfiguration" --query properties.ftpEnabled
```

## Remote debugging access

Remote debugging is default disabled at the App Service Environment level. You can enable network level access for all apps using this configuration. You will still have to [configure remote debugging](../configure-common.md?tabs=cli#configure-general-settings) at the individual app level.

Run the following Azure cli command to enable remote debugging access:

```azurecli
ASE_NAME="[myAseName]"
RESOURCE_GROUP_NAME="[myResourceGroup]"
az resource update --name $ASE_NAME/configurations/networking --set properties.RemoteDebugEnabled=true -g $RESOURCE_GROUP_NAME --resource-type "Microsoft.Web/hostingEnvironments/networkingConfiguration"

az resource show --name $ASE_NAME/configurations/networking -g $RESOURCE_GROUP_NAME --resource-type "Microsoft.Web/hostingEnvironments/networkingConfiguration" --query properties.remoteDebugEnabled
```

## Get started

The Azure Quickstart Resource Manager template site includes a template with the base definition for [creating an App Service Environment](https://azure.microsoft.com/resources/templates/web-app-asp-app-on-asev3-create/).
