---
title: UI Mobile Library Usage scenarios
titleSuffix: An Azure Communication Services - UI Mobile Library Usage scenarios
description: In this document, introduce the UI Mobile Library Capabilities and how is going to work in your applications
author: jorgegarc

ms.author: jorgegarc
ms.date: 09/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
---

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

# UI Library (iOS and Android) capabilities

UI Library for iOS and Android supports calling use cases by using the **calling composite**.
Composites enable developers to easily integrate a whole calling experience into their application with only a couple of lines of code; those composites take care of the entire lifecycle of the call from setup to the call ending.

## Calling

| Area                                                                                            | Use Cases                                              |
| ----------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Call Types                                                                                      | Join Teams Meeting                                     |
|                                                                                                 | Join Azure Communication Services call with Group Id   |
| [Teams Interop](../../concepts/teams-interop) | Call Lobby                                             |
|                                                                                                 | Transcription and recording alert banner               |
| Participant Gallery                                                                             | Remote participants are displayed on grid              |
|                                                                                                 | Video preview available throughout call for local user |
|                                                                                                 | Default avatars available when video is off            |
|                                                                                                 | Shared screen content displayed on participant gallery |
|                                                                                     | Participant roster                                     |
| Call configuration                                                                              | Microphone device management                           |
|                                                                                                 | Camera device management                               |
|                                                                                                 | Speaker device management                              |
|                                                                                                 | Local preview available for user to check video        |
| Call Controls                                                                                   | Mute/unmute call                                       |
|                                                                                                 | Video on/off on call                                   |
|                                                                                                 | End call                                               |

## Supported Identities

An Azure Communication Services identity is required to initialize the composites and authenticate to the service.
For more information on authentication, see [Authentication](../authentication) and [Access Tokens](../../quickstarts/access-tokens)

## Teams Interop

![Teams Interop pattern for calling and chat](../media/mobile-ui/TeamsInteropDiagram.png)

For [Teams Interop](../teams-interop) scenarios, developers can use UI Mobile Library Components to join Teams meetings through Azure Communication Services.
To enable Teams Interop, developers can use the calling composite, which will take care of the lifecycle of joining a Teams Interop call.

<img src="../media/mobile-ui/teams_meet.png" width="600"/> 


## Theming

The UI Library Calling Composite for iOS and Android provides interfaces for developers change the theme of the experience by passing in a primary color. The Composite uses that primary color to provide appropriate theming across the experience. More theming customizations will be available on GA release.

| Android                            | iOS                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
|  <img alt="android theming" src="../media/mobile-ui/android_color.png" width="75%"/>     | <img alt="iOS theming" src="../media/mobile-ui/ios_dark.png" width="50%" /> |


## Screen size

The calling composite offers to adapt to any screen size that would bring support from 5" screens to tablets, get the dynamic participants' roster layout, provide clarity on the view, and focus on the conversation.

|Split mode | Tablet mode|
|---------|---------|
| <img alt="split screen" src="../media/mobile-ui/meet_splitscreen.png" width="80%"/>      | <img alt="tablet mode" src="../media/mobile-ui/tablet_landscape.png" width="80%"/>  |

## Recommended Architecture

Composite are initialized using an Azure Communication Services access token. Access tokens should be procured from Azure Communication Services through a
trusted service that you manage. See [Quickstart: Create Access Tokens](../../quickstarts/access-tokens) and [Trusted Service Tutorial](../../tutorials/trusted-service-tutorial) for more information.

<img alt="recommended architecture diagram" src="../media/mobile-ui/ui-library-architecture.png" width="80%" />

These client libraries also require the context for the call they will join. Similar to user access tokens, this context should be disseminated to clients via your own trusted service. The list below summarizes the initialization and resource management functions that you need to operationalize.

| Contoso Responsibilities                                 | UI Library Responsibilities                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| Provide access token from Azure                          | Pass through given access token to initialize components        |
| Provide refresh function                                 | Refresh access token using developer provided function          |
| Retrieve/Pass join information for call or chat          | Pass through call and chat information to initialize components |
| Retrieve/Pass user information for any custom data model | Pass through custom data model to components to render          |

## Platform support

|Platform | Versions|
|---------|---------|
| iOS     | iOS 13+ |
| Android | v23+    |

## Accessibility

Accessibility by design is a principle across Microsoft products.
UI Library follows this principle in making sure that all UI Components are fully accessible.
During public preview, the UI Library will continue to improve and add accessibility feature to the UI Components.
We expect to add more details on accessibility ahead of the UI Library being in General Availability.

## Localization

Localization is a key to making products that can be used across the world and by people who speak different languages.
UI Library will provide out of the box support for some languages and capabilities such as RTL.
Developers can provide their own localization files to be used for the UI Library.
These localization capabilities will be added ahead of General Availability.