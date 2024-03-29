---
title: Call setup issues - The call setup takes too long
titleSuffix: Azure Communication Services - Troubleshooting Guide
description: Learn how to troubleshoot when the call setup takes too long
author: enricohuang
ms.author: enricohuang

services: azure-communication-services
ms.date: 02/24/2024
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.subservice: calling
---

# The call setup takes too long
When  establishing a call between two parties, multiple steps and messages are exchanged between the signaling layer and media transport.
If the call setup takes too long, it is often due to network issues.
Another factor that contributes to call setup delay is the stream acquisition delay, which is the time it takes for a browser to get the media stream.
Additionally, device performance can also affect call setup time. For example, a busy browser may take longer to schedule the API request, resulting in a longer call setup time.

## How to detect
### SDK
The application can calculate the timestamp between when the call is initiated and when it is connected.

## How to monitor
### Azure log
...
### CDC
...

## How to mitigate or resolve
If a user consistently experiences long call setup times, they should check their network for issues such as slow network speed, long round trip time, or high packet loss.
These issues can affect call setup time because the signaling layer uses a TCP connection, and factors such as retransmissions can cause delays.
Additionally, if the user suspects the delay comes from stream acquisition, they should check their devices. For example, they can choose a different audio input device.
