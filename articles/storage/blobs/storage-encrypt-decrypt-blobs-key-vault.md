---
title: Encrypt and decrypt blobs using Azure Key Vault
titleSuffix: Azure Storage
description: Learn how to encrypt and decrypt a blob using client-side encryption with Azure Key Vault.
author: pauljewellmsft
ms.service: storage
ms.topic: tutorial
ms.date: 09/16/2022
ms.author: pauljewell
ms.reviewer: ozgun
ms.subservice: blobs
ms.devlang: csharp
ms.custom: devx-track-csharp
---

# Tutorial: Encrypt and decrypt blobs using Azure Key Vault

In this tutorial, you learn how to use client-side encryption to encrypt and decrypt blobs using keys stored with Azure Key Vault.

Azure Key Vault is a cloud solution for managing secrets, keys, and certificates. You can read more about Azure Key Vault on the [overview page](../../key-vault/general/overview.md).

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Add a key in Azure Key Vault
> * Configure client-side encryption options using a key stored in a key vault
> * Create a blob service client object with client-side encryption enabled
> * Upload an encrypted blob, then download and decrypt the blob

## Prerequisites

- Azure subscription - [create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure storage account - [create a storage account](../common/storage-account-create.md)
- Key vault - create one using [Azure portal](/azure/key-vault/general/quick-create-portal), [Azure CLI](/azure/key-vault/general/quick-create-cli), or [PowerShell](/azure/key-vault/general/quick-create-powershell)
- [Visual Studio 2022](https://visualstudio.microsoft.com) installed

## Assign a role to your Azure AD user

When developing locally, make sure that the user account that is accessing the key vault has the correct permissions. You'll need the [Key Vault Crypto Officer role](/azure/role-based-access-control/built-in-roles#key-vault-crypto-officer) to create a key and perform actions on keys in a key vault. You can assign Azure RBAC roles to a user using the Azure portal, Azure CLI, or Azure PowerShell. You can learn more about the available scopes for role assignments on the [scope overview](../articles/role-based-access-control/scope-overview.md) page.

In this scenario, you'll assign permissions to your user account, scoped to the key vault, to follow the [Principle of Least Privilege](../articles/active-directory/develop/secure-least-privileged-access.md). This practice gives users only the minimum permissions needed and creates more secure production environments.

The following example shows how to assign the **Key Vault Crypto Officer** role to your user account, which provides the access you'll need to complete this tutorial.

> [!IMPORTANT]
> In most cases it will take a minute or two for the role assignment to propagate in Azure, but in rare cases it may take up to eight minutes. If you receive authentication errors when you first run your code, wait a few moments and try again.

### [Azure portal](#tab/roles-azure-portal)

1. In the Azure portal, locate your key vault using the main search bar or left navigation.

2. On the storage account overview page, select **Access control (IAM)** from the left-hand menu.

3. On the **Access control (IAM)** page, select the **Role assignments** tab.

4. Select **+ Add** from the top menu and then **Add role assignment** from the resulting drop-down menu.

    :::image type="content" source="./media/storage-blob-encrypt-keyvault/assign-role-kv.png" lightbox="./media/storage-blob-encrypt-keyvault/assign-role-kv.png" alt-text="A screenshot showing how to assign a role in Azure portal.":::

5. Use the search box to filter the results to the desired role. For this example, search for *Key Vault Crypto Officer* and select the matching result and then choose **Next**.

6. Under **Assign access to**, select **User, group, or service principal**, and then choose **+ Select members**.

7. In the dialog, search for your Azure AD username (usually your *user@domain* email address) and then choose **Select** at the bottom of the dialog.

8. Select **Review + assign** to go to the final page, and then **Review + assign** again to complete the process.

### [Azure CLI](#tab/roles-azure-cli)

To assign a role at the resource level using the Azure CLI, you first must retrieve the resource ID using the `az storage account show` command. You can filter the output properties using the `--query` parameter.

```azurecli
az keyvault show --resource-group '<your-resource-group-name>' --name '<your-unique-keyvault-name>' --query id
```

Copy the output `Id` from the preceding command. You can then assign roles using the [az role](/cli/azure/role) command of the Azure CLI.

```azurecli
az role assignment create --assignee "<user@domain>" \
    --role "Key Vault Crypto Officer" \
    --scope "<your-resource-id>"
```

### [PowerShell](#tab/roles-powershell)

To assign a role at the resource level using Azure PowerShell, you first must retrieve the resource ID using the `Get-AzResource` command.

```azurepowershell
Get-AzResource -ResourceGroupName "<yourResourceGroupname>" -Name "<yourKeyVaultName>"
```

Copy the `Id` value from the preceding command output. You can then assign roles using the [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) command in PowerShell.

```azurepowershell
New-AzRoleAssignment -SignInName <user@domain> `
    -RoleDefinitionName "Key Vault Crypto Officer" `
    -Scope <yourKeyVaultId>
```

---

## Set up your project

1. In a console window (such as PowerShell or Bash), use the `dotnet new` command to create a new console app with the name *BlobEncryptionKeyVault*. This command creates a simple "Hello World" C# project with a single source file: *Program.cs*.

   ```dotnetcli
   dotnet new console -n BlobEncryptionKeyVault
   ```

1. Switch to the newly created *BlobEncryptionKeyVault* directory.

   ```console
   cd BlobEncryptionKeyVault
   ```

1. Open the project in your desired code editor. To open the project in:
    * Visual Studio, locate and double-click the `BlobEncryptionKeyVault.csproj` file.
    * Visual Studio Code, run the following command:

    ```bash
    code .
    ```
---

To interact with Azure services in this example, install the following client libraries using `dotnet add package`.

```dotnetcli
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Keys.Cryptography
dotnet add package Azure.Storage.Blobs
```

```powershell
Install-Package Azure.Identity
Install-Package Azure.Security.KeyVault.Keys.Cryptography
Install-Package Azure.Storage.Blobs
```

Add the following `using` directives and make sure to add a reference to `System.Configuration` to the project.

```csharp
using Azure.Identity
using Azure.Security.KeyVault.Keys.Cryptography
using Azure.Storage.Blobs
```

## Set environment variable

This application looks for an environment variable called `KEY_VAULT_NAME` to retrieve the name of your key vault. To set the environment variable, open a console window, and follow the instructions for your operating system. Replace `<your-key-vault-name>` with your actual key vault name.

**Windows:**

You can set environment variables for Windows from the command line. However, when using this approach the values are accessible to all applications running on that operating system and may cause conflicts if you aren't careful. Environment variables can be set at either user or system level:

```cmd
setx KEY_VAULT_NAME "<your-key-vault-name>"
````
After you add the environment variable in Windows, you must start a new instance of the command window. If you're using Visual Studio on Windows, you may need to relaunch Visual Studio after creating the environment variable for the change to be detected.

**Linux:**

```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## Add a key in Azure Key Vault

In this example, we create a key and add it to the key vault using the Azure Key Vault client library. You can also create and add a key to a key vault using [Azure CLI](/azure/key-vault/keys/quick-create-cli#add-a-key-to-key-vault), [Azure portal](/azure/key-vault/keys/quick-create-portal#add-a-key-to-key-vault), or [PowerShell](/azure/key-vault/keys/quick-create-powershell#add-a-key-to-key-vault).

In the sample below, we create a [KeyClient](/dotnet/api/azure.security.keyvault.keys.keyclient?view=azure-dotnet) object for the specified vault. The `KeyClient` object is then used to create a new RSA key in the specified vault.

```csharp
var keyName = "TestRSAKey";
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");

// URI for the key vault resource
var keyVaultUri = $"https://{keyVaultName}.vault.azure.net";

TokenCredential tokenCredential = new DefaultAzureCredential();

// Create a KeyClient object
var keyClient = new KeyClient(new Uri(keyVaultUri), tokenCredential);

// Add a key to the key vault
var key = await keyClient.CreateKeyAsync(keyName, KeyType.Rsa);
```

## Create key and key resolver instances

Next, we'll use the key we just added to the vault to create the cryptography client and key resolver instances. [CryptographyClient](/dotnet/api/azure.security.keyvault.keys.cryptography.cryptographyclient?view=azure-dotnet-preview) implements [IKeyEncryptionKey](/dotnet/api/azure.core.cryptography.ikeyencryptionkey?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) and is used to perform cryptographic operations with keys stored in Azure Key Vault. [KeyResolver](/dotnet/api/azure.security.keyvault.keys.cryptography.keyresolver?view=azure-dotnet)  implements [IKeyEncryptionResolver](/dotnet/api/azure.core.cryptography.ikeyencryptionkeyresolver?view=azure-dotnet) and retrieves key encryption keys from the key identifier and resolves the key.
```csharp
// Cryptography client and key resolver instances using Azure Key Vault client library
CryptographyClient cryptoClient = keyVaultClient.GetCryptographyClient(key.Value.Name, key.Value.Properties.Version);
KeyResolver keyResolver = new KeyResolver(tokenCredential);
```

If you have an existing key in the vault that you'd like to encrypt with, you can create the key and key resolver instances by passing in the URI:
```csharp
var keyVaultKeyUri = $"https://{keyVaultName}.vault.azure.net/keys/{keyName}";
CryptographyClient cryptoClient = new CryptographyClient(new Uri(keyVaultKeyUri), tokenCredential);
```

## Configure encryption options

Now we need to configure the encryption options to be used for blob upload and download. To use client-side encryption, we first create a `ClientSideEncryptionOptions` object and set it on client creation with `SpecializedBlobClientOptions`. 

 The [ClientSideEncryptionOptions](/dotnet/api/azure.storage.clientsideencryptionoptions?view=azure-dotnet) class provides the client configuration options for connecting to blob storage using client-side encryption. [KeyEncryptionKey](/dotnet/api/azure.storage.clientsideencryptionoptions.keyencryptionkey?view=azure-dotnet) is required for upload operations and is used to wrap the generated content encryption key. [KeyResolver](/dotnet/api/azure.storage.clientsideencryptionoptions.keyresolver?view=azure-dotnet) is required for download operations and fetches the correct key encryption key to unwrap the downloaded content encryption key. [KeyWrapAlgorithm]() is required for uploads and specifies the algorithm identifier to use when wrapping the content encryption key.

> [!WARNING]
>It's important to construct the `ClientSideEncryptionOptions` object using `ClientSideEncryptionVersion.V2_0`. This is recommended due to a security vulnerability in version 1. For more information about this security vulnerability, see [Azure Storage updating client-side encryption in SDK to address security vulnerability](https://aka.ms/azstorageclientencryptionblog).

```csharp
// Configure the encryption options to be used for upload and download
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V2_0)
{
    KeyEncryptionKey = cryptoClient,
    KeyResolver = keyResolver,
    // String value that the client library will use when calling IKeyEncryptionKey.WrapKey()
    KeyWrapAlgorithm = "RSA-OAEP"
};

// Set the encryption options on the client options.
BlobClientOptions options = new SpecializedBlobClientOptions() { ClientSideEncryption = encryptionOptions };
```

## Configure client object to use client-side encryption

In this example, we apply the client-side encryption configuration options to a `BlobServiceClient` object. When applied at the service client level, these encryption options are passed from the service client to container clients, and from container clients to blob clients. When the `BlobClient` object performs an upload or download operation, the Azure Blob Storage client libraries use envelope encryption to encrypt and decrypt blobs on the client side. Envelope encryption encrypts a key with one or more additional keys.

```csharp
// Create a blob client with client-side encryption enabled.
// Client-side encryption options are passed from service clients to container clients, 
// and from container clients to blob clients.
// Attempting to construct a BlockBlobClient, PageBlobClient, or AppendBlobClient from a BlobContainerClient
// with client-side encryption options present will throw, as this functionality is only supported with BlobClient.
BlobClient blob = new BlobServiceClient(blobUri, tokenCredential, options).GetBlobContainerClient("my-container").GetBlobClient("myBlob");
```

## Encrypt blob and upload
When the `BlobClient` object calls an upload method, several steps occur to perform the client-side encryption:
1. The Azure Storage client library generates a random initialization vector (IV) of 16 bytes and a random content encryption key (CEK) of 32 bytes, and performs envelope encryption of the blob data using this information.
1. Blob data is encrypted using the CEK.
1. The CEK is then wrapped (encrypted) using the key encryption key (KEK) we specified in `ClientSideEncryptionOptions`. In this example, the KEK is an asymmetric key pair stored in the specified Azure Key Vault resource. The blob client itself never has access to the KEK, it just invokes the key wrapping algorithm that is provided by Key Vault.
1. The encrypted blob data is then uploaded to the storage account.

Add the following code to encrypt a blob and upload it to your Azure storage account:

```csharp
// Upload the encrypted contents to the blob
Stream blobContent = BinaryData.FromString("Ready for encryption, Captain.").ToStream();
await blob.UploadAsync(blobContent);
```

## Decrypt blob and download

The Azure Storage client library assumes that the user is managing the KEK either locally or in a key vault. The user doesn't need to know the specific key that was used for encryption. The key resolver specified in `ClientSideEncryptionOptions` will be used to resolve key identifiers when blob data is downloaded and decrypted.

When the `BlobClient` object calls a download method, several steps occur to decrypt the encrypted blob data:

1.The client library downloads the encrypted data along with any encryption information stored in the storage account.
1.The wrapped CEK is then unwrapped (decrypted) using the KEK. The client library doesn't have access to the KEK during this process, but only invokes the key unwrapping algorithm specified in `ClientSideEncryptionOptions`. The private key of an RSA key remains in the key vault, so the encrypted key from the blob metadata that contains the CEK is sent to the key vault for decryption.
1.The client library uses the CEK to decrypt the encrypted blob data.

Add the following code to download and decrypt the blob that you previously uploaded.

```csharp
// Download and decrypt the encrypted contents from the blob
 MemoryStream outputStream = new MemoryStream();
blob.DownloadTo(outputStream);
```

## Next steps

For more information about using Microsoft Azure Storage with C#, see [Microsoft Azure Storage Client Library for .NET](/previous-versions/azure/dn261237(v=azure.100)).

For more information about the Blob REST API, see [Blob Service REST API](/rest/api/storageservices/Blob-Service-REST-API).

For the latest information on Microsoft Azure Storage, go to the [Microsoft Azure Storage Team Blog](/archive/blogs/windowsazurestorage/).
