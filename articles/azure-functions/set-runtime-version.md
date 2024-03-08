---
title: How to target Azure Functions runtime versions
description: Azure Functions supports multiple versions of the runtime. Learn how to specify the runtime version of a function app hosted in Azure.
ms.topic: conceptual
ms.custom:
  - ignite-2023
ms.date: 01/25/2024
zone_pivot_groups: app-service-platform-windows-linux
---

# How to target Azure Functions runtime versions

A function app runs on a specific version of the Azure Functions runtime. By default, function apps are created in latest 4.x version of the Functions runtime. Apps are only supported when running on a [supported major version](functions-versions.md). This article explains how to configure a function app in Azure to run on a specific version you choose. 
::: zone pivot="platform-windows" 
The way that you manually target a specific version depends on whether you're running Windows or Linux. This version of the article targets Windows. Choose your operating system at the top of the article.
::: zone-end
::: zone pivot="platform-linux" 
The way that you manually target a specific version depends on whether you're running Windows or Linux. This version of the article targets Linux. Choose your operating system at the top of the article.
::: zone-end
During local development, your installed version of Azure Functions Core Tools must match major runtime version targeted in Azure. For more information, see [Core TOols versions](functions-run-local.md#v2). 
::: zone pivot="platform-windows" 
## Automatic and manual version updates

When running on Windows, Azure Functions lets you use the `FUNCTIONS_EXTENSION_VERSION` application setting to target the runtime version used by a given function app. When you specify only the major version (`~4`), the function app is automatically updated to new minor versions of the runtime when they become available. Minor version updates are done automatically because new minor versions shouldn't introduce breaking changes. 

When you specify a specific minor version (such as `4.0.12345`), the function app is pinned to that specific version of the runtime until you explicitly choose to move to a new version. You should only pin to a specific minor version long enough to resolve any issues with your function app that prevent you from targeting the major version. Older minor versions are regularly removed from the production environment. When you are pinned to a minor version that gets removed, your function app goes back to running on the major version instead of the version set in `FUNCTIONS_EXTENSION_VERSION`. Minor version removals are announced in [App Service announcements](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> When you try to publish from Visual Studio to an app that is pinned to a specific minor version of the runtime, a dialog prompts you to update to the latest version or cancel the publish. To avoid this check when you must use a specific minor version, add the `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` property in your `.csproj` file.

When a new major version is publicly available, a prompt in the portal gives you the chance to move up to that version. After moving to a new version, you can always move back to a previous supported version.

The following table shows the `FUNCTIONS_EXTENSION_VERSION` values for each supported major version that enable automatic updates:

| Major version<sup>2</sup>  | `FUNCTIONS_EXTENSION_VERSION` value | Additional configuration  |
| -------------  | ----------------------------------- | ------------------------- |
| 4.x            | `~4`                                | [On Windows, enable .NET 6](./migrate-version-3-version-4.md#upgrade-your-function-app-in-azure)<sup>1</sup> |
| 1.x<sup>3</sup>| `~1`                                |                           |

<sup>1</sup> If using a later version with the .NET Isolated worker model, instead enable that version.  
<sup>2</sup>For a detailed support statement about previous version that have reached end-of-support (not shown in this table), see [Retired versions](functions-versions.md#retired-versions).  
<sup>3</sup>[Support for version 1.x of the Azure Functions runtime ends on September 14, 2026](https://aka.ms/azure-functions-retirements/hostv1). Before that date, [migrate your version 1.x apps to version 4.x](./migrate-version-1-version-4.md) to maintain full support.  

A change to the runtime version causes your function app to restart. 
::: zone-end

## View the current runtime version

You can view the current runtime version of your function app in one of these ways:

### [Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]


### [Azure CLI](#tab/azurecli)

You can view the `FUNCTIONS_EXTENSION_VERSION` from the Azure CLI.  

Using the Azure CLI, view the current runtime version with the [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings) command.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

In this code, replace `<function_app>` with the name of your function app. Also replace `<my_resource_group>` with the name of the resource group for your function app. 

You see the `FUNCTIONS_EXTENSION_VERSION` in the following output, which has been truncated for clarity:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~4"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
]
```

Choose **Try it** in the previous code example to run the command in [Azure Cloud Shell](../cloud-shell/overview.md). You can also run the [Azure CLI locally](/cli/azure/install-azure-cli) to execute this command. When running locally, you must first run [`az login`](/cli/azure/reference-index#az-login) to sign in.

### [PowerShell](#tab/powershell)

To check the Azure Functions runtime, use the following cmdlet: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```
Replace `<FUNCTION_APP>` with the name of your function app and `<RESOURCE_GROUP>`. The current value of the `FUNCTIONS_EXTENSION_VERSION` setting is returned in the hash table.

---

::: zone pivot="platform-windows" 
## Change the runtime version

You can change the major version of the runtime used by your function app to another supported version. Because of the potential of breaking changes, you can only change the runtime version before you've created any functions in your function app. 

> [!IMPORTANT]
> Although the runtime version is determined by the `FUNCTIONS_EXTENSION_VERSION` setting, when possible you should only make this change in the Azure portal and not by changing the setting directly. This is because the portal validates your changes and makes other related changes as needed. 
> When you need to temporarily pin your function app to a specific minor version, it's OK to set the specific minor version by directly changing the `FUNCTIONS_EXTENSION_VERSION` setting.  

### [Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]


### [Azure CLI](#tab/azurecli)

You can update the `FUNCTIONS_EXTENSION_VERSION` setting in the function app with the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) command.

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Replace `<FUNCTION_APP>` with the name of your function app and `<RESOURCE_GROUP>` with the name of the resource group for your function app. Also, replace `<VERSION>` with either a supported major version (`~4` or `~1`) or a specific minor version you temporarily need to target.

Choose **Try it** in the previous code example to run the command in [Azure Cloud Shell](../cloud-shell/overview.md). You can also run the [Azure CLI locally](/cli/azure/install-azure-cli) to execute this command. When running locally, you must first run [`az login`](/cli/azure/reference-index#az-login) to sign in.

### [PowerShell](#tab/powershell)

To check the Azure Functions runtime, use the following cmdlet: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Replace `<FUNCTION_APP>` with the name of your function app and `<RESOURCE_GROUP>`. The current value of the `FUNCTIONS_EXTENSION_VERSION` setting is returned in the hash table.

Use the following script to change the Functions runtime:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Replace `<FUNCTION_APP>` with the name of your function app and `<RESOURCE_GROUP>` with the name of the resource group for your function app. Also, replace `<VERSION>` with either a supported major version (`~4` or `~1`) or a specific minor version you temporarily need to target. You can verify the updated value of the `FUNCTIONS_EXTENSION_VERSION` setting in the returned hash table. 

---

The function app restarts after the change is made to the application setting. 
::: zone-end
::: zone pivot="platform-linux" 
## <a name="manual-version-updates-on-linux"></a>Pin to a specific version on Linux

To pin a Linux function app to a specific host version, you set a version-specific base image URL in the [`linuxFxVersion` site setting][`linuxFxVersion`] in the format `DOCKER|<PINNED_VERSION_IMAGE_URI>`. 

> [!IMPORTANT]
> Pinned function apps on Linux don't receive regular security and host functionality updates. Unless recommended by a support professional, use the [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) setting and a standard [`linuxFxVersion`] value for your language and version, such as `Python|3.9`. For valid values, see the [`linuxFxVersion` reference article][`linuxFxVersion`].   
>
> Pinning to a specific runtime isn't currently supported for Linux function apps running in a Consumption plan. 

The following is an example of the [`linuxFxVersion`] value required to pin a Node.js 18 function app to a specific runtime version of 4.11.2:

`DOCKER|mcr.microsoft.com/azure-functions/node:4.11.2-node18-appservice` 

When needed, a support professional can provide you with a valid base image URI for your application. 

Use the following Azure CLI commands to view and set the [`linuxFxVersion`]. You can't currently set [`linuxFxVersion`] in the portal or by using Azure PowerShell. 

To view the current runtime version, use with the [az functionapp config show](/cli/azure/functionapp/config) command.

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group> --query 'linuxFxVersion' -o tsv
```

In this code, replace `<function_app>` with the name of your function app. Also replace `<my_resource_group>` with the name of the resource group for your function app. The current value of [`linuxFxVersion`] is returned.

To update the [`linuxFxVersion`] setting in the function app, use the [az functionapp config set](/cli/azure/functionapp/config) command.

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Replace `<FUNCTION_APP>` with the name of your function app. Also replace `<RESOURCE_GROUP>` with the name of the resource group for your function app. Finally, replace `<LINUX_FX_VERSION>` with the value of a specific image provided to you by a support professional.

You can run this command from the [Azure Cloud Shell](../cloud-shell/overview.md) by choosing **Try it** in the preceding code sample. You can also use the [Azure CLI locally](/cli/azure/install-azure-cli) to execute this command after executing [`az login`](/cli/azure/reference-index#az-login) to sign in.

The function app restarts after the change is made to the site config.
::: zone-end
## Next steps

> [!div class="nextstepaction"]
> [See Release notes for runtime versions](https://github.com/Azure/azure-webjobs-sdk-script/releases)

[`linuxFxVersion`]: functions-app-settings.md#linuxfxversion
