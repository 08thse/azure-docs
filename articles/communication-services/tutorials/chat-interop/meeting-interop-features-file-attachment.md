---
title: Enable File Attachment Support in your Chat app
titleSuffix: An Azure Communication Services quickstart
description: In this tutorial, you learn how to enable file attachment interoperability with the Azure Communication Chat SDK.
author: jopeng
ms.author: jopeng
ms.date: 05/15/2023
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.custom: mode-other, devx-track-js
zone_pivot_groups: acs-interop-chat-tutorial-js-csharp
---

# Tutorial: Enable file attachment support in your Chat app

The Chat SDK is designed to work with Microsoft Teams seamlessly in the context of a meeting. File attachments can only be sent by a Teams user to an ACS user. Note that sending file attachments from Azure Communication Services user to Teams user isn't currently supported, see the current capabilities of [Teams Interop Chat](../../concepts/interop/guest/capabilities.md) for details.

## Add file attachment support

The Chat SDK provides `previewUrl` for each file attachment. Specifically, the `previewUrl` provides a link to a webpage on the SharePoint where the user can see the content of the file, edit the file and download the file if permission allows. 

You should be aware of couple constraints that come with this feature:

1. The Teams admin of the sender's tenant could impose policies that limits or disable this feature entirely. For example, the Teams admin could disable certain permissions (such as "Anyone") that could cause the file attachment URLs (`previewUrl`) to be inaccessible. 
2. We currently only support the following file permissions:
   - "Anyone," and
   - "People you choose" (with email address)

   The Teams user should be made aware of that all other permissions (such as "People in your organization") aren't supported. The Teams user should double check if the default permission is supported after uploading the file on their Teams client. 
3. The direct download URL (`url`) isn't supported.

In addition to regular files (with `AttachmentType` of `file`), the Chat SDK also provides the `AttachmentType` of `image` for image attachments so that you can use it to mirror the behavior of how Microsoft Teams client converts image attachment to inline images at the UI layer. See section "Image Attachment Handling" for more info. 

Images added via "Upload from this device" renders on Teams side, and Chat SDK would be returning such attachments as `image`. For images uploaded via "Attach cloud files" however, they would be treated as regular files on the Teams side, and therefore Chat SDK would be returning such attachments as `file`.

Also note that only files uploaded via "drag-and-drop" or via attachment menu of "Upload from this device" and "Attach cloud files" are supported. Some messages with embedded media (such as video clips, audio messages, weather cards, etc.) are adaptive card, which currently isn't supported.


::: zone pivot="programming-language-javascript"
[!INCLUDE [Teams File Attachment Interop with JavaScript SDK](./includes/meeting-interop-features-file-attachment-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp" 
[!INCLUDE [Teams File Attachment Interop with C# SDK](./includes/meeting-interop-features-file-attachment-csharp.md)]
::: zone-end



## Next steps

For more information, see the following articles:

- Learn more about [how you can enable inline image support](./meeting-interop-features-inline-image.md)
- Learn more about other [supported interoperability features](../../concepts/interop/guest/capabilities.md)
- Check out our [chat hero sample](../../samples/chat-hero-sample.md)
- Learn more about [how chat works](../../concepts/chat/concepts.md)
