---
title: How to manage suppression lists for a Azure Communication Email Domain using the Azure Portal
titleSuffix: An Azure Communication Services quick start guide.
description: Learn about managing suppression lists for a Azure Communication Email Domain using the Azure Portal
author: matthohn
manager: koagbakp
services: azure-communication-services
ms.author: matthohn
ms.date: 03/21/2024
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: devx-track-extended-java, devx-track-js, devx-track-python
zone_pivot_groups: acs-js-csharp-java-python
---

# Quickstart: How to manage suppression lists for a Azure Communication Email Domain using the Azure Portal

In this quick start, you will learn how to manage suppression lists for a Azure Communication Email Domain using the Azure Portal.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/dotnet/).
- An Azure Email Communication Services Resource ready to provision domains. [Get started creating an Email Communication Resource](../create-email-communication-resource.md).
- An [Azure Managed Domain](../add-azure-managed-domains.md) or [Custom Domain](../add-custom-verified-domains.md) provisioned and ready to send emails.

Once you have a domain provisioned, you are ready to start configuring your resource.

1. Navigate to the Domains blade of your EmailService resource.
:::image type="content" source="./media/manage-suppressionlist-provisioned-domains.png" alt-text="Screenshot that highlights an email service with a provisioned domain.":::

2. Click on the link for your provisioned domain, under the Domain name column.
3. Then navigate to the Suppression Lists blade of your domain resource.
:::image type="content" source="./media/manage-suppressionlist-blade.png" alt-text="Screenshot that highlights the Suppression List blade on a domains resource.":::

4. Select your desired domain from the dropdown list. You will see entry in the dropdown for each sender username that has been provisioned for the domains.  See here for more details on SenderUsernames [link to sender username docs].
:::image type="content" source="./media/manage-suppressionlist-select-sender-domain.png" alt-text="Screenshot that highlights the dropdown list of mailfrom email addresses.":::

5. Once you have chosen a sender username to create the list for, either click the Add button or the Create new Suppression List button.
:::image type="content" source="./media/manage-suppressionlist-add-new-list.png" alt-text="Screenshot that highlights the options to add a new suppression list for a mail from address.":::

6. Once you have a SuppressionList created, you will see options to add a new recipient or upload an CSV of contacts.
:::image type="content" source="./media/manage-suppressionlist-add-recipients.png" alt-text="Screenshot that highlights the options to add a recipients to a suppression.":::

7. Adding a single user will show the Add Recipient flyout. Here you can enter the recipients email address and some basic contact information.
:::image type="content" source="./media/manage-suppressionlist-add-single-recipient-flyout.png" alt-text="Screenshot that highlights the Add Recipient flayout.":::

8. If you do not want to add a single recipient at a time, you can choose the upload CSV option.  When you click on Upload CSV File, the import csv flyout will be shown.  Here you can select a CSV file from your machine.
:::image type="content" source="./media/manage-suppressionlist-import-csv.png" alt-text="Screenshot that highlights the import CSV flayout.":::

9. The CSV will be checked for conflcts
:::image type="content" source="./media/manage-suppressionlist-import-csv-conflicts.png" alt-text="Screenshot that highlights the import CSV flayout.":::

10. Finally, confirm the import and conflicts are resolved.
:::image type="content" source="./media/manage-suppressionlist-import-csv-confirm.png" alt-text="Screenshot that highlights the import CSV flayout.":::

11. Recipients can be exported, edited or removed.
:::image type="content" source="./media/manage-suppressionlist-recipients-view.png" alt-text="Screenshot that highlights the suppression list recipients view.":::

12. Recipients can be editied and deleted from the Edit Recipient flyout.
:::image type="content" source="./media/manage-suppressionlist-edit-recipient.png" alt-text="Screenshot that highlights the edit recipient flayout.":::

## Next steps

- [Quickstart: Manage Domain Suppression Lists in Azure Communication Services using the Management Client Libraries](./manage-suppression-list-mgmt-sdks.md)
- [Send Mail with Azure Communication Services](./send-email.md)
