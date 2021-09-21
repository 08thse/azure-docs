---
title: Connecting cameras to Azure Video Analyzer cloud
description: This article discusses connecting cameras to Azure Video Analyzer's cloud service.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/01/2021
---

# Connect cameras to the cloud

Azure Video Analyzer allows users to capture and record video directly to the cloud using cloud pipelines. Connecting cameras to Video Analyzer will either reduce the computational load on your edge device or eliminate the need for an edge device completely. Video Analyzer currrently supports three different methods for connecting cameras to the cloud: connecting via a transparent gateway, connecting behind a firewall using an IoT PnP command, and connecting over the open internet without a firewall.

<!--- TODO: add diagram similar to pvt preview just without pvt preview verbage -->

## Connecting via a transparent gateway
This method is for cameras to connect to Video Analyzer via the Video Analyzer Edge module acting as a [transparent gateway](../../../iot-edge/iot-edge-as-gateway.md). This approach is appropriate for users that are uncomfortable with connecting their cameras directly to the cloud without a firewall. The Video Analyzer Edge module acting as a transparent gateway will lighten the computational load on the edge device, therefore this method is appropriate for users that want to use a lightweight edge device to connect cameras to Video Analyzer and use our [cloud pipelines]().<!--- TODO: link to cloud pipeline-->

## Connecting behind a firewall using an IoT PnP command
This method is for cameras to connect to Video Analyzer behind a firewall using [IoT’s Plug and Play command interface](../../../iot-develop/overview-iot-plug-and-play.md) and eliminates the need for an edge device. This method is for users that can convert their cameras into an IoT device. Currently, this method has only been fully implemented for Axis camera models <!--- TODO: add what models or what firmware the camera must be running --> using the [Video Analyzer ACAP application](). Information for how to connect a compatible device from any manufacturer is [here](connect-devices.md).


## Connecting over the open internet (no firewall)
This method is for users that are comfortable with connecting their camera directly to the cloud without a firewall and their camera's RTSP server is accessible over the open internet. Using this method without a firewall allows users to get started quickly and use our cloud APIs and pipeline. This option does not require the Video Analyzer Edge module to act as transparent gateway. Here is a [how-to guide]()<!--- TODO: is it appropriate to describe how to connect without firewall in transparent gateway how-to?  --> for connecting a camera over the open internet with no firewall.


# Next Steps
- Follow [this how-to guide](use-transparent-gateway.md) to connect cameras via a transparent gateway
- Follow [this tutorial]() <!--- TODO: link to ACAP tutorial--> to connect an Axis camera
- Follow [this how-to guide](connect-devices.md) for information on connecting devices from other manufactures
- Follow [this how-to guide]() <!--- TODO: is it appropriate to describe how to connect without firewall in transparent gateway how-to?  --> to connect cameras over the open internet with no firewall


