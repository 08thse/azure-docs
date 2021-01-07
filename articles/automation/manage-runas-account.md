---
title: Manage an Azure Automation Run As account
description: This article tells how to manage your Run As account with PowerShell or from the Azure portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/06/2021
ms.topic: conceptual
---

# Manage an Azure Automation Run As account

Run As accounts in Azure Automation provide authentication for managing resources on the Azure Resource Manager or Azure Classic deployment model using Automation runbooks and other Automation features. This article provides guidance on how to manage a Run As or Classic Run As account.

To learn more about Azure Automation account authentication and guidance related to process automation scenarios, see [Automation Account authentication overview](automation-security-overview.md).

## <a name="cert-renewal"></a>Renew a self-signed certificate

The self-signed certificate that you have created for the Run As account expires one year from the date of creation. At some point before your Run As account expires, you must renew the certificate. You can renew it any time before it expires.

When you renew the self-signed certificate, the current valid certificate is retained to ensure that any runbooks that are queued up or actively running, and that authenticate with the Run As account, aren't negatively affected. The certificate remains valid until its expiration date.

>[!NOTE]
>If you think that the Run As account has been compromised, you can delete and re-create the self-signed certificate.

>[!NOTE]
>If you have configured your Run As account to use a certificate issued by your enterprise certificate authority and you use the option to renew a self-signed certificate option, the enterprise certificate is replaced by a self-signed certificate.

Use the following steps to renew the self-signed certificate.

1. In the Azure portal, open the Automation account.

1. Select **Run As Accounts** in the account settings section.

        :::image type="content" source="manage-runas-account/automation-account-properties-pane.png" alt-text="Automation account properties pane.":::

1. On the Run As Accounts properties page, select either the Run As account or the Classic Run As account for which to renew the certificate.

1. On the properties pane for the selected account, click **Renew certificate**.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Renew certificate for Run As account.":::

1. While the certificate is being renewed, you can track the progress under **Notifications** from the menu.

## Limit Run As account permissions

To control the targeting of Automation against resources in Azure, you can run the [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) script. This script changes your existing Run As account service principal to create and use a custom role definition. The role has permissions for all resources except [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>After you run the **Update-AutomationRunAsAccountRoleAssignments.ps1** script, runbooks that access Key Vault through the use of Run As accounts no longer work. Before running the script, you should review runbooks in your account for calls to Azure Key Vault. To enable access to Key Vault from Azure Automation runbooks, you must [add the Run As account to Key Vault's permissions](#add-permissions-to-key-vault).

If you need to restrict,  further what the Run As service principal can do, you can add other resource types to the `NotActions` element of the custom role definition. The following example restricts access to `Microsoft.Compute/*`. If you add this resource type to `NotActions` for the role definition, the role will not be able to access any Compute resource. To learn more about role definitions, see [Understand role definitions for Azure resources](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

You can determine if the service principal used by your Run As account is in the Contributor role definition or a custom one.

1. Go to your Automation account and select **Run As Accounts** in the account settings section.
2. Select **Azure Run As Account**.
3. Select **Role** to locate the role definition that is being used.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Verify the Run As Account role." lightbox="media/manage-runas-account/verify-role-expanded.png":::

You can also determine the role definition used by the Run As accounts for multiple subscriptions or Automation accounts. Do this by using the [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) script in the PowerShell Gallery.

### Add permissions to Key Vault

You can allow Azure Automation to verify if Key Vault and your Run As account service principal are using a custom role definition. You must:

* Grant permissions to Key Vault.
* Set the access policy.

You can use the [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) script in the PowerShell Gallery to give your Run As account permissions to Key Vault. See [Assign a Key Vault access policy](../key-vault/general/assign-access-policy-powershell.md) for more details on setting permissions on Key Vault.

## Resolve misconfiguration issues for Run As accounts

Some configuration items necessary for a Run As or Classic Run As account might have been deleted or created improperly during initial setup. Possible instances of misconfiguration include:

* Certificate asset
* Connection asset
* Run As account removed from the Contributor role
* Service principal or application in Azure AD

For such misconfiguration instances, the Automation account detects the changes and displays a status of *Incomplete* on the Run As Accounts properties pane for the account.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Incomplete Run As account configuration.":::

When you select the Run As account, the account properties pane displays the following error message:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

You can quickly resolve these Run As account issues by [deleting](delete-runas-account.md) and [re-creating](create-runas-account.md) the Run As account.

## Next steps

* [Application Objects and Service Principal Objects](../active-directory/develop/app-objects-and-service-principals.md).
* [Certificates overview for Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
* To create or re-create a Run As account, see [Create a Run As account](create-runas-account.md).
* If you no longer need to use a Run As account, see [Delete a Run As account](delete-runas-account.md).