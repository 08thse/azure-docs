---
title: Configure customer-managed key encryption at rest in Azure VMware Solution
description: Learn how to encrypt data in Azure VMware Solution with customer-managed keys using Azure Key Vault.
ms.topic: how-to 
ms.date: 5/09/2022

---

# Configure customer-managed key encryption at rest in Azure VMware Solution

## Overview

This article will show you how to encrypt VMware vSAN Key Encryption Keys (KEK) with customer-managed keys (CMKs) managed by customer-owned Azure Key Vault.

When CMK encryptions are enabled on your Azure VMware Solution private cloud, Azure VMware Solution uses the CMK from your Key Vault to encrypt the vSAN KEKs. Each ESXi host that participates in the vSAN cluster uses randomly generated Disk Encryption Keys (DEKs) that ESXi uses to encrypt disk data at rest. vSAN encrypts all DEKs with a KEK provided by Azure VMware Solution key management system (KMS). Azure VMware Solution private cloud and Key Vault don't need to be in the same subscription.

When managing your own encryption keys, you can do the following actions:

- Control Azure access to vSAN keys.
- Centrally manage and lifecycle CMK.
- Revoke Azure from accessing the KEK.

Customer-managed keys (CMKs) feature supports, see the following shown by key type and key size.

- RSA: 2048, 3072, 4096
- RSA-HSM: 2048, 3072, 4096

## Topology

The following diagram shows how Azure VMware Solution uses Azure Active Directory (Azure AD) and a Key Vault to deliver the customer-managed key.

:::image type="content" source="media/configure-customer-managed-keys/customer-managed-keys-diagram-topology.png" alt-text="Diagram showing the customer-managed keys topology." border="false" lightbox="media/configure-customer-managed-keys/customer-managed-keys-diagram-topology.png":::

## Prerequisites

Before you begin to enable customer-managed key (CMK) functionality, ensure the following listed requirements are met:

1. You'll need an Azure Key Vault to use CMK functionality. If you don't have an Azure Key Vault, you can create one using [Quickstart: Create a Key Vault using the Azure portal](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal).
1. If you enabled restricted access to Key Vault, you'll need to allow Microsoft Trusted Services to bypass the Azure Key Vault firewall. Go to [Configure Azure Key Vault networking settings](https://docs.microsoft.com/azure/key-vault/general/how-to-azure-key-vault-network-security?tabs=azure-portal) to learn more.
1. Enable system assigned Identity on your Azure VMware Solution private cloud if you didn't enable it during software-defined data center (SDDC) provisioning.

    # [Azure Portal](#tab/azure-portal)
    
    1. Log into Azure portal.
    1. Navigate to **Azure VMware Solution** and locate your SDDC.
    1. From the left navigation, open **Manage** and select **Identity**. 
    1. In **System Assigned**, check **Enable** and select **Save**.
    
    **System Assigned identity** should now be enabled.

    # [Azure CLI](#tab/azure-cli)

    Get the Private Cloud resource id and save it to a variable. You will need this value in the next step to update resource with system assigned identity.
    
    `privateCloudId=$(az vmware private-cloud show --name $privateCloudName --resource-group $resourceGroupName --query id | tr -d '"')`
     
    To configure the system-assigned identity on Azure VMware Solution private cloud with Azure CLI, call [az-resource-update](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update), providing the variable for the private cloud resource ID that you previously retrieved.
    
    `az resource update --ids $privateCloudId --set identity.type=SystemAssigned --api-version "2021-12-01"`

---

4. Configure the key vault access policy to grant permissions to the managed identity. It'll be used to authorize access to the key vault.
    
    # [Azure Portal](#tab/azure-portal)

    1. Log into Azure portal.
    1. Navigate to **Key vaults** and locate the Key vault you want to use.
    1. From the left navigation, under **Settings**, select **Access policies**.
    1. In **Access policies**, select **Add Access Policy**.
        1. From the **Key Permission** drop-down, select **Get**, wrap key and unwrap Key permissions.
        1. Select **Principal**.
        1. Below the search box, paste the **Object ID** from the previous step or search the private cloud name you want to use. Choose **Select** when you're done.
        1. Select **ADD**.
        1. Verify the new policy appears under the current policy's Application section.
        1. Select **Save** to commit changes.

    # [Azure CLI](#tab/azure-cli)

   Get the principal ID for the system-assigned managed identity and save it to a variable. You will need this value in the next step to create the key vault access policy.
    
    `principalId=$(az vmware private-cloud show --name $privateCloudName --resource-group $resourceGroupName --query identity.principalId | tr -d '"')`
    
    To configure the key vault access policy with Azure CLI, call [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-set-policy), providing the variable for the principal ID that you previously retrieved for the managed identity.

    `az keyvault set-policy --name $keyVault --resource-group $resourceGroupName --object-id $principalId --key-permissions get unwrapKey wrapKey`

    Learn more about how to [Assign an Azure Key Vault access policy](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy?tabs=azure-portal).

---

## Enable CMK with system-assigned identity

