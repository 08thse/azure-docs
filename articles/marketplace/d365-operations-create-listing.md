---
title: Configure Dynamics 365 for Operations offer listing details on Azure Marketplace
description: Learn how to configure virtual machine offer listing details on Azure Marketplace.
ms.service: marketplace 
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
---

# How to configure D365 for Operations offer listing details

<strike>This page displays the languages in which your offer will be listed. Currently, **English (United States)** is the only available option.</strike>On the **Offer listing** page (select from the left-nav menu in Partner Center), define the offer details such as offer name, description, links, and contacts.

> [!NOTE]
> Provide offer listing details in one language only. It is not required to be in English, as long as the offer description begins with the phrase, "This application is available only in [non-English language]." It is also acceptable to provide a *Useful link URL* to offer content in a language other than the one used in the Offer listing content.

Here's an example of how offer information appears in Microsoft AppSource (any listed prices are for example purposes only and not intended to reflect actual costs):

:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="Illustrates how this offer appears in Microsoft AppSource.":::

#### Call-out descriptions

1. Logo
2. Products
3. Categories
4. Industries
5. Support address (link)
6. Terms of use
7. Privacy policy
8. Offer name
9. Description
10. Screenshots/videos

## Marketplace details

### Name

The name you enter here is shown to customers as the title of your offer listing. This field is auto-filled with the name that you entered in the **Offer alias** box when you created the offer, but you can change this value. The name:

- Can include trademark and copyright symbols.
- Must be 50 characters or less.
- Can't include emojis.

### Search results summary

Provide a short description of your offer to display in Azure Marketplace search results. It can contain up to 100 characters.

### Description

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Use HTML tags to format your description so it's more engaging. For a list of allowed tags, see [Supported HTML tags](supported-html-tags.md).

### Search keywords

You can optionally enter up to three search keywords to help customers find your offer in the marketplace. For best results, also use these keywords in your description.

### Products your app works with

If you want to let customers know that your app works with specific products, enter up to three product names here.

#### Help/Privacy URLs

Enter a **Help link for your app** where customers can learn more about your offer. Your Help URL cannot be the same as your Support URL.

Enter a **Privacy policy link** to your organization's privacy policy. You are responsible for ensuring your app complies with privacy laws and regulations, and for providing a valid privacy policy.

### Contact information

Provide the name, email, and phone number for a **Support contact** and an **Engineering contact**. This information is not shown to customers, but will be available to Microsoft, and may be provided to CSP partners.

In the **Support contact** section, provide the **Support URL** where CSP partners can find support for your offer. Your Support URL cannot be the same as your Help URL.

## Supporting documents

Provide at least one (and up to three) related marketing documents here, such as white papers, brochures, checklists, or presentations, in PDF format.

## Marketplace media

Provide logos and images to use with your offer. All images must be in PNG format. Blurry images will cause your submission to be rejected.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>If you have an issue uploading files, ensure that your local network doesn't block the https://upload.xboxlive.com service that's used by Partner Center.

### Logos

Provide a PNG file for the **Large** size logo. Partner Center will use this to create a **Small** logo. You can optionally replace this with a different image later.

- **Large** (from 216 x 216 to 350 x 350 px, required)
- **Small** (48 x 48 px, optional)

These logos are used in different places in the listing:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

>[!Note]
>If you have an issue uploading files, make sure your local network does not block the https://upload.xboxlive.com service used by Partner Center.

### Screenshots

Add up to five screenshots (one is required) that show how your offer works. Each screenshot must be 1280 x 720 pixels in size and in PNG format. Each screenshot must include a caption.

### Videos

Add up to five videos that demonstrate your offer. The videos should be hosted on an external video service. Enter each video's name, web address, and a thumbnail PNG image of the video at 1280 x 720 pixels.

For additional marketplace listing resources, see [Best practices for marketplace offer listings](gtm-offer-listing-best-practices.md).

Select **Save draft** before continuing to the next tab in the left-nav menu, **Availability**.

## Next steps

- [Set offer availability](d365-operations-create-availability.md)
