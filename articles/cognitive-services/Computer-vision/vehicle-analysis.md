---
title: Configure vehicle analysis containers
titleSuffix: Azure Cognitive Services
description: Vehicle analysis provides each container with a common configuration framework, so that you can easily configure and manage compute, AI insight egress, logging, and security settings.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/28/2022
ms.author: pafarley
---

# Install and run vehicle analysis (preview)

Vehicle analysis is a set of capabilities that, when used with the Spatial Analysis container, enable you to analyze real-time streaming video to understand vehicle characteristics and placement. In this article, you'll learn how to use the capabilities of the spatial analysis container to deploy vehicle analysis operations.

## Prerequisites

* To utilize the operations of vehicle analysis, you must first follow the steps to [install and run spatial analysis container](./spatial-analysis-container.md) including configuring your host machine, downloading and configuring your [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest.json) file, executing the deployment, and setting up device [telemetry](./Project-Archon-Telemetry). 
   * When you configure your [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest.json) file, refer to the steps below to add the graph configurations for vehicle analysis to your manifest prior to deploying the container. Or, once the spatial analysis container is up and running, you may add the graph configurations and follow the steps to redeploy. The steps below will outline how to properly configure your container.
If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

> [!NOTE]
> Make sure that the edge device has at least 50GB disk space available before deploying the Spatial Analysis module.

## Vehicle analysis operations

Similar to Spatial Analysis, vehicle analysis enables the analysis of real-time streaming video from camera devices. For each camera device you configure, the operations for vehicle analysis will generate an output stream of JSON messages that are being sent to your instance of Azure IoT Hub.

The following operations for vehicle analysis are available in the current Spatial Analysis container. Vehicle analysis offers operations optimized for both GPU and CPU (CPU operations include the *.cpu* distinction).

| Operation identifier | Description |  
| -------------------- | ---------------------------------------- |  
| **cognitiveservices.vision.vehicleanalysis-vehiclecount-preview** and **cognitiveservices.vision.vehicleanalysis-vehiclecount.cpu-preview** | Counts vehicles parked in a designated zone in the camera's field of view. </br> Emits an initial _vehicleCountEvent_ event and then _vehicleCountEvent_ events when the count changes. |  
| **cognitiveservices.vision.vehicleanalysis-vehicleinpolygon-preview** and **cognitiveservices.vision.vehicleanalysis-vehicleinpolygon.cpu-preview** | Identifies when a vehicle parks in a designated parking region in the camera's field of view. </br> Emits a _vehicleInPolygonEvent_ event when the vehicle is parked inside a parking space. |

