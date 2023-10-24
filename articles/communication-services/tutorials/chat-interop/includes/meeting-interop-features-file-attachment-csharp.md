---
title: Tutorial - Enable File Attachment Support
author: palatter
ms.author: palatter
ms.date: oct 20, 2023
ms.topic: include
ms.service: azure-communication-services
---

In this tutorial, you learn how to enable file attachment support using the Azure Communication Services Chat SDK for C#.

## Sample code
Find the finalized code of this tutorial on [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/join-chat-to-teams-meeting).

## Prerequisites 

* You've gone through the quickstart - [Join your chat app to a Teams meeting](../../../quickstarts/chat/meeting-interop.md). 
* Create an Azure Communication Services resource. For details, see [Create an Azure Communication Services resource](../../../quickstarts/create-communication-resource.md). You need to **record your connection string** for this tutorial.
* You've set up a Teams meeting using your business account and have the meeting URL ready.
* You're using the Chat SDK for C# (Azure.Communication.Chat) 1.3.0 or the latest. See [here](https://www.nuget.org/packages/Azure.Communication.Chat/).

## Goal

1. Be able to render file attachment in the message thread. Each file attachment card has an "Open" button.
2. Be able to render image attachments as inline images.

## Handle file attachments

The Chat SDK for JavaScript would return `AttachmentType` of `file` for regular files and `image` for image attachments.

```c#
public class ChatAttachment
{
    public ChatAttachment(string id, AttachmentType attachmentType) { }
    public AttachmentType AttachmentType { get }
    public string Extension { get }
    public string Id { get }
    public string Name { get }
    public System.Uri PreviewUrl { get }
    public System.Uri Url { get }
}

public struct AttachmentType : System.IEquatable<AttachmentType>
{
    public AttachmentType(string value) { }
    public static Azure.Communication.Chat.AttachmentType File { get }
    public static Azure.Communication.Chat.AttachmentType Image { get }
}
```

As an example, the following JSON is an example of what `ChatAttachment` might look like for an image attachment and a file attachment:

```json
"attachments": [
    {
        "id": "08a182fe-0b29-443e-8d7f-8896bc1908a2",
        "attachmentType": "file",
        "extension": "pdf",
        "name": "business report.pdf",
        "url": "",
        "previewUrl": "https://contoso.sharepoint.com/:u:/g/user/h8jTwB0Zl1AY"
    },
    {
        "id": "9d89acb2-c4e4-4cab-b94a-7c12a61afe30",
        "attachmentType": "image",
        "extension": "png",
        "name": "Screenshot.png",
        "url": "https://contoso.communication.azure.com/chat/threads/19:9d89acb29d89acb2@thread.v2/images/9d89acb2-c4e4-4cab-b94a-7c12a61afe30/views/original?api-version=2023-11-03",
        "previewUrl": "https://contoso.communication.azure.com/chat/threads/19:9d89acb29d89acb2@thread.v2/images/9d89acb2-c4e4-4cab-b94a-7c12a61afe30/views/small?api-version=2023-11-03"
      }
]
```

Now let's go back to event handler we have created in previous [quickstart](../../../quickstarts/chat/meeting-interop.md) to add some extra logic to handle attachments with `attachmentType` of `file`: 

```C#
INSERT CODE HERE
```

Let's make sure we add some CSS for the attachment card as well:

INSERT FORMATTING HERE

That's it all we need for handling file attachments. Next let's run the code.

## Run the code 

Webpack users can use the `webpack-dev-server` to build and run your app. Run the following command to bundle your application host on a local webserver:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

or

```console
npm start
```

## File attachment demo

Open your browser and navigate to `<TODO>`. Enter the meeting URL and the thread ID.

Now let's send some file attachments from Teams client like this:

:::image type="content" source="<TODO>" alt-text="A screenshot of Teams client shown a sent message with three file attachments.":::

Then you should see the new message being rendered along with file attachments:

:::image type="content" source="<TODO>" alt-text="A screenshot of sample app shown a received incoming message with three file attachments.":::


## Handle image attachments

In addition to regular files, image attachment needs to be treated differently. As we wrote in the beginning, the image attachment has `attachmentType` of `image`, which requires the communication token to retrieve the preview image and full scale image.

Before we go any further, make sure you have gone through the tutorial that demonstrates [how you can enable inline image support in your chat app](../meeting-interop-features-inline-image.md). To summary, fetching images require a communication token in the request header. Upon getting the image blob, we need to create an `ObjectUrl` that points to this blob. Then we inject this URL to `src` attribute of each inline image.

Now you're familiar with how inline images work and it's easy to render image attachments just like a regular inline image. 

Firstly, we inject an image tag to message content whenever there's an image attachment:

```js
<TODO>
```

Now let's borrow `fetchPreviewImages()` from the [tutorial](../meeting-interop-features-inline-image.md) and we should be able to use it as is without any changes:

```js
<TODO>
```
This function needs a `tokenString` so we need to have a global copy of it and initialize in `init()` as demonstrated in the following code snippet:

```js
<TODO>
```

That's it! Now we have added image attachment support as well. Now let's run the code and see it in action!


## Image attachment demo

Now let's send some image attachments from Teams client like this:

:::image type="content" source="<TODO>" alt-text="A screenshot of Teams client shown a send box with an image attachment uploaded.":::

Upon sending the image attachment, you notice that it becomes an inline image on the Teams client side:

:::image type="content" source="<TODO>" alt-text="A screenshot of Teams client shown a message with the image attachment sent to the other participant.":::

Let's go back to our sample app, the same image should be rendered as well:

:::image type="content" source="<TODO>" alt-text="A screenshot of sample app shown an incoming message with one inline image rendered.":::
