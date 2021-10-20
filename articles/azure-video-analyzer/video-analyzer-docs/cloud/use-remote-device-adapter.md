---
title: Connect camera to cloud using a remote device adapter
description: This article explains how to connect a camera to Azure Video Analyzer using a remote device adapter
ms.topic: how-to
ms.date: 11/01/2021
---

# Connect cameras to the cloud using a remote device adapter

Azure Video Analyzer allows users to connect cameras directly to the cloud in order to capture and record video, using cloud pipelines.
Connecting cameras to the cloud using a remote device adapter allows cameras to connect to Video Analyzer's service via the Video Analyzer edge module acting as a transparent gateway for video packets via RTSP protocol. This approach is useful in the following scenarios:


* When cameras connected to the gateway need to be shielded from exposure to the internet
* When cameras do not have the functionality to connect to IoT Hub independently
* When power, space, or other considerations permit only a lightweight edge device to be deployed on-premise

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-remote-device-adapter/use-remote-device-adapter.svg" alt-text="Connect cameras to the cloud with a remote device adapter":::

## Pre-reading

* [Get started with Azure Video Analyzer in the Portal](../get-started-detect-motion-emit-events-portal.md)  
* [Connect camera to the cloud](connect-cameras-to-cloud.md#connect-via-a-remote-device-adapter)

## Prerequisites

The following are required for this how-to guide:

* An Azure account that has an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) if you don't already have one.
* [Azure Video Analyzer account](../create-video-analyzer-account.md) with associated:
  * Storage account
  * User-assigned managed identity (UAMI)
* IoT Hub
  * User-assigned managed identity with **Contributor** role access
* Video Analyzer account must be paired with IoT Hub
* [IoT Edge with Video Analyzer edge module installed and configured](../edge/deploy-iot-edge-device.md)
* RTSP capable camera(s)
  * Ensure that camera(s) are on the same network as edge device

## Overview
The following is a overview of the instructions of this how-to guide:

* Provision a device entry on IoT Hub to represent the legacy camera device
* Create a device adapter with Video Analyzer edge to proxy the legacy camera as a transparent gateway
* Reference the IoT Camera device in the cloud live topology and pipeline to ingest data from the camera.

## Provisioning IoT device entry/credentials

An IoT device has to be created for each camera to connect to the cloud.

In the Azure portal:

1. Navigate to the IoT Hub
1. Select the **IoT devices** pane under **Explorers**
1. Select **+Add device** 
1. Enter a **Device ID** with a unique string (Ex: building404-camera1)
1. **Authentication type** must be **Symmetric key**
1. All other properties can be left as default
1. Select **Save** to create the IoT device
1. Select the IoT device, and record the **Primary key** or **Secondary key**, as it will be needed

## Create remote device adapter to enable transparent gateway

To enable the edge module to act as a transparent gateway for video between the camera and Video Analyzer, you must create a remote device adapter for each camera by invoking the **RemoteDeviceAdapterSet** direct method that requires the following values: 

* Device ID for the IoT device
* Primary key for the IoT device
* Camera's IP address

In the Azure portal:

1. Navigate to the IoT Hub
1. Select the **IoT Edge** pane under **Automatic Device Management**
1. Select the corresponding edge device (Ex: ava-sample-device)
1. Under modules, select **avaedge**
1. Select **</> Direct Method** 
1. Enter **RemoteDeviceAdapterSet** for Method Name
1. Enter the following for **Payload** :

```
 {
   "@apiVersion" : "1.1",
   "name": "remoteDeviceAdapterCamera1",
   "properties": {
     "target": {
       "host": "<Camera's IP address>"
      },
     "iotHubDeviceConnection": {
      "deviceId": "<IoT Hub Device ID>",
      "credentials": {
        "@type": "#Microsoft.VideoAnalyzer.SymmetricKeyCredentials",
        "key": "<Primary or Secondary Key>"
       }
     }
   }
 }
 
```

If successful, you will receive a response with a status code 201.


## Create pipeline topology in the cloud

