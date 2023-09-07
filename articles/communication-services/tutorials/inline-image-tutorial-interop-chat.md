---
title: Enable inline image using UI Library in Teams Interoperability Chat
titleSuffix: An Azure Communication Services tutorial
description: Learn how to use the UI Library to enable inline image support in Teams Interoperability Chat
author: jpeng-ms
services: azure-communication-services
ms.author: jopeng
ms.date: 08/03/2023
ms.topic: tutorial
ms.service: azure-communication-services
ms.subservice: chat
---

# Enable inline image using UI Library in Teams Interoperability Chat

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

In a Teams Interopability Chat ("Interop Chat"), we can enable communication users to receive inline images sent by Teams users. Please note, letting communication users to send inline images to Teams users are currently not supported. To learn more about what current is supported by the UI library, please refer to [UI library Use Cases](../concepts/ui-library/includes/web-ui-use-cases.md) for more information.

>[!IMPORTANT]
>
>Inline image feature comes with the CallWithChat Composite without additional setups. 
>
>Currently, the Communication user is able to only receive inline images from the Teams user. Please refer to [UI Library Use Cases](../concepts/ui-library/includes/web-ui-use-cases.md) to learn more.


## Download code

Access the code for this tutorial on [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/ui-library-quickstart-teams-interop-meeting-chat).

## Prerequisites

- An Azure account with an active subscription. For details, see [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio Code](https://code.visualstudio.com/) on one of the [supported platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
- [Node.js](https://nodejs.org/), Active LTS and Maintenance LTS versions (10.14.1 recommended). Use the `node --version` command to check your version.
- An active Communication Services resource and connection string. [Create a Communication Services resource](../quickstarts/create-communication-resource.md).
- Using the UI library version [1.7.0-beta.1](https://www.npmjs.com/package/@azure/communication-react/v/1.7.0-beta.1) or the latest.
- Have a Teams meeting created and the meeting link ready.
- Familar with how [ChatWithChat Composite](https://azure.github.io/communication-ui-library/?path=/docs/composites-call-with-chat-basicexample--basic-example) works.


## Background

First of all, we need to understand that Teams Interop Chat is part of a Teams meeting. When the Teams user creates an online meeting, a chat thread would be created and associated with the meeting. To enable the Communication Service user joining the chat and starting to send/receive messages, they need to be admitted by a meeting participant first (a Teams user) first. Otherwise, they won't have access to the chat.

Once the Communication Service User is admitted to the call, they would be able to start any chat related operations. 
In this tutoria, we will be checking out how inline image works in Interop chat.

## Overview

As mentioned above, since we need to join a Teams meeting first, we need to leverage the ChatWithChat Composite from the UI library. Let's recall that we can create a ChatWithChat Composite [Storybook page](https://azure.github.io/communication-ui-library/?path=/docs/composites-call-with-chat-basicexample--basic-example) like the following:

```js
export const CallWithChatExperience = (props: CallWithChatExampleProps): JSX.Element => {
  // Construct a credential for the user with the token retrieved from your server. This credential
  // must be memoized to ensure useAzureCommunicationCallWithChatAdapter is not retriggered on every render pass.
  const credential = useMemo(() => new AzureCommunicationTokenCredential(props.token), [props.token]);

  // Create the adapter using a custom react hook provided in the @azure/communication-react package.
  // See https://aka.ms/acsstorybook?path=/docs/composite-adapters--page for more information on adapter construction and alternative constructors.
  const adapter = useAzureCommunicationCallWithChatAdapter({
    userId: props.userId,
    displayName: props.displayName,
    credential,
    locator: props.locator,
    endpoint: props.endpointUrl
  });

  // The adapter is created asynchronously by the useAzureCommunicationCallWithChatAdapter hook.
  // Here we show a spinner until the adapter has finished constructing.
  if (!adapter) {
    return <Spinner label="Initializing..." />;
  }

  return <CallWithChatComposite adapter={adapter} fluentTheme={props.fluentTheme} options={props.compositeOptions} />;
};

```

Noticing it needs `CallWithChatExampleProps` which is defined as the following:

```js
export type CallWithChatExampleProps = {
  // Props needed for the construction of the CallWithChatAdapter
  userId: CommunicationUserIdentifier;
  token: string;
  displayName: string;
  endpointUrl: string;
  locator: TeamsMeetingLinkLocator | CallAndChatLocator;

  // Props to customize the CallWithChatComposite experience
  fluentTheme?: PartialTheme | Theme;
  compositeOptions?: CallWithChatCompositeOptions;
  callInvitationURL?: string;
};

```

To be able to start the Composite for meeting chat, we need to pass `TeamsMeetingLinkLocator` which looks like this:

```js
{ "meetingLink": "<TEAMS_MEETING_LINK>" }
```

Please note that meeting link should look something like `https://teams.microsoft.com/l/meetup-join/19%3ameeting_XXXXXXXXXXX%40thread.v2/XXXXXXXXXXX`

And this is all you need! And there's no other set up needed to enable inline image specifically. 


## Run the code

Let's run `npm run start` then you should be able to access our sample app via `localhost:3000` like the following: 

![ACS UI library](./media/inline-image-tutorial-interop-chat-0.png "Screenshot of a ACS UI library.")

Simply click on the chat button located on lower-right to reveal the chat panel and now if Teams user sents an image, you should see something like the following:

![Teams sending two images](./media/inline-image-tutorial-interop-chat-1.png "Screenshot of a Teams client sending 2 inline images.")

![ACS getting two images](./media/inline-image-tutorial-interop-chat-2.png "Screenshot of ACS UI library receiving 2 inline images.")

Please note that in a Teams Interop Chat, we currently only support Communication Users to recieve inline images sent by the Teams user. To learm more about what features are supported, please refer to the [UI library Use Cases](../concepts/ui-library/includes/web-ui-use-cases.md)


## Known Issues

* Certain GIF images might not be supported by the UI library at this time. The user might receive a static image instead.
* the Web UI library doesn't support Clips (short videos) sent by the Teams users at this time.**


## Next steps

> [!div class="nextstepaction"]
> [Check the rest of the UI Library](https://azure.github.io/communication-ui-library/)

You may also want to:

- [Add chat to your app](../quickstarts/chat/get-started.md)
- [Creating user access tokens](../quickstarts/identity/access-tokens.md)
- [Learn about client and server architecture](../concepts/client-and-server-architecture.md)
- [Learn about authentication](../concepts/authentication.md)
- [Add file sharing with UI Library in Azure Communication Service Chat](./file-sharing-tutorial-acs-chat.md)
- [Add file sharing with UI Library in Teams Interoperability Chat](./file-sharing-tutorial-interop-chat.md)
