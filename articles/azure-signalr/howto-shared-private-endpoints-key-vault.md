---
title: Access Key Vault in a private network through shared private endpoints
titleSuffix: Azure SignalR Service
description: How to access key vault in private network through Shared Private Endpoints
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: how-to
ms.date: 09/23/2022
ms.author: dayshen
---

# Access Key Vault in a private network through shared private endpoints

Through Shared Private Endpoints, Azure SignalR Service can access your Key Vault in a private network. This way, your Key Vault is not exposed on a public network.

   :::image type="content" alt-text="Diagram showing architecture of shared private endpoint." source="media\howto-shared-private-endpoints-key-vault\shared-private-endpoint-overview.png" :::

You can create private endpoints through Azure SignalR Service APIs for shared access to a resource integrated with [Azure Private Link service](https://azure.microsoft.com/services/private-link/).   These endpoints, called *shared private link resources*, are created inside the SignalR execution environment and are not accessible outside this environment.

In this article, you learn how to create a shared private endpoint to Key Vault.

## Prerequisites

QUESTION: Do we need to add any prerequisites here?
- You will need an Azure SignalR Service instance.
- You will need an Azure Key Vault instance.


QUESTION:  Does it matter what the user calls the Key Vault, SignalR and endpoints?  I'm assuming they can be named anything, but I'm not sure.
> [!NOTE]
> The examples in this article are based on the following assumptions:
> * The resource ID of this Azure SignalR Service is _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr_.
> * The resource ID of Azure Key Vault is _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.KeyVault/vaults/contoso-kv_.
> * The rest of the examples show how the *contoso-signalr* service can be configured so that its outbound calls to Key Vault go through a private endpoint rather than public network.

## Create a shared private link resource to the Key Vault

### [Azure portal](#tab/azure-portal)

1. In the Azure portal, go to your Azure SignalR Service resource.
1. Select **Networking**. 
1. Select the **Private access** tab.
1. Select **Add shared private endpoint** in the **Shared private endpoints** section.

   :::image type="content" alt-text="Screenshot of shared private endpoints management." source="media\howto-shared-private-endpoints-key-vault\portal-shared-private-endpoints-management.png" lightbox="media\howto-shared-private-endpoints-key-vault\portal-shared-private-endpoints-management.png" :::

    Enter the following information:
    | Field | Description |
    | ----- | ----------- |
    | **Name** | The name of the shared private endpoint. |
    | **Type** | Select *Microsoft.KeyVault/vaults* |
    | **Subscription** | The subscription containing your Key Vault. |
    | **Resource** | Enter the name of your Key Vault resource. |
    | **Request Message** | Enter "please approve" |
    
    1. Select **Add**.

   :::image type="content" alt-text="Screenshot of adding a shared private endpoint." source="media\howto-shared-private-endpoints-key-vault\portal-shared-private-endpoints-add.png" :::

1. The shared private endpoint resource will be in **Succeeded** provisioning state. The connection state is **Pending** approval at target resource side.

   :::image type="content" alt-text="Screenshot of an added shared private endpoint." source="media\howto-shared-private-endpoints-key-vault\portal-shared-private-endpoints-added.png" lightbox="media\howto-shared-private-endpoints-key-vault\portal-shared-private-endpoints-added.png" :::


QUESTION:  Why is this article using dotnetcli instead of azurecli? These are all Azure CLI commands.
### [Azure CLI](#tab/azure-cli)

Make the following API call with the [Azure CLI](/cli/azure/) to create a shared private link resource:

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/sharedPrivateLinkResources/kv-pe?api-version=2021-06-01-preview --body @create-pe.json
```

The contents of the *create-pe.json* file, which represent the request body to the API, are as follows:

```json
{
      "name": "contoso-kv",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.KeyVault/vaults/contoso-kv",
        "groupId": "vault",
        "requestMessage": "please approve"
      }
}
```

The process of creating an outbound private endpoint is a long-running (asynchronous) operation. As in all asynchronous Azure operations, the `PUT` call returns an `Azure-AsyncOperation` header value that looks like the following:

```plaintext
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview"
```

You can poll this URI periodically to obtain the status of the operation.

If you're using the CLI, you can poll for the status by manually querying the `Azure-AsyncOperationHeader` value,

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview
```

