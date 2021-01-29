---
title: Device Update for IoT Hub network requirements | Microsoft Docs
description: Device Update for IoT Hub uses a variety of network ports for different purposes.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub
---

# Ports Used With Device Update for IoT Hub
ADU uses a variety of network ports for different purposes.

## Ports

Purpose|Port Number |
---|---
Download from Networks/CDNs  | 80 (HTTP Protocol)
Download from MCC/CDNs | 80 (HTTP Protocol)
ADU Agent Connection to Azure IoT Hub  | 8883 (MQTT Protocol)

**NOTE**: The ADU agent can be modified to use any of the supported Azure IoT
Hub protocols.
[Learn more](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows) about the current list of supported protocols.
