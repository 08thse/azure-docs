---
title: "Quickstart: Get started with using Trusted Signing"
description: Complete this quickstart to get started with using Trusted Signing to sign your files.
author: mehasharma 
ms.author: mesharm 
ms.service: trusted-signing 
ms.topic: quickstart 
ms.date: 04/12/2024 
ms.custom: references_regions 
---


# Quickstart: Get started with using Trusted Signing

Trusted Signing is an Azure fully managed, end-to-end certificate signing service. In this quickstart, you create the following three Trusted Signing resources:

- A Trusted Signing account
- An identity validation
- A certificate profile

Trusted Signing gives you both an Azure portal and an Azure CLI extension experience to create and manage your Trusted Signing resources.

*You can complete identity validation only in the Azure portal. You can't complete identity validation by using the Azure CLI.*

## Prerequisites

- A Microsoft Entra tenant ID.

   For more information, see [Create a Microsoft Entra tenant](/azure/active-directory/fundamentals/create-new-tenant#create-a-new-tenant-for-your-organization).

- An Azure subscription.

   If you don't already have one, see [Create an Azure subscription](../cost-management-billing/manage/create-subscription.md#create-a-subscription) before you begin.

## Register the Trusted Signing resource provider

Before you use Trusted Signing, you must first register the Trusted Signing resource provider.

To register a Trusted Signing resource provider:

A resource provider is a service that supplies Azure resources. Use the Azure portal or the Azure CLI `az provider register` command to register the `Microsoft.CodeSigning` Trusted Signing resource provider.

# [Azure portal](#tab/registerrp-portal)

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. In either the search box or under **All services**, select **Subscriptions**.
1. Select the subscription where you want to create Trusted Signing resources.

   :::image type="content" source="media/trusted-signing-subscription-resource-provider.png" alt-text="Screenshot of trusted-signing-subscription-resource-provider." lightbox="media/trusted-signing-subscription-resource-provider.png":::

1. In the list of resource providers, select **Microsoft.CodeSigning**. By default, the resource provider is `NotRegistered`.
1. Select the ellipsis, and then select **Register**.

   The status changes to **Registered**.

   :::image type="content" source="media/trusted-signing-resource-provider-registration.png" alt-text="Screenshot that shows the Microsoft.CodeSigning resource provider as registered." lightbox="media/trusted-signing-resource-provider-registration.png":::

# [Azure CLI](#tab/registerrp-cli)

1. If you're using a local installation of the Azure CLI, sign in to the Azure CLI by using the `az login` command.  

1. To finish the authentication process, complete the steps that are described in your terminal. For other sign-in options, see [Sign in by using the Azure CLI](/cli/azure/authenticate-azure-cli).

1. When you're prompted on first use, install the Azure CLI extension. For more information, see [Use extensions with the Azure CLI](/cli/azure/azure-cli-extensions-overview).

   For more information about the Trusted Signing CLI extension, see [Trusted Signing service](https://learn.microsoft.com/cli/azure/service-page/trusted%20signing%20service?view=azure-cli-latest).

1. To see the versions of the Azure CLI and the dependent libraries that are installed, use the `az version` command.

1. To upgrade to the latest versions, use the following code:

   ```bash
   az upgrade [--all {false, true}]
      [--allow-preview {false, true}]
       [--yes]
   ```

1. To set your default subscription ID, use the `az account set -s <subscription ID>` command.

1. To register a Trusted Signing resource provider, use this command:

   ```azurecli
   az provider register --namespace "Microsoft.CodeSigning"
   ```

1. You can verify that registration is complete by using this command:

   ```azurecli
   az provider show --namespace "Microsoft.CodeSigning"
   ```

1. To add the extension for Trusted Signing, use this command:

   ```azurecli
   az extension add --name trustedsigning
   ```

---

## Create a Trusted Signing account

A Trusted Signing account is a logical container that holds identity validation and certificate profile resources.

### Azure regions that support Trusted Signing

You can create resources only in Azure regions where Trusted Signing is currently available. The following table lists the current Azure regions that support Trusted Signing resources:

| Region                               | Region class fields  | Endpoint URI value                   |
| :----------------------------------- | :------------------- |:-------------------------------------|
| East US                              | EastUS               | `https://eus.codesigning.azure.net`  |
| West US                              | WestUS               | `https://wus.codesigning.azure.net`  |
| West Central US                      | WestCentralUS        | `https://wcus.codesigning.azure.net` |
| West US 2                            | WestUS2              | `https://wus2.codesigning.azure.net` |
| North Europe                         | NorthEurope          | `https://neu.codesigning.azure.net`  |
| West Europe                          | WestEurope           | `https://weu.codesigning.azure.net`  |

### Naming constraints for Trusted Signing accounts

Trusted Signing account names have some constraints.

A Trusted Signing account name must:

- Contain from 3 to 24 alphanumeric characters.
- Begin with a letter, end with a letter or number, and not contain consecutive hyphens.
- Be globally unique.

A Trusted Signing account name is:

- Not case-sensitive (“ABC” is the same as “abc”).
- Rejected by Azure Resource Manager if it begins with "one".

# [Azure portal](#tab/account-portal)

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. On either the Azure portal menu or on the Home pane, select **Create a resource**.
1. In the search box, enter and then select **Trusted Signing account**.
1. On the Trusted Signing account Overview pane, select **Create**.
1. For **Subscription**, select your Azure subscription.
1. For **Resource group**, select **Create new**, and then enter a resource group name.
1. For **Account Name**, enter a unique account name.

   For naming requirements, see [Naming constraints for Trusted Signing accounts](#naming-constraints-for-trusted-signing-accounts).
1. For **Region**, select an Azure region that supports Trusted Signing.
1. For **Pricing**, select a pricing tier.
1. Select the **Review + Create** button.

   :::image type="content" source="media/trusted-signing-account-creation.png" alt-text="Screenshot that shows creating a Trusted Signing account." lightbox="media/trusted-signing-account-creation.png":::

1. After you successfully create your Trusted Signing account, select **Go to resource**.  

# [Azure CLI](#tab/account-cli)

To create a Trusted Signing account by using the Azure CLI:

1. Create a resource group by using the following command. If you choose to use an existing resource group, skip this step.

   ```azurecli
   az group create --name MyResourceGroup --location EastUS
   ```

1. Create a unique Trusted Signing account by using the following command.

   For naming requirements, see [Naming constraints for Trusted Signing accounts](#naming-constraints-for-trusted-signing-accounts).

   To create a Trusted Signing account that has a Basic SKU:

   ```azurecli
   trustedsigning create -n MyAccount -l eastus -g MyResourceGroup --sku Basic
   ```

   To create a Trusted Signing account that has a Premium SKU:

   ```azurecli
   trustedsigning create -n MyAccount -l eastus -g MyResourceGroup --sku Premium
   ```

1. Verify your Trusted Signing account by using the `trustedsigning show -g MyResourceGroup -n MyAccount` command.

   > [!NOTE]
   > If you use an earlier version of the Azure CLI from the Trusted Signing private preview, your account defaults to the Basic SKU. To use the Premium SKU, either upgrade the Azure CLI to the latest version or use the Azure portal to create the account.

The following table lists *helpful commands* to use when you create a Trusted Signing account:

| Command                                                                                  | Description                               |  
|:-----------------------------------------------------------------------------------------|:------------------------------------------|
| `trustedsigning -h`                                                                      | Shows help commands and detailed options.   |
| `trustedsigning show -n MyAccount  -g MyResourceGroup`                                   | Shows the details of an account.            |
| `trustedsigning update -n MyAccount -g MyResourceGroup --tags "key1=value1 key2=value2"` | Update tags                               |
| `trustedsigning list -g MyResourceGroup`                                                 | Lists all accounts that are in a resource group. |

---

## Create an identity validation request

You can complete your own identity validation by filing out the request form with the information that must be included in the certificate. Identity validation can be completed only in the Azure portal. You can't complete identity validation by using the Azure CLI.

To create an identity validation request:

1. In the Azure portal, go to your new Trusted Signing account.
1. Confirm that you are assigned the Trusted Signing Identity Verifier role.

   - To learn about managing access by using role-based access control (RBAC), see [Assign roles in Trusted Signing](tutorial-assign-roles.md).
1. On either the Trusted Signing account Overview page or on the Objects pane, select **Identity Validation**.
1. Select **New Identity Validation**, and then select either **Public** or **Private**.

   - Public identity validation applies only to these certificate profile types: Public Trust, Public Trust Test, VBS Enclave.
   - Private identity validation applies only to these certificate profile types: Private Trust, Private Trust CI Policy.
1. On **New identity validation**, provide the following information:

    | Fields       | Details     |
    | :------------------- | :------------------- |
    | **Organization Name**          | For public identity validation, provide the legal business entity to which the certificate will be issued. For private identity validation, the value defaults to your Microsoft Entra tenant name. |
    | **(Private Identity Type only) Organizational Unit**          | Enter the relevant information. |
    | **Website url**          | Enter the primary website that belongs to the legal business entity. |
    | **Primary Email**           | Enter the organization’s primary email address. A verification link is sent to this email address to verify the email address. Ensure that the email address can receive emails from external email addresses that have links. The verification link expires in seven days.  |
    | **Secondary Email**          | These email addresses must be different than the primary email address. For organizations, the domain must match the email address that's provided in the primary email address. Ensure that the email address can receive emails from external email addresses that have links.|
    | **Business Identifier**           | Enter a business identifier for the legal business entity. |
    | **Seller ID**          | Applicable only to Microsoft Store customers. Find your Seller ID in the Partner Center portal. |
    | **Street, City, Country, State, Postal code**           | Enter the business address of the legal business entity. |

1. Check **Certificate subject preview** for the preview of the information that appears in the certificate.
1. Select **Review and accept Trusted Signing Terms of Use**. You can download the Terms of Use to review or save them.  
1. Select the **Create** button.
1. When the request is successfully created, the identity validation request status changes to **In Progress**.
1. If more documents are required, an email is sent and the request status changes to **Action Required**.
1. When the identity validation process is finished, the request status changes, and an email is sent with the updated status of the request:

   - **Completed** when the process is completed successfully.
   - **Failed** When the process is not completed successfully.

:::image type="content" source="media/trusted-signing-identity-validation-public.png" alt-text="Screenshot that shows the Public option in the New identity validation pane." lightbox="media/trusted-signing-identity-validation-public.png":::

:::image type="content" source="media/trusted-signing-identity-validation-private.png" alt-text="Screenshot that shows the Private option in the New identity validation pane." lightbox="media/trusted-signing-identity-validation-private.png":::

### Important information for public identity validation

| Requirements         | Details     |
| :------------------- | :------------------- |
| Onboarding           | Trusted Signing at this time can onboard only legal business entities that have verifiable tax history of three or more years. For a quicker onboarding process, ensure that public records for the legal business entity that you are validated are up to date. |
| Accuracy             | Ensure that you provide the correct information for public identity validation. If you need to make any changes after it is created, you must complete a new identity validation request. This change affects the associated certificates that are being used for signing. |
| Additional documentation            | If we need more documentation to process the identity validation request, you are notified though email. You can upload the documents in the Azure portal. The documentation request email contains information about file size requirements. Ensure that any documents you provide are the most current. |
| Failed email verification            | If email verification fails, you must initiate a new identity validation request. |
| Identity validation status            | You are notified through email when there is an update to the Identity Validation status. You can also check the status in the Azure portal at any time. |
| Processing time            | Processing your identity validation request takes from 1 to 7 business days (possibly longer if we need to request more documentation from you). |

## Create a certificate profile  

A certificate profile resource is the logical container of the certificates that is issued to you for signing.

### Naming constraints for certificate profiles

Certificate profile names have some constraints.

A certificate profile name must:

- Contain from 5 to 100 alphanumeric characters.
- Begin with a letter, end with a letter or number, and not contain consecutive hyphens.
- Be unique within the account.

A certificate profile name is:

- In the same Azure region as the account, by default inheritance.
- Not case-sensitive (*ABC* is the same as *abc*).

# [Azure portal](#tab/certificateprofile-portal)

To create a certificate profile in the Azure portal:

1. In the Azure portal, go to your new Trusted Signing account.
1. On the Trusted Signing account Overview pane or on the Objects pane, select **Certificate Profile**.
1. On **Certificate Profiles**, select the certificate profile type in the dropdown menu.

    - Public identity validation is applicable to Public Trust and Public Trust Test.
    - Private identity validation is applicable to Private Trust and Private Trust CI Policy.
1. On **Create certificate profile**, provide the following information:

   1. For **Certificate Profile Name**, enter a unique name.

      For naming requirements, see [Naming constraints for certificate profiles](#naming-constraints-for-certificate-profiles).

      The value for **Certificate Type** is autopopulated based on your selection.
   1. For **Verified CN and O**, select an identity validation that must be displayed on the certificate.

      - If the street address must be displayed on the certificate, select the **Include street address** checkbox.
      - If the postal code must be displayed on the certificate, select the **Include postal code** checkbox.

      The values for the remaining fields are autopopulated based on your selection for **Verified CN and O**.

      A generated **Certificate Subject Preview** shows the preview of the certificate that will be issued.
   1. Select **Create**.

:::image type="content" source="media/trusted-signing-certificate-profile-creation.png" alt-text="Screenshot that shows the Create certificate profile pane." lightbox="media/trusted-signing-certificate-profile-creation.png":::

# [Azure CLI](#tab/certificateprofile-cli)

To create a certificate profile by using the Azure CL:

1. Create a certificate profile by using the following command:

   ```azurecli
   trustedsigning certificate-profile create -g MyResourceGroup --a
   account-name MyAccount -n MyProfile --profile-type PublicTrust --identity-validation-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   ```

   For naming requirements, see [Naming constraints for certificate profiles](#naming-constraints-for-certificate-profiles).

1. Create a certificate profile that includes optional fields (street address or postal code) in subject name of certificate using the following command:

   ```azurecli
   trustedsigning certificate-profile create -g MyResourceGroup --account-name MyAccount -n MyProfile --profile-type PublicTrust --identity-validation-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --include-street true
   ```

1. Verify that you successfully created a certificate profile by using the following command to get the Certificate Profile details:

   ```azurecli
   trustedsigning certificate-profile show -g myRG --account-name MyAccount -n  MyProfile
   ```

The following table lists *helpful commands* to use when you create a certificate profile by using the Azure CLI:

| Command                               | Description  |
| :----------------------------------- | :------------------- |
| `trustedsigning certificate-profile create -–help`                            | Show help for sample commands and show detailed parameter descriptions.              |
| `trustedsigning certificate-profile list -g MyResourceGroup --account-name MyAccount`                            |List all certificate profiles that are associated with a Trusted Signing account.          |
| `trustedsigning certificate-profile show -g MyResourceGroup --account-name MyAccount -n MyProfile`                            | Get details for a profile.              |

---

## Clean up resources

# [Azure portal](#tab/deleteresources-portal)

### Delete a certificate profile

1. In the Azure portal, go to your Trusted Signing account.
1. On the Trusted Signing account Overview pane or on the Objects pane, select **Certificate Profile**.
1. On **Certificate Profiles**, select the certificate profile that you want to delete.
1. Select **Delete**.

> [!NOTE]
> This action stops any signing that's associated with the corresponding certificate profiles.

### Delete a Trusted Signing account

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. In the search box, enter and then select **Trusted Signing account**.
1. In the Trusted Signing account section, select the Trusted Signing account that you want to delete.
1. Select **Delete**.

> [!NOTE]
> This action removes all certificate profiles that are linked to this account. Any signing processes that are associated with those certificate profiles stops.

# [Azure CLI](#tab/adeleteresources-cli)

### Delete a Trusted Signing account

To delete a Trusted Signing account, run this command:

```azurecli
trustedsigning delete -n MyAccount -g MyResourceGroup
```

> [!NOTE]
> This action removes all certificate profiles that are linked to this account. Any signing processes that are associated with those certificate profiles stops.

### Delete a certificate profile

To delete a Trusted Signing account, run this command:

```azurecli
trustedsigning certificate-profile delete -g MyResourceGroup --account-name MyAccount -n MyProfile
```

> [!NOTE]
> This action stops any signing that's associated with the corresponding certificate profiles.

---

## Related content

In this quickstart, you created a Trusted Signing account, an identity validation request, and a certificate profile. To learn more about Trusted Signing and to start your signing journey, explore the following articles:

- Learn more about [signing integrations](how-to-signing-integrations.md).
- Learn more about the [trust models that Trusted Signing supports](concept-trusted-signing-trust-models.md).
- Learn more about [certificate management](concept-trusted-signing-cert-management.md).
