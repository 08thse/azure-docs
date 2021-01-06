---
title: Delete an Azure Automation Run As account
description: This article tells how to delete a Run As account with PowerShell or from the Azure portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/06/2021
ms.topic: conceptual
---

# Delete an Azure Automation Run As account

Run As accounts in Azure Automation provide authentication for managing resources on the Azure Resource Manager or Azure Classic deployment model using Automation runbooks and other Automation features. This article describes how to delete a Run As or Classic Run As account. When you perform this action, the Automation account is retained. After you delete the Run As account, you can re-create it in the Azure portal or with the provided PowerShell script.

To learn more about Azure Automation account authentication and guidance related to process automation scenarios, see [Automation Account authentication overview](automation-security-overview.md).

## Delete a Run As or Classic Run As account

1. In the Azure portal, open the Automation account.

2. In the left pane, select **Run As Accounts** in the account settings section.

3. On the Run As Accounts properties page, select either the Run As account or Classic Run As account that you want to delete.

4. On the Properties pane for the selected account, click **Delete**.

   ![Delete Run As account](media/manage-runas-account/automation-account-delete-runas.png)

5. While the account is being deleted, you can track the progress under **Notifications** from the menu.

## Next steps

To recreate your Run As or Classic Run As account, see [Create Run As accounts](create-runas-account.md).