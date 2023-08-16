---
title: Tutorial - Access and customize the developer portal - Azure API Management | Microsoft Docs
description: Follow this tutorial to learn how to customize the API Management developer portal, an automatically generated, fully customizable website with the documentation of your APIs. 
services: api-management
author: dlepow

ms.service: api-management
ms.topic: tutorial
ms.date: 08/16/2023
ms.author: danlep
ms.custom: engagement-fy23
---

# Tutorial: Access and customize the developer portal

In this tutorial, you will get started with customizing the API Management *developer portal*. The developer portal is an automatically generated, fully customizable website with the documentation of your APIs. It's where API consumers can discover your APIs, learn how to use them, and request access.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Access the managed version of the developer portal
> * Navigate its administrative interface
> * Customize the content
> * Publish the changes
> * View the published portal

You can find more details on the developer portal in the [Azure API Management developer portal overview](api-management-howto-developer-portal.md).

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="Screenshot of the API Management developer portal - administrator mode." border="false":::

## Prerequisites

- Complete the following quickstart: [Create an Azure API Management instance](get-started-create-service-instance.md)
- Import and publish an API. For more information, see [Import and publish](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## Access the portal as an administrator

Follow the steps below to access the managed version of the portal.

1. In the [Azure portal](https://portal.azure.com), navigate to your API Management instance.
1. Select the **Developer portal** button in the top navigation bar. A new browser tab with an administrative version of the portal will open.

## Understand the portal's administrative interface

[!INCLUDE [api-management-developer-portal-editor](../../includes/api-management-developer-portal-editor.md)]

## Add an image to the media library

You'll usually want to use your own images and other media content in the developer portal to reflect your organization's branding. If an image that you want to use isn't already in the portal's media library, you can add it in the developer portal:

1. In the left menu of the visual editor, select **Media**.
1. Do one of the following:
    * Select **Upload file** and select a local image file on your computer.
    * Select **Link file**. Enter a **Reference URL** to the image file and other details. Then select **Download**.
1. Select **Close** to exit the media library.

> [!TIP]
> You can also add an image to the media library by dragging and dropping it directly in the visual editor window.

## Replace the default logo on the home page

1. In the developer portal, select the default **Contoso** logo in the top left of the navigation bar. 
1. Select the **Edit** icon. 
1. Under the **Main** section, select **Source**.
1. In the **Media** pop-up, either select one of the following:
    * An image already uploaded in your media library, or
    * **Upload file** to upload a new image file to your media file that you can select
    * **None** if you don't want to use a logo.
1. The logo updates in real time.
1. Select outside the pop-up windows to exit the media library.
1. Select **Save**.

## Edit content on the home page

The default **Home** page and other pages are filled with placeholder text and other images. You can either remove entire sections containing this content or keep the structure and adjust the elements one by one. Replace the generated text and images with your own and make sure amy links point to desired locations. You can edit the structure and content of the generated pages in several ways. For example:
* Select existing text and heading elements to edit and format content.
* Add a section to a page by hovering over a blank area then click on a blue icon with a plus sign.
    :::image type="content" source="media/api-management-howto-developer-portal-customize/add-section.png" alt-text="Screenshot showing the add section icon in the developer portal.":::

* Add a widget (for example, text, image, custom widget, or APIs list) by hovering over a blank area, then click a grey icon with a plus sign.
    :::image type="content" source="media/api-management-howto-developer-portal-customize/add-widget.png" alt-text="Screenshot showing the add widget icon in the developer portal.":::

* Drag and drop page elements to your desired placement.
* Verify your buttons point to the right locations.

For example, you might want 
## Edit the site's primary color

You can change colors, gradients, typography, buttons, and other user interface elements in the developer portal by editing site styles. The primary color is used for the navigation bar, buttons, and other elements. You can change the primary color to match your organization's branding.

In the developer portal, in the left menu of the visual editor, select **Styles**. 
1. Select the **Edit** icon. 
1. Under the **Colors** section, select the color style item you want to edit. For example, select **Primary**.
1. Select **Edit color**..
1. Select the color from the color-picker, or enter the hex color code.
1. Select **Save**.

The updated color is applied to the site in real time.

> [!TIP]
> If you want to, add and name another color item by selecting **+ Add color** on the **Styles** page.

## Change the background image on the home page

You can change the background on your home page to an image or color that matches your organization's branding. If you haven't already uploaded a different image to the media library, you can do it before changing the background image, or when you change the background image.

1. On the home page of the developer portal, click in the top right corner so that the top section is highlighted at the corners and a pop-up menu appears.
1. To the right of **Edit article** in the pop-up menu, select the up-down arrow (**Switch to parent**). 
<!-- Add image -->
1. Select **Edit section**.
1. In the **Section** pop-up, under **Background**, select one of the icons:

    :::image type="content" source="media/api-management-howto-developer-portal-customize/background.png" alt-text="Screenshot of background settings in the developer portal.":::
    * **Clear background**, to remove a background image
    * **Background image**, to select an image from the media library or upload a new image
    * **Background color**, to select a color from the color picker, or clear a color
    * **Background gradient**, to select a gradient from your site styles page, or clear a gradient
1. Select **Save**.

## Change the default layout

The developer portal uses *layouts* to define common content elements such as navigation bars and footers on groups of related pages. Each page is automatically matched with a layout based on a URL template. By default, the developer portal comes with two layouts, **Home** and **Default**. The **Home** layout is used for the home page (URL template `/`), and the **Default** layout is used for all other pages (URL template 
`/*`). You can change the layout for any page in the developer portal and define new layouts to apply to pages that match other URL templates.

For example, you change the logo that's used in the navigation bar of the Default layout to match your organization's branding.

1. In the left menu of the visual editor, select **Pages**.
1. Select the **Layouts** tab, and select **Default**.
1. Select the picture of the logo in the upper left corner.
1. Under the **Main** section, select **Source**.
1. In the **Media** pop-up, either select one of the following:
    * An image already uploaded in your media library, or
    * **Upload file** to upload a new image file to your media file that you can select
    * **None** if you don't want to use a logo.
1. The logo updates in real time.
1. Select outside the pop-up windows to exit the media library.
1. Select **Save**.

## Edit navigation menus

You can edit the navigation menus at the top of the developer portal pages to change the order of menu items, add new menu items, and remove menu items. You can also change the name of menu items and the URL or other content they point to.

The **Default** and **Home** layouts the developer portal pages show a main menu with links to **Home**, **APIs**, and **Products**, and an anonymous user menu with links to **Sign in** and **Sign up** pages. However, you might want to customize them. For example, if you want to independently invite users to your site, you might want to disable the **Sign up** link in the anonymous user menu.
<!--Add screenshot of nav items-->

1. In the left menu of the visual editor, select **Site menu**.
1. On the left, expand **Anonymous user menu**.
1. Select **Sign up**, and select **Delete**. To confirm your choice, select **Yes**.

## Edit site settings

You can edit the site settings for the developer portal to change the site name, description, and other details. 

1. In the left menu of the visual edit, select **Settings**.
1. In the **Settings** pop-up, enter the site metadata you want to change. Optionally, setup a favicon for the site from an image in your media library.
1. Select **Save**.

> [!TIP]
> You can also change the site's domain name, but you must first set up a custom domain in your API Management instance. [Learn more about custom domain names](configure-custom-domain.md) in API Management.


## Publish the portal

To make your portal and its latest changes available to visitors, you need to *publish* it. Select **Publish site** within the portal's administrative interface. You can also publish the site from the Azure portal. On the **Portal overview** page of your API Management instance in the Azure portal, select **Publish**. 

Publishing the portal can take a few minutes to complete.



## Visit the published portal

After you publish the portal, you can access it at the same URL as the administrative panel, for example `https://contoso-api.developer.azure-api.net`. View it in a separate browser session (using incognito or private browsing mode) as an external visitor.

## Apply the CORS policy on APIs

To let the visitors of your portal test the APIs through the built-in interactive console, enable CORS (cross-origin resource sharing) on your APIs, if you haven't already done so. On the **Portal overview** page of your API Management instance in the Azure portal, select **Enable CORS**. [Learn more](enable-cors-developer-portal.md).
## Next steps

Learn more about the developer portal:

- [Azure API Management developer portal overview](api-management-howto-developer-portal.md)
- [Migrate to the new developer portal](developer-portal-deprecated-migration.md) from the deprecated legacy portal.
- Configure authentication to the developer portal with [usernames and passwords](developer-portal-basic-authentication.md), [Azure AD](api-management-howto-aad.md), or [Azure AD B2C](api-management-howto-aad-b2c.md).
- Learn more about [customizing and extending](developer-portal-extend-custom-functionality.md) the functionality of the developer portal.