> [!NOTE]
> IoT Hub ARM ID and IoT Hub User-Assiged Managed Identity ARM ID will be needed for the next steps. To acquire the IoT Hub ARM ID, navigate to the **Overview** pane of the IoT Hub and select **JSON View**. Record the **Resource ID** value for the IoT Hub ARM ID. To acquire the IoT Hub User-Assiged Managed Identity ARM ID, navigate to the **Overview** pane of the user-assigned managed identity that has been assigned **Owner** role on the IoT Hub and select **JSON View**. Record the **Resource ID** value for the IoT Hub User-Assiged Managed Identity ARM ID.

When creating a cloud pipeline topology to ingest from a camera behind a firewall, tunneling must be enabled on the RTSP source node of the pipeline topology.

See an example of a [pipeline topology]()<!-- TODO: add link to sample topology with tunneling enabled on RTSP source node -->.  

[This quickstart](get-started-livepipelines-portal.md#deploy-a-live-pipeline) outlines the steps for creating a pipeline topology and live pipeline in Azure portal. Use the sample topology `CVR from private camera`.

The following values, specific to the IoT device provisioned in the previous instructions, are required to enable tunneling on the RTSP source node:

* IoT Hub Name
* IoT Hub Device ID

```
            {
                "@type": "#Microsoft.VideoAnalyzer.RtspSource",
                "name": "rtspSource",
                "transport": "tcp",
                "endpoint": {
                    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                    "url": "${rtspUrlParameter}",
                    "credentials": {
                        "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                        "username": "${rtspUsernameParameter}",
                        "password": "${rtspPasswordParameter}"
                    },
                    "tunnel": { 
                        "@type": "#Microsoft.VideoAnalyzer.SecureIotDeviceRemoteTunnel",
                        "iotHubName" : "<IoT Hub Name>",
                        "deviceId": "${ioTHubDeviceIdParameter}"
                    }
                }
            }
``` 

Ensure that:
* `Transport` is set to `TCP`
* `Endpoint` is set to `Unsecured endpoint`
* `Tunnel` is set to `Secure IoT device remote tunnel`

## Create and activate live pipeline in the cloud

When creating the live pipeline, the RTSP URL, RTSP username, RTSP password, and IoT Hub Device ID must be defined.

```
   {
    "name": "Sample-Pipeline-1",
    "properties": {
        "topologyName": "CVRwithRemoteDeviceAdapter", <!-- TODO: change to match name in GitHub -->
        "description": "Continuous video recording with ingestion via a remote device adapter",
        "bitrateKbps": 500,
        "parameters": [
            {
                "name": "rtspUrlParameter",
                "value": "rtsp://localhost:554/media/camera-300s.mkv"
            },
            {
                "name": "rtspUsernameParameter",
                "value": "test"
            },
            {
                "name": "rtspPasswordParameter",
                "value": "password"
            },
            {
                "name": "ioTHubDeviceIdParameter",
                "value": "building404-camera1"
            }
          ]
       }
   }
```
The RTSP URL must be **localhost** because the access to the camera is being tunneled.  

After creating the live pipeline, the pipeline can be activated to start recording to the Video Analyzer video resource.  
[The quickstart](get-started-livepipelines-portal.md#deploy-a-live-pipeline) mentioned in the previous step also outlines how to activate a live pipeline in Azure portal.

The [AVA C# cloud sample repository]() <!-- TODO: add link to cloud sample repo --> can also be used to automate this process.

### Playback recorded video in the Azure portal

1. After activating the live pipeline, the video resource will be available under the Video Analyzer account **Videos** pane in the portal. The status will indicate **Is in use** as pipeline is active and recording.
1. Select the video name that was defined in the topology and view the video.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="Screenshot of the recorded video captured by live pipeline on the cloud.":::


[!INCLUDE [activate-deactivate-pipeline](../edge/includes/common-includes/activate-deactivate-pipeline.md)]

## Next steps

Now that a video exists in your Video Analyzer account, you can export a clip of this recorded video to MP4 format using [this tutorial](export-portion-of-video-as-mp4.md).

