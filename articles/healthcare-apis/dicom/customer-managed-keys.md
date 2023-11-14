---
title: Best practices for customer-managed keys for the DICOM service in Azure Health Data Services
description: Encrypt your data with customer-managed keys (CMK) in the DICOM service in Azure Health Data Services. Get tips on requirements, best practices, limitations, and troubleshooting.
author: mmitrik
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/20/2023
ms.author: mmitrik
---

# Best practices for using customer-managed keys with the DICOM service

By using customer-managed keys (CMK), you can protect and control access to your organization's data with keys that you create and manage. You use [Azure Key Vault](../../key-vault/index.yml) to create and manage CMK and then use the keys to encrypt the data stored by the DICOM&reg; service. 

## Requirements

You need to store customer-managed keys in [Azure Key Vault](../../key-vault/general/overview.md) or [Azure Key Vault Managed Hardware Security Module (HSM)](../../key-vault/managed-hsm/overview.md).

Customer-managed keys must meet these requirements:

   - The key is versioned.
  
   - The key type is **RSA-HSM** or **RSA**.
  
   - The key is **2048-bit** or **3072-bit**.
  
   - The key vault is located in the same Azure tenant as the DICOM service.
  
   - When using a key vault with a firewall to disable public access, the option to **Allow trusted Microsoft services to bypass this firewall** must be enabled.

   - To prevent losing the encryption key for the DICOM service, the key vault or managed HSM must have **soft delete** and **purge protection** enabled. These features allow you to recover deleted keys for a certain time (default 90 days) and block permanent deletion until that time is over.

   - Before you set up keys, enable a managed identity for the DICOM service. Use either a system-assigned or user-assigned managed identity. For more information, see [Microsoft Entra managed identities for Azure resources](/entra/identity/managed-identities-azure-resources/overview).

## Rotate keys often

Follow [security best practices](../../key-vault/secrets/secrets-best-practices.md) and rotate keys often. Keys used with the DICOM service must be rotated manually. To rotate a key, update the version of the existing key or set a new encryption key from a different storage location. Always make sure to keep existing keys enabled when adding new keys because they're still needed to access the data that was encrypted with them.  

## Update the DICOM service after changing a managed identity
If you change the managed identity in any way, such as moving your DICOM service to a different tenant or subscription, the DICOM service isn't able to access your keys until you update the service manually with an ARM template deployment. For steps, see [Use an ARM template to update the encryption key](configure-customer-managed-keys.md#use-an-arm-template-to-update-the-encryption-key).

## Recover from lost key access

For the DICOM service to operate properly, it must always have access to the key in the key vault. However, there are scenarios where the service could lose access to the key, including:

- The key is disabled or deleted from the key vault.

- The DICOM service system-assigned managed identity is disabled.

- The DICOM service system-assigned managed identity loses access to the key vault.

In any scenario where the DICOM service can't access the key, API requests return with `500` errors and the data is inaccessible until access to the key is restored. The [Azure Resource health](../../service-health/overview.md) view for the DICOM service helps you diagnose key access issues.

If key access is lost for less than 30 minutes, data is automatically recovered. After access is re-enabled, allow 5 to 10 minutes for your DICOM service to become available again.

If key access is lost for more than 30 minutes, you can recover by following these steps:

1. Complete the steps to re-enable key access, such as restoring lost permissions. 

2. Get the currently configured properties of your DICOM service. 

3. Perform an update to your DICOM service by deploying an ARM template with any additional configured properties added.  For example:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "tags": {
            "type": "Object"
        }
    },
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/workspaces/dicomservices",
            "apiVersion": "2023-11-01",
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tags')]",
            "identity": {
                <insert configured identity>
            },
            "properties": {
                <insert any additional configured properties>,
                "encryption": {
                    "customerManagedKeyEncryption": {
                        "keyEncryptionKeyUrl": ""
                    }
                }
            }
        }
    ]
}
```

After the deployment completes, the key will be re-validated automatically and the DICOM service will return to normal operation.

## Key vault location
The key vault must be located in the same Azure tenant as your DICOM service.

## Firewall settings
When using a key vault with a firewall to disable public access, the option to **Allow trusted Microsoft services to bypass this firewall** must be enabled.

## Next steps

[Configure customer-managed keys for the DICOM service](configure-customer-managed-keys.md)

[!INCLUDE [DICOM trademark statement](../includes/healthcare-apis-dicom-trademark.md)]