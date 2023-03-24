---
title: Admit and reject users from Teams meeting lobby
titleSuffix: An Azure Communication Services how-to guide
description: Use Azure Communication Services SDKs to admit or reject users from Teams meeting lobby.
author: tinaharter
ms.author: tinaharter
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.topic: how-to 
ms.date: 03/14/2023
ms.custom: template-how-to

---
[!INCLUDE [Install SDK](../calling-sdk/includes/install-sdk/install-sdk-web.md)]
Lobby admit and reject are the apis on `Call` or `TeamsCall`. It allows user to admit and reject participants from Teams meeting lobby. You first need to import `Call` or `TeamsCall` deom the Calling SDK:

# Manage participants in Teams meeting Lobby

In this article you will learn how to admit and reject participants from Microsoft Teams meetings lobby by using Azure Communication Service calling SDKs.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- A deployed Communication Services resource. [Create a Communication Services resource](../../quickstarts/create-communication-resource.md).
- A user access token to enable the calling client. For more information, see [Create and manage access tokens](../../quickstarts/access-tokens.md).
- Optional: Complete the quickstart to [add voice calling to your application](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Microsoft 365 or Azure Communication Services users can admit or reject users from lobby, if they are connected to Teams meeting and have Organizer, Co-organizer, or Presenter meeting role.
[Learn more about meeting roles](https://support.microsoft.com/office/roles-in-a-teams-meeting-c16fa7d0-1666-4dde-8686-0a0bfe16e019)

To admit, reject or admit all users from the lobby, you can use the `admit`, `rejectParticipant` and `admitAll` asynchronous APIs:

You can admit specific user to the Teams meeting from lobby by calling the method `admit` on the object `TeamsCall` or `Call`. The method accepts identifiers `MicrosoftTeamsUserIdentifier`, `CommunicationUserIdentifier`, `PhoneNumberIdentifier` or `UnknownIdentifier` as input.
```js
const teamsUserIdentifier = { microsoftTeamsUserId: '<MICROSOFT_TEAMS_USER_ID>' };
const userIdentifier = { communicationUserId: '<COMMUNICATION_SERVICES_USER_ID>' };
const phoneUserIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
await call.admit(teamsUserIdentifier);
await call.admit(userIdentifier);
await call.admit(phoneUserIdentifier);
```

You can also reject specific user to the Teams meeting from lobby by calling the method `rejectParticipant` on the object `TeamsCall` or `Call`. The method accepts identifiers `MicrosoftTeamsUserIdentifier`, `CommunicationUserIdentifier`, `PhoneNumberIdentifier` or `UnknownIdentifier` as input.

```js
const teamsUserIdentifier = { microsoftTeamsUserId: '<MICROSOFT_TEAMS_USER_ID>' };
const userIdentifier = { communicationUserId: '<COMMUNICATION_SERVICES_USER_ID>' };
const phoneUserIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
await call.rejectParticipant(teamsUserIdentifier);
await call.rejectParticipant(userIdentifier);
await call.rejectParticipant(phoneUserIdentifier);
```

You can also admit all users in the lobby by calling the method `admitAll` on the object `TeamsCall` or `Call`. 

```js
await call.admitAll();
```

## Next steps
- [Learn how to manage calls](./manage-calls.md)
- [Learn how to manage Teams calls](../cte-calling-sdk/manage-calls.md)
- [Learn how to join Teams meeting](./teams-interoperability.md)
- [Learn how to manage video](./manage-video.md)
- [Learn how to transfer calls](./transfer-calls.md)
