---
title: Storing and using certificates in Azure Cloud Services (extended support)
description: Processes for storing and using certificates in Azure Cloud Services (extended support)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: 
---

# Storing and using certificates in Azure Cloud Services (extended support)

To install certificates on your Cloud Service roles, users need to add the certificates to a Key Vault and reference the certificate thumbprints in the cscfg and osProfile.

## Upload a certificate to Key Vault 

1.	Login to the Azure portal and navigate to your Key Vault. If you do not have Key Vault set up, you can opt to create one in the same window.

2.	Ensure your Access policies include the following properties:
    - **Enable access to Azure Virtual Machines for deployment**
    - **Enable access to Azure Resource Manager for template deployment** 

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="Image shows access policies window in the Azure portal.":::
 
3.	Click on **Certificates** and select **Generate / Import**. Import the certificate generated when you created your Cloud Service. 
 
    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Image shows importing window in the Azure portal.":::

4.	In your cscfg file, ensure you add the certificate details associated with the role. 

    `<Certificate name="<your cert name>" thumbprint="<thumbprint in Key Vault" thumbprintAlgorithm="sha1" />`

5.	In your template file, add the Key Vault reference. 

    - `vaultId` is the Azure Resource Manager ID to your Key Vault. You can find this information by looking in the properties section of the Key Vault. 
    - `vaultSecertUrl` is stored in the certificate of your Key Vault. Browse to your certificate in the Azure portal and copy the **Certificate Identifier**.

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="Image shows adding properties to an ARM template.":::
 

## Next steps

[Enable Remote Desktop](enable-rdp.md) for your Cloud Service (extended support) roles.
