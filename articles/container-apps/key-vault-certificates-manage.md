---
title: Import certificates from Azure Key Vault to Azure Container Apps
description: Learn to managing secure certificates in Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 04/22/2024
ms.author: cshoe
---

# Import certificates from Azure Key Vault to Azure Container Apps (preview)

You can set up Azure Key Vault to manage your container app's certificates to handle updates, renewals, and monitoring. Without Key Vault, you're left managing your certificate manually, which means you can't manage certificates in a central location and can't take advantage of lifecycle automation or notifications.

## Prerequisites

- [Azure Key Vault](/azure/key-vault/): Make sure you have a certificate stored in Azure Key Vault.

- [Assign a Key Vault access policy](../key-vault/general/assign-access-policy-cli.md): By default, your container app doesn't have access to your vault. To use a key vault for a certificate deployment, you must [authorize read access for the resource provider to the vault](../key-vault/general/assign-access-policy-cli.md).

- [Azure CLI](/cli/azure/install-azure-cli): You need the Azure CLI updated with the Azure Container Apps extension version `0.3.49` or higher. Use the `list-available` command to view your extension's version number.

    ```azurecli
    az extension list-available --output table | findstr containerapp
    ```

    If you need to upgrade your extension, then use the `upgrade` parameter with the `add` command:

    ```azurecli
    az extension add --name containerapp --upgrade`
    ```

## Enable managed identity

An [Azure Key Vault](/azure/key-vault/general/manage-with-cli2) instance is required to store your certificate. Make the following updates to your Key Vault instance:

1. Open the [Azure portal](https://portal.azure.com) and find your instance of Azure Key Vault.

1. Edit the Identity Access Management (IAM) access control and set yourself as a *Key Vault Administrator*.

1. Go to your certificate's details and copy the value for *Secret Identifier* and paste it into a text editor for use in an upcoming step.

## Assign roles

1. Open the [Azure portal](https://portal.azure.com) and find your instance of your Azure Container Apps environment where you want to import a certificate.

1. Go to the *Identity* tab and set *RBAC* to **Key Vault Secrets User**.

## Import a certificate

Once you authorize your container app to read the vault, you can use the `az containerapp env certificate upload` command to associate your vault with your Container Apps environment.

Before you run the following command, replace the placeholder tokens surrounded by `<>` brackets with your own values.

```azurecli
az containerapp env certificate upload \
  --resource-group <RESOURCE_GROUP> \
  --name <CONTAINER_APP_NAME> \
  --akv-url <KEY_VAULT_URL>
  --certificate-identity <CERTIFICATE_IDENTITY>
```

## Related

> [!div class="nextstepaction"]
> [Manage secrets](manage-secrets.md)
