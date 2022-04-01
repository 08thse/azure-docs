---
title: Email Communication Service overview for Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Learn about the Azure Communication Services Email Communication Resources and Domains.
author: bashan
manager: shanhen
services: azure-communication-services

ms.author: bashan
ms.date: 02/15/2022
ms.topic: overview
ms.service: azure-communication-services
ms.custom: private_preview
---
# Email Communication Services
> [!IMPORTANT]
> Functionality described on this document is currently in private preview. Private preview includes access to SDKs and documentation for testing purposes that are not yet available publicly.
> Apply to become an early adopter by filling out the form for [preview access to Azure Communication Services](https://aka.ms/ACS-EarlyAdopter).

Similar to Chat, VOIP and SMS modalities under the Azure Communication Services , you will be able to send an email using an Azure Communication Resource. However
sending an email requires certain preconfiguration steps and you have to rely on your organization admins help setting that up. The administartor of your organization need to, 
- Approve the domain that you organization allows you to send mail from 
- Define the sender domain they will use as the P1 sender email address (also known as MailFrom email address) that shows up on the envelope of the email [RFC 5321](https://tools.ietf.org/html/rfc5321)
- Define the P2 sender email address that most email recipients will see on their email client [RFC 5322](https://tools.ietf.org/html/rfc5322). 
- Setup and verify the sender domain by adding necessary DNS records for sender verification to succeed.

Once the sender domain is succeffully configured correctly and verified you will able to link the verified domains with your Azure Communication Services resource and start sending emails.
 
One of the key prinicipals for azure communication services is to have a simplified developer experience. Our email platform will simplify the expereicne for developers and ease this back and forth operations with organization administartors and improve the end to end expirence by allowing admin developers to configure the necessary sender authentication and other compliance releated steps to send email and letting you focus on building the required paylod.

Your Azure Administartors will create a new resource of type “Email Communication Services” and add the allowed email sender domains under this resource. The domains added under this resource type will contain all the sender authentication and engagment tracking configurations that are required to be completed before start sending emails. Once the sender domain is configured and verified you  will able to link these domains with your Azure Communication Services resource and you can select which  of the verified domains is suitable for your application and connect them to send emails from your application.  


 ## Organziation Admins \ Admin Devlopers Responsibility 

- Plan all the requried Email Domains for the applications in the organization
- Add Custom domains or get an azure managed domain.
- Perform the Sender Verification Steps for custom domains
- Setup DMARC Policy for the verified Sender Domains.

## Devlopers Responsibility 
- Connect the preferred domains to Azure Communication Service resources.
- Responsible for generating email payload and define the required
  - Email headers 
  - Body of email
  - Recipient list
  - Attachments if any
- Submits to Communication Services Email API.
- Verify the Status of Email Delivery.

## Next steps

> [Understanding Email Domains in Azure Communication Services](./Understanding-email-domain-setup.md)

> [Get started with Creating Email Communication Resource](../../quickstarts/Email/create-email-communication-resource.md)

> [Get started by Connecting Email Resource with a Communication Resource](../../quickstarts/Email/connect-email-communication-acs-resource.md)


The following documents may be interesting to you:

- Familiarize yourself with the [Email client library](../Email/sdk-features.md)
- How to send emails with custom verified domains?[Add custom domains](../../quickstarts/Email/add-custom-verified-domains.md)
- How to send emails with Azure Communication Service managed domains?[Add Azure Managed domains](../../quickstarts/Email/add-azure-managed-domains.md)