In addition to exposing the vehicle location, other estimated attributes for **cognitiveservices.vision.vehicleanalysis-vehiclecount-preview**, **cognitiveservices.vision.vehicleanalysis-vehiclecount.cpu-preview**, **cognitiveservices.vision.vehicleanalysis-vehicleinpolygon-preview** and **cognitiveservices.vision.vehicleanalysis-vehicleinpolygon.cpu-preview** include vehicle color and vehicle type. All of the possible values for these attributes are found in the [output section (below)](#sample-cognitiveservicesvisionvehicleanalysis-vehiclecount-output).

### Operation parameters for vehicle analysis

The following table shows the parameters required by each of the vehicle analysis operations. Many are shared with Spatial Analysis; the only one not shared is the `PARKING_REGIONS` setting. The full list of Spatial Analysis operation parameters can be found in the **Environment Variables** section under [How to deploy the container on Azure IoT Edge on the host computer](https://github.com/Azure/CSContainers/blob/master/Project-Archon/Project-Archon.md#how-to-deploy-the-container-on-azure-iot-edge-on-the-host-computer).

| Operation parameters| Description|
|---------|---------|
| Operation ID | The Operation Identifier from table above.|
| enabled | Boolean: true or false|
| VIDEO_URL| The RTSP URL for the camera device (for example: `rtsp://username:password@url`). Spatial Analysis supports H.264 encoded streams either through RTSP, HTTP, or MP4. |
| VIDEO_SOURCE_ID | A friendly name for the camera device or video stream. This will be returned with the event JSON output.|
| VIDEO_IS_LIVE| True for camera devices; false for recorded videos.|
| VIDEO_DECODE_GPU_INDEX| Index specifying which GPU will decode the video frame. By default it is 0. This should be the same as the `gpu_index` in other node configurations like `VICA_NODE_CONFIG`, `DETECTOR_NODE_CONFIG`.|
| PARKING_REGIONS | JSON configuration for zone and line as outlined below. </br> PARKING_REGIONS must contain four points in normalized coordinates ([0, 1]) that define a convex region (the points follow a clockwise or counterclockwise order).|
| EVENT_OUTPUT_MODE | Can be ON_INPUT_RATE or ON_CHANGE. ON_INPUT_RATE will generate an output on every single frame received (one FPS). ON_CHANGE will generate an output when something changes (number of vehicles or parking spot occupancy). |
| PARKING_SPOT_METHOD | Can be BOX or PROJECTION. BOX will use an overlap between the detected bounding box and a reference bounding box. PROJECTIONS will project the centroid point into the parking spot polygon drawn on the floor. This is only used for Parking Spot and can be suppressed.|

This is an example of a valid `PARKING_REGIONS` configuration:

```json
"{\"parking_slot1\": {\"type\": \"SingleSpot\", \"region\": [[0.20833333, 0.46203704], [0.3015625 , 0.66203704], [0.13229167, 0.7287037 ], [0.07395833, 0.51574074]]}}"
```

### Zone configuration for cognitiveservices.vision.vehicleanalysis-vehiclecount-preview and cognitiveservices.vision.vehicleanalysis-vehiclecount.cpu-preview

This is an example of a JSON input for the `PARKING_REGIONS` parameter that configures a zone. You may configure multiple zones for this operation.

```json
{
    "zone1": {
        type: "Queue",
        region: [(x1, y1), (x2, y2), (x3, y3), (x4, y4)]
    }
}
```

| Name | Type| Description|
|---------|---------|---------|
| `zones` | dictionary | Keys are the zone names and the values are a field with type and region.|
| `name` | string| Friendly name for this zone.|
| `region` | list| Each value pair represents the x,y for vertices of polygon. The polygon represents the areas in which vehicles are tracked or counted. The float values represent the position of the vertex relative to the top left corner. To calculate the absolute x, y values, you multiply these values with the frame size. 
| `type` | string| For **cognitiveservices.vision.vehicleanalysis-vehiclecount** this should be "Queue".|


### Zone configuration for cognitiveservices.vision.vehicleanalysis-vehicleinpolygon-preview and cognitiveservices.vision.vehicleanalysis-vehicleinpolygon.cpu-preview

This is an example of a JSON input for the `PARKING_REGIONS` parameter that configures a zone. You may configure multiple zones for this operation.

```json
{
    "zone1": {
        type: "SingleSpot",
        region: [(x1, y1), (x2, y2), (x3, y3), (x4, y4)]
    }
}
```

| Name | Type| Description|
|---------|---------|---------|
| `zones` | dictionary | Keys are the zone names and the values are a field with type and region.|
| `name` | string| Friendly name for this zone.|
| `region` | list| Each value pair represents the x,y for vertices of polygon. The polygon represents the areas in which vehicles are tracked or counted. The float values represent the position of the vertex relative to the top left corner. To calculate the absolute x, y values, you multiply these values with the frame size. 
| `type` | string| For **cognitiveservices.vision.vehicleanalysis-vehicleingpolygon-preview** and **cognitiveservices.vision.vehicleanalysis-vehicleingpolygon.cpu-preview** this should be "SingleSpot".|

## Configuring the vehicle analysis operations

You must configure the graphs for vehicle analysis in your [DeploymentManifest.json](./DeploymentManifest.json) file to enable the vehicle analysis operations. Below are sample graphs for vehicle analysis. You can add these JSON snippets to your deployment manifest in the "graphs" configuration section, configure the parameters for your video stream, and deploy the module. If you only intend to utilize the vehicle analysis capabilities, you may replace the existing graphs in the [DeploymentManifest.json](./DeploymentManifest.json) with the vehicle analysis graphs.

Below is the graph optimized for the **vehicle count** operation.

```json
"vehiclecount": {
    "operationId": "cognitiveservices.vision.vehicleanalysis-vehiclecount-preview",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "vehiclecountgraph",
        "VIDEO_DECODE_GPU_INDEX": 0,
        "VIDEO_IS_LIVE":true,
        "PARKING_REGIONS": ""{\"1\": {\"type\": \"Queue\", \"region\": [[0.20833333, 0.46203704], [0.3015625 , 0.66203704], [0.13229167, 0.7287037 ], [0.07395833, 0.51574074]]}}"
    }
}
```

Below is the graph optimized for the **vehicle in polygon** operation, utilized for vehicles in a parking spot.

```json
"vehicleinpolygon": {
    "operationId": "cognitiveservices.vision.vehicleanalysis-vehicleinpolygon-preview",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "vehcileinpolygon",
        "VIDEO_DECODE_GPU_INDEX": 0,
        "VIDEO_IS_LIVE":true,
        "PARKING_REGIONS": ""{\"1\": {\"type\": \"SingleSpot\", \"region\": [[0.20833333, 0.46203704], [0.3015625 , 0.66203704], [0.13229167, 0.7287037 ], [0.07395833, 0.51574074]]}}"
    }
}
```

## Sample cognitiveservices.vision.vehicleanalysis-vehiclecount-preview and cognitiveservices.vision.vehicleanalysis-vehiclecount.cpu-preview output

The JSON below demonstrates an example of the vehicle count operation graph output.

```json
{
    "events": [
        {
            "id": "95144671-15f7-3816-95cf-2b62f7ff078b",
            "type": "vehicleCountEvent",
            "detectionIds": [
                "249acdb1-65a0-4aa4-9403-319c39e94817",
                "200900c1-9248-4487-8fed-4b0c48c0b78d"
            ],
            "properties": {
                "@type": "type.googleapis.com/microsoft.rtcv.insights.VehicleCountEventMetadata",
                "detectionCount": 3
            },
            "zone": "1",
            "trigger": ""
        }
    ],
    "sourceInfo": {
        "id": "vehiclecountTest",
        "timestamp": "2022-09-21T19:31:05.558Z",
        "frameId": "4",
        "width": 0,
        "height": 0,
        "imagePath": ""
    },
    "detections": [
        {
            "type": "vehicle",
            "id": "200900c1-9248-4487-8fed-4b0c48c0b78d",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5962499976158142,
                        "y": 0.46250003576278687,
                        "visible": false,
                        "label": ""
                    },
                    {
                        "x": 0.7544531226158142,
                        "y": 0.64000004529953,
                        "visible": false,
                        "label": ""
                    }
                ],
                "name": "",
                "normalizationType": "UNSPECIFIED_NORMALIZATION"
            },
            "confidence": 0.9934938549995422,
            "attributes": [
                {
                    "task": "VehicleType",
                    "label": "Bicycle",
                    "confidence": 0.00012480001896619797
                },
                {
                    "task": "VehicleType",
                    "label": "Bus",
                    "confidence": 1.4998147889855318e-05
                },
                {
                    "task": "VehicleType",
                    "label": "Car",
                    "confidence": 0.9200984239578247
                },
                {
                    "task": "VehicleType",
                    "label": "Motorcycle",
                    "confidence": 0.0058081308379769325
                },
                {
                    "task": "VehicleType",
                    "label": "Pickup_Truck",
                    "confidence": 0.0001521655503893271
                },
                {
                    "task": "VehicleType",
                    "label": "SUV",
                    "confidence": 0.04790870100259781
                },
                {
                    "task": "VehicleType",
                    "label": "Truck",
                    "confidence": 1.346438511973247e-05
                },
                {
                    "task": "VehicleType",
                    "label": "Van/Minivan",
                    "confidence": 0.02388562448322773
                },
                {
                    "task": "VehicleType",
                    "label": "type_other",
                    "confidence": 0.0019937530159950256
                },
                {
                    "task": "VehicleColor",
                    "label": "Black",
                    "confidence": 0.49258527159690857
                },
                {
                    "task": "VehicleColor",
                    "label": "Blue",
                    "confidence": 0.47634875774383545
                },
                {
                    "task": "VehicleColor",
                    "label": "Brown/Beige",
                    "confidence": 0.007451261859387159
                },
                {
                    "task": "VehicleColor",
                    "label": "Green",
                    "confidence": 0.0002614705008454621
                },
                {
                    "task": "VehicleColor",
                    "label": "Grey",
                    "confidence": 0.0005819533253088593
                },
                {
                    "task": "VehicleColor",
                    "label": "Red",
                    "confidence": 0.0026496786158531904
                },
                {
                    "task": "VehicleColor",
                    "label": "Silver",
                    "confidence": 0.012039118446409702
                },
                {
                    "task": "VehicleColor",
                    "label": "White",
                    "confidence": 0.007863214239478111
                },
                {
                    "task": "VehicleColor",
                    "label": "Yellow/Gold",
                    "confidence": 4.345366687630303e-05
                },
                {
                    "task": "VehicleColor",
                    "label": "color_other",
                    "confidence": 0.0001758455764502287
                }
            ],
            "metadata": {
                "tracking_id": ""
            }
        },
        {
            "type": "vehicle",
            "id": "249acdb1-65a0-4aa4-9403-319c39e94817",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.44859376549720764,
                        "y": 0.5375000238418579,
                        "visible": false,
                        "label": ""
                    },
                    {
                        "x": 0.6053906679153442,
                        "y": 0.7537500262260437,
                        "visible": false,
                        "label": ""
                    }
                ],
                "name": "",
                "normalizationType": "UNSPECIFIED_NORMALIZATION"
            },
            "confidence": 0.9893689751625061,
            "attributes": [
                {
                    "task": "VehicleType",
                    "label": "Bicycle",
                    "confidence": 0.0003215899341739714
                },
                {
                    "task": "VehicleType",
                    "label": "Bus",
                    "confidence": 3.258735432609683e-06
                },
                {
                    "task": "VehicleType",
                    "label": "Car",
                    "confidence": 0.825579047203064
                },
                {
                    "task": "VehicleType",
                    "label": "Motorcycle",
                    "confidence": 0.14065399765968323
                },
                {
                    "task": "VehicleType",
                    "label": "Pickup_Truck",
                    "confidence": 0.00044341650209389627
                },
                {
                    "task": "VehicleType",
                    "label": "SUV",
                    "confidence": 0.02949284389615059
                },
                {
                    "task": "VehicleType",
                    "label": "Truck",
                    "confidence": 1.625348158995621e-05
                },
                {
                    "task": "VehicleType",
                    "label": "Van/Minivan",
                    "confidence": 0.003406822681427002
                },
                {
                    "task": "VehicleType",
                    "label": "type_other",
                    "confidence": 8.27941985335201e-05
                },
                {
                    "task": "VehicleColor",
                    "label": "Black",
                    "confidence": 2.028317430813331e-05
                },
                {
                    "task": "VehicleColor",
                    "label": "Blue",
                    "confidence": 0.00022600525699090213
                },
                {
                    "task": "VehicleColor",
                    "label": "Brown/Beige",
                    "confidence": 3.327144668219262e-06
                },
                {
                    "task": "VehicleColor",
                    "label": "Green",
                    "confidence": 5.160827640793286e-05
                },
                {
                    "task": "VehicleColor",
                    "label": "Grey",
                    "confidence": 5.614096517092548e-05
                },
                {
                    "task": "VehicleColor",
                    "label": "Red",
                    "confidence": 1.0396311012073056e-07
                },
                {
                    "task": "VehicleColor",
                    "label": "Silver",
                    "confidence": 0.9996315240859985
                },
                {
                    "task": "VehicleColor",
                    "label": "White",
                    "confidence": 1.0256461791868787e-05
                },
                {
                    "task": "VehicleColor",
                    "label": "Yellow/Gold",
                    "confidence": 1.8006812751991674e-07
                },
                {
                    "task": "VehicleColor",
                    "label": "color_other",
                    "confidence": 5.103976263853838e-07
                }
            ],
            "metadata": {
                "tracking_id": ""
            }
        }
    ],
    "schemaVersion": "2.0"
}
```

| Event Field Name | Type| Description|
|---------|---------|---------|
| `id` | string| Event ID|
| `type` | string| Event type|
| `detectionsId` | array| Array of unique identifier of the vehicle detections that triggered this event|
| `properties` | collection| Collection of values [detectionCount]|
| `zone` | string | The "name" field of the polygon that represents the zone that was crossed|
| `trigger` | string| Not used |

| Detections Field Name | Type| Description|
|---------|---------|---------|
| `id` | string| Detection ID|
| `type` | string| Detection type|
| `region` | collection| Collection of values|
| `type` | string| Type of region|
| `points` | collection| Top left and bottom right points when the region type is RECTANGLE |
| `confidence` | float| Algorithm confidence|

| Attribute | Type | Description |
|---------|---------|---------|
| `VehicleType` | float | Detected vehicle types. Possible detections include  "VehicleType_Bicycle", "VehicleType_Bus", "VehicleType_Car", "VehicleType_Motorcycle", "VehicleType_Pickup_Truck", "VehicleType_SUV",  "VehicleType_Truck", "VehicleType_Van/Minivan", "VehicleType_type_other" |
| `VehicleColor` | float | Detected vehicle colors. Possible detections include "VehicleColor_Black", "VehicleColor_Blue", "VehicleColor_Brown/Beige", "VehicleColor_Green", "VehicleColor_Grey", "VehicleColor_Red", "VehicleColor_Silver", "VehicleColor_White", "VehicleColor_Yellow/Gold", "VehicleColor_color_other" |
| `confidence` | float| Algorithm confidence|

| SourceInfo Field Name | Type| Description|
|---------|---------|---------|
| `id` | string| Camera ID|
| `timestamp` | date| UTC date when the JSON payload was emitted in format YYYY-MM-DDTHH:MM:SS.ssZ |
| `width` | int | Video frame width|
| `height` | int | Video frame height|
| `frameId` | int | Frame identifier|

## Sample cognitiveservices.vision.vehicleanalysis-vehicleinpolygon-preview and cognitiveservices.vision.vehicleanalysis-vehicleinpolygon.cpu-preview output

The JSON below demonstrates an example of the vehicle in polygon operation graph output.

```json
{
    "events": [
        {
            "id": "1b812a6c-1fa3-3827-9769-7773aeae733f",
            "type": "vehicleInPolygonEvent",
            "detectionIds": [
                "de4256ea-8b38-4883-9394-bdbfbfa5bd41"
            ],
            "properties": {
                "@type": "type.googleapis.com/microsoft.rtcv.insights.VehicleInPolygonEventMetadata",
                "status": "PARKED"
            },
            "zone": "1",
            "trigger": ""
        }
    ],
    "sourceInfo": {
        "id": "vehicleInPolygonTest",
        "timestamp": "2022-09-21T20:36:47.737Z",
        "frameId": "3",
        "width": 0,
        "height": 0,
        "imagePath": ""
    },
    "detections": [
        {
            "type": "vehicle",
            "id": "de4256ea-8b38-4883-9394-bdbfbfa5bd41",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.18703125417232513,
                        "y": 0.32875001430511475,
                        "visible": false,
                        "label": ""
                    },
                    {
                        "x": 0.2650781273841858,
                        "y": 0.42500001192092896,
                        "visible": false,
                        "label": ""
                    }
                ],
                "name": "",
                "normalizationType": "UNSPECIFIED_NORMALIZATION"
            },
            "confidence": 0.9583820700645447,
            "attributes": [
                {
                    "task": "VehicleType",
                    "label": "Bicycle",
                    "confidence": 0.0005135730025358498
                },
                {
                    "task": "VehicleType",
                    "label": "Bus",
                    "confidence": 2.502854385966202e-07
                },
                {
                    "task": "VehicleType",
                    "label": "Car",
                    "confidence": 0.9575894474983215
                },
                {
                    "task": "VehicleType",
                    "label": "Motorcycle",
                    "confidence": 0.03809007629752159
                },
                {
                    "task": "VehicleType",
                    "label": "Pickup_Truck",
                    "confidence": 6.314369238680229e-05
                },
                {
                    "task": "VehicleType",
                    "label": "SUV",
                    "confidence": 0.003204471431672573
                },
                {
                    "task": "VehicleType",
                    "label": "Truck",
                    "confidence": 4.916510079056025e-07
                },
                {
                    "task": "VehicleType",
                    "label": "Van/Minivan",
                    "confidence": 0.00029918691143393517
                },
                {
                    "task": "VehicleType",
                    "label": "type_other",
                    "confidence": 0.00023934587079565972
                },
                {
                    "task": "VehicleColor",
                    "label": "Black",
                    "confidence": 0.7501943111419678
                },
                {
                    "task": "VehicleColor",
                    "label": "Blue",
                    "confidence": 0.02153826877474785
                },
                {
                    "task": "VehicleColor",
                    "label": "Brown/Beige",
                    "confidence": 0.0013857109006494284
                },
                {
                    "task": "VehicleColor",
                    "label": "Green",
                    "confidence": 0.0006621106876991689
                },
                {
                    "task": "VehicleColor",
                    "label": "Grey",
                    "confidence": 0.007349356077611446
                },
                {
                    "task": "VehicleColor",
                    "label": "Red",
                    "confidence": 0.1460476964712143
                },
                {
                    "task": "VehicleColor",
                    "label": "Silver",
                    "confidence": 0.015320491977036
                },
                {
                    "task": "VehicleColor",
                    "label": "White",
                    "confidence": 0.053948428481817245
                },
                {
                    "task": "VehicleColor",
                    "label": "Yellow/Gold",
                    "confidence": 0.0030805091373622417
                },
                {
                    "task": "VehicleColor",
                    "label": "color_other",
                    "confidence": 0.0004731453664135188
                }
            ],
            "metadata": {
                "tracking_id": ""
            }
        }
    ],
    "schemaVersion": "2.0"
}
```

| Event Field Name | Type| Description|
|---------|---------|---------|
| `id` | string| Event ID|
| `type` | string| Event type|
| `detectionsId` | array| Array of unique identifier of the vehicle detection that triggered this event|
| `properties` | collection| Collection of values status [PARKED/EXITED]|
| `zone` | string | The "name" field of the polygon that represents the zone that was crossed|
| `trigger` | string| Not used |

| Detections Field Name | Type| Description|
|---------|---------|---------|
| `id` | string| Detection ID|
| `type` | string| Detection type|
| `region` | collection| Collection of values|
| `type` | string| Type of region|
| `points` | collection| Top left and bottom right points when the region type is RECTANGLE |
| `confidence` | float| Algorithm confidence|

| Attribute | Type | Description |
|---------|---------|---------|
| `VehicleType` | float | Detected vehicle types. Possible detections include  "VehicleType_Bicycle", "VehicleType_Bus", "VehicleType_Car", "VehicleType_Motorcycle", "VehicleType_Pickup_Truck", "VehicleType_SUV",  "VehicleType_Truck", "VehicleType_Van/Minivan", "VehicleType_type_other" |
| `VehicleColor` | float | Detected vehicle colors. Possible detections include "VehicleColor_Black", "VehicleColor_Blue", "VehicleColor_Brown/Beige", "VehicleColor_Green", "VehicleColor_Grey", "VehicleColor_Red", "VehicleColor_Silver", "VehicleColor_White", "VehicleColor_Yellow/Gold", "VehicleColor_color_other" |
| `confidence` | float| Algorithm confidence|

| SourceInfo Field Name | Type| Description|
|---------|---------|---------|
| `id` | string| Camera ID|
| `timestamp` | date| UTC date when the JSON payload was emitted in format YYYY-MM-DDTHH:MM:SS.ssZ |
| `width` | int | Video frame width|
| `height` | int | Video frame height|
| `frameId` | int | Frame identifier|

## Zone and line configuration for vehicle analysis

For guidelines on where to place your zones for vehicle analysis, you can refer to the [zone and line placement](./Project-Archon-Zone-and-Line-Placement.md) guide for spatial analysis. Configuring zones for vehicle analysis can be more straightforward than zones for spatial analysis if the parking spaces are already defined in the zone which you're analyzing.

## Camera placement for vehicle analysis

For guidelines on where and how to place your camera for vehicle analysis, refer to the [camera placement](./Project-Archon-Camera-Placement.md) guide found in the spatial analysis documentation. Other limitations to consider include the height of the camera mounted in the parking lot space. When you analyze vehicle patterns, a higher vantage point is ideal to ensure that the camera's field of view is wide enough to accommodate one or more vehicles, depending on your scenario.

## Billing

The vehicle analysis container sends billing information to Azure, using a Computer Vision resource on your Azure account. The use of vehicle analysis in public preview is currently free.

Azure Cognitive Services containers aren't licensed to run without being connected to the metering / billing endpoint. You must enable the containers to communicate billing information with the billing endpoint at all times. Cognitive Services containers don't send customer data, such as the video or image that's being analyzed, to Microsoft.

## Next steps

* Go back to the ![spatial analysis documentation](spatial-analysis-container.md)

# Install and run vehicle analysis (Private Preview)

Vehicle analysis is a set of capabilities that, when used with the spatial analysis container, enable you to analyze real-time streaming video to understand vehicle placement, movement, and characteristics, as well as relationships between people and vehicles. In this article, you'll learn how to use the capabilities of the spatial analysis container to deploy vehicle analysis operations.

## Vehicle analysis operations

Similarly to spatial analysis, vehicle analysis enables the analysis of real-time streaming video from camera devices. For each camera device you configure, the operations for vehicle analysis will generate an output stream of JSON messages that are being egressed to your instance of Azure IoT Hub.

The following operations for vehicle analysis are available in the current spatial analysis container. Vehicle analysis offers operations optimized for both GPU and CPU (CPU operations including the *.cpu* distinction).

| Operation Identifier | Description |  
| -------------------- | ---------------------------------------- |  
| **cognitiveservices.vision.spatialanalysis-personnexttomovingvehicle-preview** and **cognitiveservices.vision.spatialanalysis-personnexttomovingvehicle.cpu-preview** | Identifies when a person is in the path of a vehicle that is moving. Emits a *relationship* event when a person is detected, and predicates *near* to describe the relationship to the vehicle. |

### Operation parameters for person next to moving vehicle

| Operation parameters| Description|
|---------|---------|
| Operation ID | The Operation Identifier from table above.|
| enabled | Boolean: true or false|
| VIDEO_URL| The RTSP URL for the camera device(Example: `rtsp://username:password@url`). Spatial analysis supports H.264 encoded stream either through RTSP, HTTP, or MP4. |
| VIDEO_SOURCE_ID | A friendly name for the camera device or video stream. This will be returned with the event JSON output.|
| VIDEO_IS_LIVE| True for camera devices; false for recorded videos.|
| VIDEO_DECODE_GPU_INDEX| Which GPU to decode the video frame. By default it is 0. Should be the same as the `gpu_index` in other node config like `VICA_NODE_CONFIG`, `DETECTOR_NODE_CONFIG`. *This variable supports GPU operations only*|
| ENABLE_PERSONNEXTVEHICLE | Default is set to **enabled** |
| ENABLED_MOVING_VEHICLE  | Default value is set to **true** to enable moving vehicle detection |

<!-- If you are interested in understanding attributes about people involved in near vehicle events (EX. a person in a blue vest or a person in a hard hat) then refer to Person Attribute documentation to learn how to enable this feature. 
 -->

### Zone configuration for cognitiveservices.vision.vehicleanalysis-personnexttomovingvehicle-preview and cognitiveservices.vision.vehicleanalysis-personnexttomovingvehicle.cpu-preview

Currently, the person next to moving vehicle operation runs on the full frame.

Below is the graph optimized for the **person next to moving vehicle** operation:

```json
{
    "globalSettings": {
        "PlatformTelemetryEnabled": false,
        "CustomerTelemetryEnabled": false
    },
    "graphs": {
        "personnexttomovingvehicle-preview": {
            "operationId": "cognitiveservices.vision.spatialanalysis- personnexttomovingvehicle",
            "version": 1,
            "enabled": true,
            "parameters": {
                "ENABLE_VEHICLEATTRIBUTESCLASSIFICATION": true,
                "ENABLE_PERSONATTRIBUTESCLASSIFICATION": true,
                "VISION_ENDPOINT_URL": "TBD",
                "VISION_SUBSCRIPTION_KEY": "TBD",
                "PERSON_ATTRIBUTES_MODEL_ID": "TBD",
                "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
                "VIDEO_DECODE_GPU_INDEX": 0,
                "VIDEO_SOURCE_ID": "TBD",
                "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.0,0.0],[0.0,1.0],[1.0,1.0],[1.0,0.0],[0.0,0.0]], \"events\": [{\"type\":\"count\", \"config\":{\"trigger\": \"event\", \"threshold\":50.0, \"focus\": \"footprint\"}}]}]}",
                "VIDEO_URL": "TBD",
            }
        }
    }
}
```

## Sample cognitiveservices.vision.vehicleanalysis-personnexttomovingvehicle-preview and cognitiveservices.vision.vehicleanalysis-personnexttomovingvehicle.cpu-preview output

```json
{
    "events": [
        {
            "id": "dab40936-c427-4b8e-a36e-f8a2a5c8fec9",
            "type": "relationship",
            "detectionIds": [
                "1daf34f72ac84924bc19941ca846f69c",
                "ec6a14a4-8774-41a5-934c-e15ae8c4afa1"
            ],
            "properties": {
                "@type": "type.googleapis.com/microsoft.rtcv.insights.RelationshipEventMetadata",
                "predicates": {
                    "near": 0.89423084,
                    "not_near": 0.10576915
                }
            }
        }
    ],
    "sourceInfo": {
        "id": "C801 Camera 1",
        "timestamp": "2022-05-17T12:01:37.432Z",
        "width": 1920,
        "height": 1080,
        "frameId": "5",
        "imagePath": "C801 Camera 1"
    },
    "detections": [
        {
            "type": "person",
            "id": "1daf34f72ac84924bc19941ca846f69c",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.88102743636822567,
                        "y": 0.29720670542747912,
                        "visible": true
                    },
                    {
                        "x": 0.90263585363470555,
                        "y": 0.36079764520823682,
                        "visible": true
                    }
                ],
                "normalizationType": "NORMALIZED"
            },
            "confidence": 0.41010501980781555,
            "attributes": [
                {
                    "task": "head",
                    "label": "HasHelmet",
                    "confidence": 0.28970444202423096
                },
                {
                    "task": "head",
                    "label": "NoHelmet",
                    "confidence": 0.71029561758041382
                },
                {
                    "task": "vest",
                    "label": "HasVest",
                    "confidence": 0.11232931166887283
                },
                {
                    "task": "vest",
                    "label": "NoVest",
                    "confidence": 0.88767069578170776
                },
                {
                    "task": "stripes",
                    "label": "ReflectiveStripes",
                    "confidence": 0.072777517139911652
                },
                {
                    "task": "stripes",
                    "label": "NoReflectiveStripes",
                    "confidence": 0.927222490310669
                },
                {
                    "task": "human",
                    "label": "FullBody",
                    "confidence": 0.00044087698915973306
                },
                {
                    "task": "human",
                    "label": "PartialBody",
                    "confidence": 0.014238492585718632
                },
                {
                    "task": "human",
                    "label": "FalseDetection",
                    "confidence": 0.98532062768936157
                }
            ],
            "metadata": {
                "speed": "0.0",
                "trackingId": "",
                "footprintY": "0.0",
                "centerGroundPointX": "0.0",
                "groundOrientationAngle": "Infinity",
                "footprintX": "0.0",
                "centerGroundPointY": "0.0",
                "mappedImageOrientation": "Infinity"
            }
        },
        {
            "type": "car",
            "id": "ec6a14a4-8774-41a5-934c-e15ae8c4afa1",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.56531250476837158,
                        "y": 0.34499996900558472
                    },
                    {
                        "x": 0.87656247615814209,
                        "y": 0.65999990701675415
                    }
                ]
            },
            "confidence": 0.99401170015335083,
            "attributes": [
                {
                    "task": "VehicleType",
                    "label": "Bicycle",
                    "confidence": 3.7772437622152211e-07
                },
                {
                    "task": "VehicleType",
                    "label": "Bus",
                    "confidence": 1.6653397096888511e-06
                },
                {
                    "task": "VehicleType",
                    "label": "Car",
                    "confidence": 0.74716722965240479
                },
                {
                    "task": "VehicleType",
                    "label": "Motorcycle",
                    "confidence": 8.0347572293248959e-06
                },
                {
                    "task": "VehicleType",
                    "label": "Pickup_Truck",
                    "confidence": 0.0006719407974742353
                },
                {
                    "task": "VehicleType",
                    "label": "SUV",
                    "confidence": 0.1655777245759964
                },
                {
                    "task": "VehicleType",
                    "label": "Truck",
                    "confidence": 1.9298870483908104e-06
                },
                {
                    "task": "VehicleType",
                    "label": "Van/Minivan",
                    "confidence": 0.086554467678070068
                },
                {
                    "task": "VehicleType",
                    "label": "type_other",
                    "confidence": 1.654803054407239e-05
                },
                {
                    "task": "VehicleColor",
                    "label": "Black",
                    "confidence": 0.55253130197525024
                },
                {
                    "task": "VehicleColor",
                    "label": "Blue",
                    "confidence": 1.634629484215111e-06
                },
                {
                    "task": "VehicleColor",
                    "label": "Brown/Beige",
                    "confidence": 0.00063743896316736937
                },
                {
                    "task": "VehicleColor",
                    "label": "Green",
                    "confidence": 5.83013445520919e-07
                },
                {
                    "task": "VehicleColor",
                    "label": "Grey",
                    "confidence": 0.32764473557472229
                },
                {
                    "task": "VehicleColor",
                    "label": "Red",
                    "confidence": 1.2104210327379405e-05
                },
                {
                    "task": "VehicleColor",
                    "label": "Silver",
                    "confidence": 0.11916784942150116
                },
                {
                    "task": "VehicleColor",
                    "label": "White",
                    "confidence": 9.9297915312490659e-07
                },
                {
                    "task": "VehicleColor",
                    "label": "Yellow/Gold",
                    "confidence": 1.6294121607529632e-08
                },
                {
                    "task": "VehicleColor",
                    "label": "color_other",
                    "confidence": 3.3891310522449203e-06
                },
                {
                    "task": "movement",
                    "label": "moving",
                    "confidence": 0.0032303258776664734
                },
                {
                    "task": "movement",
                    "label": "not_moving",
                    "confidence": 0.99676966667175293
                }
            ],
            "metadata": {
                "tracking_id": ""
            }
        }
    ],
    "schemaVersion": "2.0"
}
```

| Event Field Name | Type| Description|
|---------|---------|---------|
| `id` | string| Event ID|
| `type` | string| Event type|
| `detectionsId` | array| Array of size 1 of unique identifier of the vehicle detection that triggered this event|
| `properties` | collection| Collection of values|
| `trackingId` | string| Unique identifier of the vehicle detected|
| `status` | string| 'Enter' or 'Exit'|
| `side` | int| The number of the side of the polygon that the vehicle crossed. Each side is a numbered edge between the two vertices of the polygon that represents your zone. The edge between the first two vertices of the polygon represents first side|
| `zone` | string | The "name" field of the polygon that represents the zone that was crossed|
| `trigger` | string| The trigger type is 'event' or 'interval' depending on the value of `trigger` in PARKING_REGION |
<br>

| Detections Field Name | Type| Description|
|---------|---------|---------|
| `id` | string| Detection ID|
| `type` | string| Detection type|
| `region` | collection| Collection of values|
| `type` | string| Type of region|
| `points` | collection| Top left and bottom right points when the region type is RECTANGLE |
| `confidence` | float| Algorithm confidence|
<br>

| Attribute | Type | Description |
|---------|---------|---------|
| `VehicleType` | float | Detected vehicle types. Possible detections include  "VehicleType_Bicycle", "VehicleType_Bus", "VehicleType_Car", "VehicleType_Motorcycle", "VehicleType_Pickup_Truck", "VehicleType_SUV",  "VehicleType_Truck", "VehicleType_Van/Minivan", "VehicleType_type_other" |
| `VehicleColor` | float | Detected vehicle colors. Possible detections include "VehicleColor_Black", "VehicleColor_Blue", "VehicleColor_Brown/Beige", "VehicleColor_Green", "VehicleColor_Grey", "VehicleColor_Red", "VehicleColor_Silver", "VehicleColor_White", "VehicleColor_Yellow/Gold", "VehicleColor_color_other" |
| `movement` | float | Detected vehicle motion. Outputs "moving" or "not_moving" |
<br>

| SourceInfo Field Name | Type| Description|
|---------|---------|---------|
| `id` | string| Camera ID|
| `timestamp` | date| UTC date when the JSON payload was emitted in format YYYY-MM-DDTHH:MM:SS.ssZ |
| `width` | int | Video frame width|
| `height` | int | Video frame height|
| `frameId` | int | Frame identifier|

<br>