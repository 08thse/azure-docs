---
title: How to add custom verified domains to Email Communication Services
titleSuffix: An Azure Communication Services quick start guide
description: Learn about adding Custom domains for Email Communication Services.
author: bashan-git
manager: sphenry
services: azure-communication-services
ms.author: bashan
ms.date: 04/15/2022
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: private_preview
---
# Quickstart: How to add custom verified domains to Email Communication Services

> [!IMPORTANT]
> Functionality described on this document is currently in private preview. Private preview includes access to SDKs and documentation for testing purposes that are not yet available publicly.
> Apply to become an early adopter by filling out the form for [preview access to Azure Communication Services](https://aka.ms/ACS-EarlyAdopter).
## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/dotnet/).
- An Azure Email Communication Services Resource created and ready to provision the domains [Get started with Creating Email Communication Resource](../../quickstarts/email/create-email-communication-resource.md)

## Provision Custom Domain


### Verify Custom Domain

1. Go the overview page of the Email Communications Service resource that you created earlier.
2. Setup Custom Domain.   
    - (Option 1) Click the **Setup** button under **Setup a custom domain**. Move to the next step.


      :::image type="content" source="./media/email-domains-custom.png" alt-text="Setup Custom Domain":::

    - (Option 2) Click **Provision Domains** on the left navigation panel.
    
        :::image type="content" source="./media/email-domains-custom-nav.png" alt-text="Navigate to Provision Domains":::

    - Click **Add domain** on the upper navigation bar.
    - Select **Custom domain** from the dropdown.
3. You will be navigating to "Add a custom Domain". 
4. Enter  your "Domain Name" and re enter domain name
5. Click **Confirm**.   

    :::image type="content" source="./media/email-domains-custom-add.png" alt-text="Enter the Custom Domain":::
6. Please ensure that domain name is not misspelled or click edit to correct the domain name and confirm.
7. Click **Add**.

    :::image type="content" source="./media/email-domains-custom-add-confirm.png" alt-text="Add a Custom Domain":::

8. This will create custom domain configuration for your domain

    :::image type="content" source="./media/email-domains-custom-add-progress.png" alt-text="Custom Domain Deployment in Progress":::

9. You can verify the ownership of the domain by clicking **Verify Domain** 

    :::image type="content" source="./media/email-domains-custom-added.png" alt-text="Custom Domain Added":::.

10. If you would like to resume the verification later you can click **Close** and resume the verification from **Provision Domains** by clicking **Configure** link

    :::image type="content" source="./media/email-domains-custom-configure.png" alt-text="Provisioned Domains":::
11. Clicking **Verify Domain** or **Configure** will navigate to "Verify Domain via TXT record" to follow. 

    :::image type="content" source="./media/email-domains-custom-verify.png" alt-text="Verify Domain ownership":::

12. You need add the above TXT record to your domain's registrar or DNS hosting provider. Click **Next** once you've completed this step. 

    :::image type="content" source="./media/email-domains-custom-verify-done.png" alt-text="Getting DNS TXT record to verify":::
13. Verify that TXT record is created successfully in your DNS and Click **Done**. 
14. DNS changes will take up to 15 to 30 minutes.  Click **Close**. 

    :::image type="content" source="./media/email-domains-custom-verify-progress.png" alt-text="Verification in progress for Sender Authentication":::
15. Once your domain is verified, you can setup your SPF, DKIM, and DMARC records to authenticate your domains. 

    :::image type="content" source="./media/email-domains-custom-verified.png" alt-text="Domain Verified":::


### Configure Sender Authentication for  Custom Domain
1. Navigate to  **Provision Domains** and confirm that  **Domain Status** is in "Verified" state. 
2. You can setup SPF and DKIM  by clicking **Configure**. You need add the following TXT record and CNAME records to your domain's registrar or DNS hosting provider. Click **Next** once you've completed this step. 

    :::image type="content" source="./media/email-domains-custom-senderauth-spf.png" alt-text="Sender Authentication Setup":::

    :::image type="content" source="./media/email-domains-custom-senderauth-dkim1.png" alt-text="SPF Records":::

    :::image type="content" source="./media/email-domains-custom-senderauth-dkim2.png" alt-text="DKIM Records":::

3. Verify that TXT and CNAME records are created successfully in your DNS and Click **Done**.
 
    :::image type="content" source="./media/email-domains-custom-senderauth-verify.png" alt-text="DNS Records for SPF and DKIM":::

4. DNS changes will take up to 15 to 30 minutes.  Click **Close**.

    :::image type="content" source="./media/email-domains-custom-senderauth-verify-progress.png" alt-text="Verification in progress":::
    
5. Wait for Verification to complete. You can check the Verification Status from **Provision Domains** page. 

    :::image type="content" source="./media/email-domains-custom-verificationstatus.png" alt-text="Verification done shown in Provisioned Domains":::
 
6. Once your sender authentication configurations are successfully verified, your email domain will be ready to send emails using custom domain.

   :::image type="content" source="./media/email-domains-custom-ready.png" alt-text="Domain is ready to Send Email":::

## Changing MailFrom and From Display Name for Custom Domains

When Azure Manged Domain is provisioned to send mail, it has default Mail From address as donotreply@xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurecomm.net and the FROM display name would be the same. You will able to configure and change the MailFrom address and FROM displayname to more user friendly value.

1. Go the overview page of the Email Communications Service resource that you created earlier.
2. Click **Provision Domains** on the left navigation panel. You will be see list of provisioned domains.
3. Click on the Custom Domain name that you would like to update.

    :::image type="content" source="./media/email-domains-custom-provision-domains.png" alt-text="Click Custom Domain in provisioned Domains":::

4. The navigation lands in Domain Overview page where you will able to see Mailfrom and From attributes.
 
    :::image type="content" source="./media/email-domains-custom-overview.png" alt-text="Navigate to Custom Domains Overview":::

5. Click on edit link on MailFrom 

    :::image type="content" source="./media/email-domains-custom-mailfrom.png" alt-text="Edit MailFrom and Display name":::

6. You will able to modify the Display Name and MailFrom address. 

    :::image type="content" source="./media/email-domains-custom-mailfrom-change.png" alt-text="Changes MailFrom and Display name":::

7. Click **Save**. You will see the updated values in the overview page. 

    :::image type="content" source="./media/email-domains-overview-updated.png" alt-text="Save changes":::

**Your email domain is now ready to send emails.**

## Next steps

> [Get started by Connecting Email Resource with a Communication Resource](../../quickstarts/email/connect-email-communication-acs-resource.md)

> [Best Practices for Sender Authentication Support in Azure Communication Services Email](../../concepts/email/email-authentication-bestpractice.md)


The following documents may be interesting to you:

- Familiarize yourself with the [Email client library](../../concepts/email/sdk-features.md)
