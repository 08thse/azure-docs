---
title: View information provided in alerts
description: 
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: article
ms.service: azure
ms.topic: how-to
---

# View information provided in alerts

Select an alert from the Alerts window to review alert details. The following information is provided in alerts:

  - ***Alert metadata***

  - ***Information about traffic, devices, and the event***

  - ***Link to connected devices in the device map***

  - ***Comments defined by security analysts and administrators***

  - ***Recommendations for investigating the event***

## Alert metadata

The following alert metadata is displayed.

  - Alert ID

  - Policy engine that triggered the alert

  - Date and time the alert was triggered

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="Unauthorized internect connectivity detected.":::

## Information about traffic, devices, and the event

The alert message provides information about:

  - The detected devices.

  - The traffic detected between the devices. For example, protocols, and function codes.

  - Insights into the implications of the event.

You can use this information when deciding how to manage the alert event.

## Link to connected devices in the device map

To learn more about devices connected to the devices detected, you can select an device image in the alert and view connected devices in the map.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="RCP operation failed.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Devices screenshot.":::

The map filters to the device you selected, and other devices connected to it. The Quick Properties dialog box for the devices detected in the alerts is displayed on the map as well.

## Comments defined by security analysts and administrators 

Alerts may include a list of predefined comments, for example with instructions regarding mitigation actions to take, or names of individuals to contact regarding the event.

When managing an alert event, you can choose the comment or comments that best reflect the event status or steps you have taken to investigate the alert.

Selected comments are saved in the alert message. Working with comments enhances communication between individuals and teams during the investigation of an alert event, and as a result, can accelerate incident response time.

Comments are pre-defined by an administrator or the security analyst users. Selected comments are not forwarded to partner systems defined in the Forwarding rules.
