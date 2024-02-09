---
title: Azure Communication Services - best practices for Calling Native SDK
description: Learn more about Azure Communication Service best practices for Calling Native SDK.
author: garchiro7
manager: chpalmer
services: azure-communication-services

ms.author: jorgarcia
ms.date: 02/08/2024
ms.topic: include
ms.service: azure-communication-services
---

## Azure Communication Services native SDK best practices

This section provides information about best practices associated with the Azure Communication Services voice and video calling native SDK.

## Supported platforms

Here are the minimum OS platform requirements to ensure optimal functionality of the Calling Native SDKs.

### [iOS](#tab/ios)

- Support for iOS 10.0+ at build time, and iOS 12.0+ at run time.
- Xcode 12.0+.
- Support for **iPadOS** 13.0+.

### [Android](#tab/android)

- Support for Android API Level 21 or Higher.
- Support for Java 7 or higher.
- Support for Android Studio 2.0.
- **Android Auto** and **IoT devices running Android** are currently not supported.

### [Windows](#tab/windows)

- Support for UWP (Universal Windows Platform).
- Support for WinUI 3.

---

## App request device permissions

To use the Calling Native SDKs for making or receiving calls, it's necessary to authorize each platform to access device resources. As a developer, you should prompt the user for access and ensure that it's enabled. The consumer authorizes these access rights, so verify that they have been granted permission previously.

### [iOS](#tab/ios)

- `NSMicrophoneUsageDescription` for microphone access.
- `NSCameraUsageDescription` for camera access.

### [Android](#tab/android)

In the Application Manifest (`app/src/main/AndroidManifest.xml`). Verify the following lines:

```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE_MICROPHONE" />
```

### [Windows](#tab/windows)

Go to `Package.appxmanifest` and select capabilities:

- `Internet (Client)` & `Internet (Client & Server)` for network access.
- `Microphone` to access the audio feed of the microphone.
- `Webcam` to access the video feed of the camera.

---

## Configure the logs

Implementing **logging** as per the [logs file retrieval tutorial](../../tutorials/log-file-retrieval-tutorial.md) is more critical than ever. Detailed logs help in diagnosing issues specific to device models or OS versions that meet the minimum SDK criteria. We encourage to the developers that start configuring the Logs API without the logs the Microsoft Support team **won't be able** to help debug and troubleshoot the calls.

## Track Call ID

When troubleshooting happens for voice or video calls, you're asked to provide a **`CallID`**; this ID is used to identify Communication Services calls. You **should** track the `CallID` in the telemetry that you configure in your app, you can follow the guidelines in the [troubleshooting guide](../troubleshooting-info.md).

## Subscribe to UFD (User Facing Diagnostics) and media quality statistics

- [User Facing Diagnostics (UFD)](../voice-video-calling/user-facing-diagnostics.md) that can be used to examine various properties of a call to determine what the issue might be during the call that affects your customers.
- [Media quality statistics](../voice-video-calling/media-quality-sdk.md) examine the low-level audio, video, and screen-sharing quality metrics for incoming and outgoing call metrics. We recommend that you collect the data and send it to your pipeline ingestion after your call ends.

## General memory management

**Preallocate Resources**. Initialize your calling client and any necessary resources during your app's startup phase rather than on demand. This approach reduces latency when starting a call.

**Dispose Properly**. Ensure that all call objects are correctly disposed of after use to free up system resources and avoid memory leaks.

## Camera or microphone being used by another process

It's important to note that on mobile devices if multiple processes try to access the camera or microphone at the same time, the first process to request access will take control of the device. As a result, the second process will immediately lose access to it.