Wait until the status changes to "Succeeded" before proceeding to the next steps.

-----

## Approve the private endpoint for the Key Vault

### [Azure portal](#tab/azure-portal)

1. Go to your Key Vault resource
1. Select the **Networking**.
1. Select the **Private endpoint connections** tab. 
    After the asynchronous operation has succeeded, there should be a request for a private endpoint connection with the request message from the previous API call.

   :::image type="content" alt-text="Screenshot of the Azure portal, showing the Private endpoint connections pane." source="media\howto-shared-private-endpoints-key-vault\portal-key-vault-approve-private-endpoint.png" :::

1. Select the private endpoint that Azure SignalR Service created. Click **Approve**.
:::image type="content" source="media/howto-shared-private-endpoints-key-vault/portal-keyvault-private-endpoint-approve-connection.png" alt-text="Screenshot of Approve connection dialog for private endpoint in Azure Key Vault.":::
1. Select **Yes** to approve the connection. 

   Make sure that the private endpoint connection appears as shown in the following screenshot. It could take one to two minutes for the status to be updated in the portal.

   :::image type="content" alt-text="Screenshot of the Azure portal, showing an Approved status on the Private endpoint connections pane." source="media\howto-shared-private-endpoints-key-vault\portal-key-vault-approved-private-endpoint.png" :::

### [Azure CLI](#tab/azure-cli)

1. List private endpoint connections.

    ```dotnetcli
    az network private-endpoint-connection list -n <key-vault-resource-name>  -g <key-vault-resource-group-name> --type 'Microsoft.KeyVault/vaults'
    ```

    There should be a pending private endpoint connection. Note down its ID.

    ```json
    [
        {
            "id": "<id>",
            "location": "",
            "name": "",
            "properties": {
                "privateLinkServiceConnectionState": {
                    "actionRequired": "None",
                    "description": "Please approve",
                    "status": "Pending"
               }
            }
        }
    ]
    ```

1. Approve the private endpoint connection.

    ```dotnetcli
    az network private-endpoint-connection approve --id <private-endpoint-connection-id>
    ```

-----

## Verify the private endpoint is functional 

It takes a few minutes for the approval to be propagated to Azure SignalR Service. You can check the state using either Azure portal or Azure CLI.

### [Azure portal](#tab/azure-portal)

   :::image type="content" alt-text="Screenshot of an approved shared private endpoint." source="media\howto-shared-private-endpoints-key-vault\portal-shared-private-endpoints-approved.png" lightbox="media\howto-shared-private-endpoints-key-vault\portal-shared-private-endpoints-approved.png" :::

### [Azure CLI](#tab/azure-cli)

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview
```

This would return a JSON, where the connection state would show up as "status" under the "properties" section.

```json
{
      "name": "contoso-kv",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.KeyVault/vaults/contoso-kv",
        "groupId": "vaults",
        "requestMessage": "please approve",
        "status": "Approved",
        "provisioningState": "Succeeded"
      }
}

```

When the "Provisioning State" (`properties.provisioningState`) of the resource is `Succeeded` and "Connection State" (`properties.status`) is `Approved`, the shared private link resource is functional and Azure SignalR Service can communicate over the private endpoint.

-----

At this point, the private endpoint between Azure SignalR Service and Azure Key Vault is established.

Now you can configure features like custom domain as usual. **You don't have to use a special domain for Key Vault**. DNS resolution is automatically handled by Azure SignalR Service.

## Next steps


+ [What are private endpoints?](../private-link/private-endpoint-overview.md)
+ [Configure custom domain](howto-custom-domain.md)
