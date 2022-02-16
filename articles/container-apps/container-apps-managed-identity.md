---
title: Enable managed identity in Container Apps
description: Learn how to enable a managed identity in Azure Container Apps that can authenticate with other Azure services
services: container-apps
author: cebundy
ms.service: container-apps
ms.topic: quickstart
ms.date: 02/08/2022
ms.author: v-bcatherine

---

# How to use managed identities with Azure Container Apps

Use [managed identities for Azure resources](../active-directory/managed-identities-azure-resources/overview.md) to run code in Azure Container Apps that interacts with other Azure services - without maintaining any secrets or credentials in code. The feature provides an Azure Container Apps deployment with an automatically managed identity in Azure Active Directory.

In this article, you learn more about managed identities in Azure Container Apps and:

> [!div class="checklist"]
> * Enable a user-assigned or system-assigned identity in a container app
> * Grant the identity access to an Azure key vault
> * Use the managed identity to access a key vault from a running container

Adapt the examples to enable and use identities in Azure Container Apps to access other Azure services. These examples are interactive. However, in practice your container images would run code to access Azure services.
 
> [!IMPORTANT]
> This feature is currently in preview. Previews are made available to you on the condition that you agree to the [supplemental terms of use](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Some aspects of this feature may change prior to general availability (GA). Currently, managed identities on Azure Container Apps, are only supported with Linux containers and not yet with Windows containers.

## Why use a managed identity?

Use a managed identity in a running container app to authenticate to any [service that supports Azure AD authentication](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) without managing credentials in your container code. For services that don't support AD authentication, you can store secrets in an Azure key vault and use the managed identity to access the key vault to retrieve credentials. For more information about using a managed identity, see [What is managed identities for Azure resources?](../active-directory/managed-identities-azure-resources/overview.md)

### Enable a managed identity

 When you create a container app, enable one or more managed identities by setting a [ContainerGroupIdentity]??? property. You can also enable or update managed identities after a container app is running - either action causes the container app to restart. To set the identities on a new or existing container app, use the Azure CLI, a Resource Manager template, a YAML file, or another Azure tool. 

Azure Container Apps supports both types of managed Azure identities: user-assigned and system-assigned. On a container app, you can enable a system-assigned identity, one or more user-assigned identities, or both types of identities. If you're unfamiliar with managed identities for Azure resources, see the [overview](../active-directory/managed-identities-azure-resources/overview.md).

### Use a managed identity

To use a managed identity, the identity must be granted access to one or more Azure service resources (such as a web app, a key vault, or a storage account) in the subscription. Using a managed identity in a running container is similar to using an identity in an Azure VM. See the VM guidance for using a [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell or Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md), or the [Azure SDKs](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

### Limitations

* Currently you can't use a managed identity in a container app deployed to a virtual network.
* You can't use a managed identity to pull an image from Azure Container Registry when creating a container app. The identity is only available within a running container.
* Currently, you can't use managed identity to access a storage resource, such as a queue or blob storage, from a container replica.  You must instead use a connection string or a storage account key.


[!INCLUDE [container-apps-create-cli-steps.md](../../includes/container-apps-create-cli-steps.md)]

## Create an Azure key vault

The examples in this article use a managed identity in Azure Container Apps to access an Azure key vault secret.

First choose a unique name for the key vault.  Replace <key-vault-name> with your own name:

# [Bash](#tab/bash)

```azurecli
KEY_VAULT="<key-vault-name>"
```

# [PowerShell](#tab/powershell)

```powershell
$KEY_VAULT="<key-vault-name>"
```

---

Create a key vault.

# [Bash](#tab/bash)

```azurecli
az keyvault create \
  --name $KEY_VAULT \
  --resource-group $RESOURCE_GROUP \ 
  --location $LOCATION
```

# [PowerShell](#tab/powershell)

```powershell
New-AzKeyVault -Name $KEY_VAULT" `
 -ResourceGroupName $RESOURCE_GROUP
 -Location $LOCATION

```

Store a sample secret:

# [Bash](#tab/bash)

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Apps" \
  --description ACIsecret \
  --vault-name $KEY_VAULT
```

# [PowerShell](#tab/powershell)

```powershell
Set-AzKeyVaultSecret `
  --name SampleSecret `
  --value "Hello Container Apps" `
  --description ContainerSecret `
  -- vault-name $KEY_VAULT
```

Continue with the following examples to access the key vault using either a user-assigned or system-assigned managed identity in Azure Container Apps.

## Example 1: Use a user-assigned identity to access Azure key vault

### Create an identity


Define an identity name:

# [Bash](#tab/bash)

```azurecli
IDENTITY_NAME="my-container-app-id"
```

# [PowerShell](#tab/powershell)

```powershell
$IDENTITY_NAME="my-container-app-id"
```

First create an identity in your subscription.  You can use the same resource group used to create the key vault, or use a different one.

# [Bash](#tab/bash)

```azurecli
az identity create \
  --resource-group $RESOURCE_GROUP \
  --name $IDENTITY_NAME
```

# [PowerShell](#tab/powershell)

```powershell
New-AzUserAssignedIdentity `
  -ResourceGroupName $RESOURCE_GROUP `
  -Name $IDENTITY_NAME
```

To use the identity in the following steps, use the [az identity show](/cli/azure/identity#az_identity_show) command to store the identity's service principal ID and resource ID in variables.

# [Bash](#tab/bash)

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show \
  --resource-group $RESOURCE_NAME \
  --name $IDENTITY_NAME \
  --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group $RESOURCE_NAME \
  --name $IDENTITY_NAME \
  --query id --output tsv)
```

# [PowerShell](#tab/powershell)
 
```powershell
# get the PsUserAssignedIdentity class object
$userassigned-id=Get-AzUserAssignedIdentity `
  -ResourceGroupName $RESOURCE_NAME `
  -Name $IDENTITY_NAME
```


### Grant user-assigned identity access to the key vault

Run the following command to set an access policy on the key vault. The following example allows the user-assigned identity to get secrets from the key vault:

# [Bash](#tab/bash)

```azurecli
 az keyvault set-policy \
    --name $KEY_VAULT\
    --resource-group $RESOURCE_GROUP \
    --object-id $spID \
    --secret-permissions get
```

# [PowerShell](#tab/powershell)

```powershell
Set-AzKeyVaultAccessPolicy `
  -VaultName $KEY_VAULT `
  -ResourceGroupName $RESOURCE_NAME `
  -ServicePrincipalName $userassigned-id.PrincipalId
  -PermissionsToSecrets Get
```


### Enable user-assigned identity on a container group

Run the following [az container create](/cli/azure/container#az_container_create) command to create a container instance based on Microsoft's `azure-cli` image. This example provides a single-container app that you can use interactively to run the Azure CLI to access other Azure services. In this section, only the base operating system is used. 

The `--assign-identity` parameter passes your user-assigned managed identity to the container app. The long-running command keeps the container running. This example uses the same resource group used to create the key vault, but you could specify a different one.

# [Bash](#tab/bash)

```azurecli
az containerapp create \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --name my-container-app \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"

```

# [PowerShell](#tab/powershell)

```powershell
az containerapp create `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --name my-container-app `
  --image mcr.microsoft.com/azure-cli `
  --assign-identity $userassigned-id.id `
  --command "tail -f /dev/null"
```


Within a few seconds, you should get a response from the Azure CLI indicating that the deployment has completed. Check its status with the [az container show](/cli/azure/container#az_container_show) command.


# [Bash](#tab/bash)

```azurecli-interactive
az containerapp show \
  --resource-group $RESOURCE_GROUP \
  --name my-container-app
```

# [PowerShell](#tab/powershell)

```azurecli-interactive
az containerapp show `
  --resource-group $RESOURCE_GROUP `
  --name my-container-app
```

The `identity` section in the output looks similar to the following, showing the identity is set in the container app. The `principalID` under `userAssignedIdentities` is the service principal of the identity you created in Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/my-container-app-id": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### Use user-assigned identity to get secret from key vault


## HOW DO WE DO THIS IN CONTAINER APPS???

Now you can use the managed identity within the running container instance to access the key vault. First launch a bash shell in the container:

```azurecli
az container exec \
  --resource-group $RESOURCE_GROUP \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Run the following commands in the bash shell in the container. To get an access token to use Azure Active Directory to authenticate to key vault, run the following command:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Output:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

To store the access token in a variable to use in subsequent commands to authenticate, run the following command:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Now use the access token to authenticate to key vault and read a secret. Be sure to substitute the name of your key vault in the URL (*https:\//mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

The response looks similar to the following, showing the secret. In your code, you would parse this output to obtain the secret. Then, use the secret in a subsequent operation to access another Azure resource.

```bash
{"value":"Hello Container Apps","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## Example 2: Use a system-assigned identity to access Azure key vault

### Enable system-assigned identity on a resource group

>[!IMPORTANT]
> Can system assigned identities be applied to the Container Apps environment?
> Are we going to implement system-assigned identity with the our containerapp create command the same way container create does?

Run the following [az container create](/cli/azure/container#az_container_create) command to create a container instance based on Microsoft's `azure-cli` image. This example provides a single-container app that you can use interactively to run the Azure CLI to access other Azure services. 

The `--assign-identity` parameter with no additional value enables a system-assigned managed identity on the container app. The identity is scoped to the resource group of the container app. The long-running command keeps the container running. This example uses the same resource group used to create the key vault, which is in the scope of the identity.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name $RESOURCE_GROUP --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group $RESOURCE_GROUP \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Within a few seconds, you should get a response from the Azure CLI indicating that the deployment has completed. Check its status with the [az container show](/cli/azure/container#az_container_show) command.

```azurecli-interactive
az container show \
  --resource-group $RESOURCE_GROUP \
  --name mycontainer
```

The `identity` section in the output looks similar to the following, showing that a system-assigned identity is created in Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Set a variable to the value of `principalId` (the service principal ID) of the identity, to use in later steps.

```azurecli-interactive
spID=$(az container show \
  --resource-group $RESOURCE_GROUP \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### Grant container app access to the key vault

Run the following [az keyvault set-policy](/cli/azure/keyvault) command to set an access policy on the key vault. The following example allows the system-managed identity to get secrets from the key vault:

```azurecli-interactive
 az keyvault set-policy \
   --name $KEY_VAULT\
   --resource-group $RESOURCE_GROUP \
   --object-id $spID \
   --secret-permissions get
```

### Use container app identity to get secret from key vault

Now you can use the managed identity to access the key vault within the running container instance. First launch a bash shell in the container:

```azurecli-interactive
az container exec \
  --resource-group $RESOURCE_GROUP \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Run the following commands in the bash shell in the container. First log in to the Azure CLI using the managed identity:

```azurecli
az login --identity
```

From the running container, retrieve the secret from the key vault:

```azurecli
az keyvault secret show \
  --name SampleSecret \
  --vault-name $KEY_VAULT--query value
```

The value of the secret is retrieved:

```output
"Hello Container Apps"
```

## Enable managed identity using Resource Manager template

To enable a managed identity in a container app using a Resource Manager template, set the `identity` property of the container app object with a `ContainerAppIdentity`??? object. The following snippets show the `identity` property configured for different scenarios.  Specify a minimum `apiVersion` of `???`.

### User-assigned identity

A user-assigned identity is a resource ID of the form:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

You can enable one or more user-assigned identities.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### System-assigned identity

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### System- and user-assigned identities

On a container app, you can enable both a system-assigned identity and one or more user-assigned identities.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```


## Next steps

In this article, you learned about managed identities in Azure Container Apps and how to:

> [!div class="checklist"]
> * Enable a user-assigned or system-assigned identity in a container app
> * Grant the identity access to an Azure key vault
> * Use the managed identity to access a key vault from a running container

* Learn more about [managed identities for Azure resources](../active-directory/managed-identities-azure-resources/index.yml).

* See an [Azure Go SDK example](https://medium.com/@samkreter/c98911206328) of using a managed identity to access a key vault from Azure Container Apps.
