---
title: Custom question answering encryption of data at rest
titleSuffix: Azure Cognitive Services
description: Microsoft offers Microsoft-managed encryption keys, and also lets you manage your Cognitive Services subscriptions with your own keys, called customer-managed keys (CMK). This article covers data encryption at rest for custom question answering, and how to enable and manage CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: egeaney
#Customer intent: As a user of the QnA Maker service, I want to learn how encryption at rest works.
---

# Custom question answering encryption of data at rest

Custom question answering automatically encrypts your data when it is persisted to the cloud, helping to meet your organizational security and compliance goals.

## About encryption key management

By default, your subscription uses Microsoft-managed encryption keys. There is also the option to manage your subscription with your own keys called customer-managed keys (CMK). CMK offers greater flexibility to create, rotate, disable, and revoke access controls. You can also audit the encryption keys used to protect your data. If CMK is configured for your subscription, double encryption is provided, which offers a second layer of protection, while allowing you to control the encryption key through your Azure Key Vault.

Custom question answering uses [CMK support from Azure search](../../../../search/search-security-manage-encryption-keys.md), and automatically associates the provided CMK to encrypt the data stored in Azure search index.

> [!IMPORTANT]
> Your Azure Search service resource must have been created after January 2019 and cannot be in the free (shared) tier. There is no support to configure customer-managed keys in the Azure portal.

## Enable customer-managed keys

Follow these steps to enable CMKs:

1.	Go to the **Encryption** tab of your Text Analytics service with Custom question answering (preview) feature enabled.
2.	Select the **Customer Managed Keys** option. Provide the details of your [customer-managed keys](../../../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) and select **Save**.

> [!div class="mx-imgBorder"]
> ![Question Answering CMK](media/question-answering-cmk.png)
   
3.	On a successful save, the CMK will be used to encrypt the data stored in the Azure Search Index.

> [!IMPORTANT]
> It is recommended to set your CMK in a fresh Azure Cognitive Search service before any knowledge bases are created. If you set CMK in a Text Analytics service with existing knowledge bases, you might lose access to them. Read more about [working with encrypted content](../../../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content) in Azure Cognitive search.

> [!NOTE]
> To request the ability to use customer-managed keys, fill out and submit the [Cognitive Services Customer-Managed Key Request Form](https://aka.ms/cogsvc-cmk).

---

## Regional availability

Customer-managed keys are available in all Azure Search regions.

## Encryption of data in transit

QnA Maker portal runs in the user's browser. Every action triggers a direct call to the respective Cognitive Service API. Hence, QnA Maker is compliant for data in transit.
However, as the QnA Maker portal service is hosted in West-US, it is still not ideal for non-US customers. 

## Next steps

* [Encryption in Azure Search using CMKs in Azure Key Vault](../../../../search/search-security-manage-encryption-keys.md)
* [Data encryption at rest](../../../../security/fundamentals/encryption-atrest.md)
* [Learn more about Azure Key Vault](../../../../key-vault/general/overview.md)