System-assigned identity is restricted to one per resource and is tied to the lifecycle of the resource. You can grant permissions to the managed identity on Azure resource. The managed identity is authenticated with Azure AD, so you don't have to store any credentials in code.

>[!IMPORTANT]
> Ensure that Key Vault is in the same region as the Azure VMware Solution private cloud.

# [Azure Portal](#tab/azure-portal)

Navigate to your **Azure Key Vault** and provide access to the SDDC on Azure Key Vault using the Principal ID captured in the **Enable MSI** tab.

1. From your Azure VMware Solution private cloud, under **Manage**, select **Encryption** and then **Customer-managed keys (CMK)**.
1. CMK provides two options for **Key Selection** from Azure Key Vault.
    
    **Option 1**

    1. Under **Encryption key**, choose the **select from Key Vault** button.
    1. Select the encryption type, then the **Select Key Vault and key** option.
    1. Select the Key Vault and key from the drop-down, click **Select**.
    
    **Option 2**

      1. Under **Encryption key**, choose the **Enter key from URI** button.
      1. Enter a specific Key URI in the **Key URI** box.

    > [!IMPORTANT]
    > If you'd like to select a specific key version instead of the auto selected latest version, you'll need to specify the key URI with key version. This will affect the CMK key version life cycle.

1. Select **Save** to grant access to the resource.

# [Azure CLI](#tab/azure-cli)

To configure customer-managed keys for a Azure VMware Solution Private Cloud with automatic updating of the key version, call [az vmware private-cloud add-cmk-encryption](https://docs.microsoft.com/cli/azure/vmware/private-cloud?view=azure-cli-latest#az-vmware-private-cloud-add-cmk-encryption). Option 1  as shown in the following example without providing specific key version. Supply key version as argument to use customer-managed keys with specific key version as mentioned in option 2.

`keyVaultUrl =$(az keyvault show --name <keyvault_name> --resource-group <resource_group_name> --query properties.vaultUri --output tsv)`

**Option 1**
az vmware private-cloud add-cmk-encryption --private-cloud <private_cloud_name> --resource-group <resource_group_name> --enc-kv-url $keyVaultUrl --enc-kv-key-name <keyvault_key_name>

**Option 2**

`az vmware private-cloud add-cmk-encryption --private-cloud <private_cloud_name> --resource-group <resource_group_name> --enc-kv-url $keyVaultUrl --enc-kv-key-name --enc-kv-key-version <keyvault_key_keyVersion>`

---

## Customer-managed key version lifecycle

You can change the Customer Managed Key by creating a new version of the key at any time without interrupting the virtual machine (VM) workload.

In Azure VMware Solution, CMK key version rotation will depend on the key selection setting you've chosen during CMK setup.

**Key selection setting 1**

A customer enables CMK encryption without supplying a specific key version for CMK. Azure VMware Solution selects the latest key version for CMK from the customer's Key Vault to encrypt the vSAN KEKs. Azure VMware Solution tracks the CMK for version rotation. When a new version of the CMK key in Azure Key Vault is created, it's captured by Azure VMware Solution automatically to encrypt vSAN KEKs.

**Key selection setting 2**

A customer can enable CMK encryption for a specified CMK key version to supply the full key version URI under the **Enter Key from URI** option. When the customer's current key expires, the customer will need to re-enable CMK encryption with a new key.

## Change from customer-managed key to Microsoft managed key 

If a customer wants to change from customer-managed key (CMK) to Microsoft managed key (MMK), it won't interrupt VM workload. To make the change from CMK to MMK, use the following steps.

1. Select **Encryption**, located under **Manage** from your Azure VMware Solution private cloud.
2. Select **Microsoft-managed keys (MMK)**.
3. Select **Save**.
  
## Restore permission

## Errors  

Errors 403 and 404 occur when Managed Service Identity (MSI) doesn't have access to Key Vault. These errors occur when a user removes access policies within Key Vault, deletes Key Vault, DNS outages, networking issues, or Key Vault outages.  It results in the delay of update operations on vCenter. The approach is to shut down the hosts to avoid any malicious access to the data within an hour of the error.  Host maintenance and update operations, whether manual or automatic, won't work, but you can still access the private cloud. 

When all errors get resolved, access to the data will be restored. No data is lost because of outages. The error will get resolved by Microsoft fixing service outages or by you restoring a KEK or access to the Key Vault. To protect data from errors, Azure VMware Solution caches data encryption keys for as long as possible. You can write the data encryption keys to disks protected by a Microsoft key or a data protection API (DP API). But only if it continues to honor the one-hour window and is cleaned up afterward.

>[!TIP]
> You'll be notified if a key or access to the key is revoked. Once revoked, you can then define custom actions to take. If you lose access to your cached data encryption key, make the data inaccessible so you don't lose the data.

>[!TIP]
> You'll be notified if a key or access to the key is revoked. Once revoked, you can then define custom actions to take. If you lose access to your cached data encryption key, make the data inaccessible so you don't lose the data.

## Next steps