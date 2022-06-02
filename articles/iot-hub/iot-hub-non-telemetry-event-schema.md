---
title: Azure IoT Hub non-telemetry event schemas
description: This article provides the properties and schema for Azure IoT Hub non-telemetry events. It lists the available event types, an example event, and event properties.
author: kgremban
ms.author: kgremban  
ms.topic: conceptual
ms.date: 06/01/2022
ms.service: iot-hub
services: iot-hub
---

# Azure IoT Hub non-telemetry event schemas

This article provides the properties and schema for non-telemetry events emitted by Azure IoT Hub. Non-telemetry events are different from device-to-cloud and cloud-to-device messages in that they are emitted directly by IoT Hub in response to specific kinds of changes on your devices. For example, lifecycle changes like a device or module being created or deleted, or connection state changes like device connects and disconnects. To observe non-telemetry events, you must have an appropriate message route configured. To learn more about IoT Hub message routing, see [IoT Hub message routing](iot-hub-devguide-messages-d2c.md).

## Available event types

Azure IoT Hub emits the non-telemetry events in the following categories:

| Event category | Description |
| ---------- | ----------- |
| Device Connection State Events | Emitted when a device connects to or disconnects from an IoT hub. |
| Device Lifecycle Events | Emitted when a device or module is created on or deleted from an IoT hub. |
| Device Twin Change Events | Emitted when a device or module twin is changed or replaced. |
| Digital Twin Change Events | Emitted when a device's or module's digital twin is changed or replaced. |

## Event common properties

Non-telemetry events share several common properties.

### System properties

The following system properties are set by IoT Hub on each event. System properties should be preceded with a '$' character when referenced in a routing query.

| Property | Type |Description |
| -------- | ---- | ---------- |
| content_encoding | string | utf-8 |
| content_type | string |application/json |
| correlation_id | string | NEED A DEFINITION. (Sometimes a GUID, sometimes not.) |
| user_id | string | The name of IoT Hub that generated the event. |

### Application properties

The following application properties are set by IoT Hub on each event.

| Property | Type |Description |
| -------- | ---- | ---------- |
| deviceId | string | The unique identifier of the device. This case-sensitive string can be up to 128 characters long, and supports ASCII 7-bit alphanumeric characters plus the following special characters: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| hubName | string | The name of the IoT Hub that generated the event. |
| iothub-message-schema | string | The message schema associated with the event category; for example, *deviceLifecycleNotification*. |
| moduleId | string | The unique identifier of the module. This property is output only for module lifecycle and twin change events. This case-sensitive string can be up to 128 characters long, and supports ASCII 7-bit alphanumeric characters plus the following special characters: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| operationTimestamp | string | The ISO8601 timestamp of the operation. |
| opType | string | The identifier for the operation that generated the event. For example, *createDeviceIdentity*. |

### Annotations

The following annotations are stamped by IoT Hub on each event.

| Property | Type | Description |
| -------- | ---- | ----------- |
| iothub-connection-device-id | string | The unique identifier of the device. This case-sensitive string can be up to 128 characters long, and supports ASCII 7-bit alphanumeric characters plus the following special characters: `- : . + % _ # * ? ! ( ) , = @ ; $ '`.  |
| iothub-connection-module-id | string | The unique identifier of the module. This property is output only for module life cycle and twin events. This case-sensitive string can be up to 128 characters long, and supports ASCII 7-bit alphanumeric characters plus the following special characters: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| iothub-enqueuedtime | number | DESCRIPTION NEEDED. 1653677358153 |
| iothub-message-source | string | A value corresponding the the event categories that identifies the message source; for example, *deviceLifecycleEvents*. |
| x-opt-sequence-number  | number | DESCRIPTION NEEDED. |
| x-opt-offset | string | DESCRIPTION NEEDED. |
| x-opt-enqueued-time | number | DESCRIPTION NEEDED. 1653677358262 |

## Connection state events

Connection state events are emitted whenever a device or module connects or disconnects from the IoT hub.

**Application properties**: The following table shows how application property values are set for connection state events:  

| Property | Value |
| ---- | ----------- |
| iothub-message-schema | deviceConnectionStateNotification |
| opType | One of the following values: deviceConnected, deviceDisconnected, moduleConnected, or moduleDisconnected. |

**Annotations**: The following table shows how annotations are set for connection state events:

| Property | Value |
| ---- | ----------- |
| iothub-message-source |  deviceConnectionStateEvents |

**Payload**: The payload contains a sequence number. The sequence number is a string representation of a hexadecimal number. You can use string compare to identify the larger number. If you're converting the string to hex, then the number will be a 256-bit number. The sequence number is strictly increasing, and the latest event will have a higher number than other events. This is useful if you have frequent device connects and disconnects, and want to ensure only the latest event is used to trigger a downstream action.

### Example

The following JSON shows a device connection state event emitted when a device disconnects.

```json
{
    "event": {
        "origin": "contoso-device-1",
        "module": "",
        "interface": "",
        "component": "",
        "properties": {
            "system": {
                "content_encoding": "utf-8",
                "content_type": "application/json",
                "correlation_id": "98dcbcf6-3398-c488-c62c-06330e65ea98",
                "user_id": "contoso-routing-hub"
            },
            "application": {
                "hubName": "contoso-routing-hub",
                "deviceId": "contoso-device-1",
                "opType": "deviceDisconnected",
                "iothub-message-schema": "deviceConnectionStateNotification",
                "operationTimestamp": "2022-06-01T18:43:04.5561024Z"
            }
        },
        "annotations": {
            "iothub-connection-device-id": "contoso-device-1",
            "iothub-enqueuedtime": 1654109018051,
            "iothub-message-source": "deviceConnectionStateEvents",
            "x-opt-sequence-number": 72,
            "x-opt-offset": "37344",
            "x-opt-enqueued-time": 1654109018176
        },
        "payload": {
            "sequenceNumber": "000000000000000001D8713FF7E0851400000002000000000000000000000007"
        }
    }
}
```

## Device lifecycle events

Device lifecycle events are emitted whenever a device or module is created or deleted from the identity registry. For more detail about when device lifecycle events are generated, see [Device and module lifecycle notifications](iot-hub-devguide-identity-registry.md#device-and-module-lifecycle-notifications).

**Application properties**: The following table shows how application property values are set for device lifecycle events:  

| Property | Value |
| ---- | ----------- |
| iothub-message-schema | deviceLifecycleNotification |
| opType | One of the following values: createDeviceIdentity, deleteDeviceIdentity, createModuleIdentity, or deleteModuleIdentity. |

**Annotations**: The following table shows how annotations are set for device lifecycle events:

| Property | Value |
| ---- | ----------- |
| iothub-message-source |  deviceLifecycleEvents |

**Payload**: The payload contains the device ID and module ID, the twin etag, the version property, and the tags, properties and associated metadata of the device or module twin.

### Example

The following JSON shows a device lifecycle event emitted when a module is created.

```json
{
    "event": {
        "origin": "contoso-device-2",
        "module": "module-1",
        "interface": "",
        "component": "",
        "properties": {
            "system": {
                "content_encoding": "utf-8",
                "content_type": "application/json",
                "correlation_id": "c5a4e6986c",
                "user_id": "contoso-routing-hub"
            },
            "application": {
                "hubName": "contoso-routing-hub",
                "deviceId": "contoso-device-2",
                "operationTimestamp": "2022-05-27T18:49:38.4904785Z",
                "moduleId": "module-1",
                "opType": "createModuleIdentity",
                "iothub-message-schema": "moduleLifecycleNotification"
            }
        },
        "annotations": {
            "iothub-connection-device-id": "contoso-device-2",
            "iothub-connection-module-id": "module-1",
            "iothub-enqueuedtime": 1653677378534,
            "iothub-message-source": "deviceLifecycleEvents",
            "x-opt-sequence-number": 62,
            "x-opt-offset": "31768",
            "x-opt-enqueued-time": 1653677378643
        },
        "payload": {
            "deviceId": "contoso-device-2",
            "moduleId": "module-1",
            "etag": "AAAAAAAAAAE=",
            "version": 2,
            "properties": {
                "desired": {
                    "$metadata": {
                        "$lastUpdated": "0001-01-01T00:00:00Z"
                    },
                    "$version": 1
                },
                "reported": {
                    "$metadata": {
                        "$lastUpdated": "0001-01-01T00:00:00Z"
                    },
                    "$version": 1
                }
            }
        }
    }
}
```

## Device twin change events

Device twin change events are emitted whenever a device twin or a module twin is updated or replaced. In some cases, several changes may be packaged in a single event. To learn more, see [Device twin backend operations](iot-hub-devguide-device-twins.md#back-end-operations) or [Module twin backend operations](iot-hub-devguide-module-twins.md#back-end-operations).

**Application properties**: The following table shows how application property values are set for device twin change events:  

| Property | Value |
| ---- | ----------- |
| iothub-message-schema | twinChangeNotification |
| opType | One of the following values: replaceTwin or updateTwin. |

**Annotations**: The following table shows how annotations are set for device twin change events:

| Property | Value |
| ---- | ----------- |
| iothub-message-source |  twinChangeEvents |

**Payload**: On an update, the payload contains the version property of the twin and the updated tags and properties and their associated metadata. On replace, the payload contains the device ID and module ID, the twin etag, the version property, and all the tags, properties and associated metadata of the device or module twin.

### Example

The following JSON shows a twin change event emitted for an update of a desired property and a tag on a module twin.

```json
{
    "event": {
        "origin": "contoso-device-3",
        "module": "module-1",
        "interface": "",
        "component": "",
        "properties": {
            "system": {
                "content_encoding": "utf-8",
                "content_type": "application/json",
                "correlation_id": "4d1f1e2e74f",
                "user_id": "contoso-routing-hub"
            },
            "application": {
                "hubName": "contoso-routing-hub",
                "deviceId": "contoso-device-3",
                "operationTimestamp": "2022-06-01T22:27:50.2612586Z",
                "moduleId": "module-1",
                "iothub-message-schema": "twinChangeNotification",
                "opType": "updateTwin"
            }
        },
        "annotations": {
            "iothub-connection-device-id": "contoso-device-3",
            "iothub-connection-module-id": "module-1",
            "iothub-enqueuedtime": 1654122470282,
            "iothub-message-source": "twinChangeEvents",
            "x-opt-sequence-number": 17,
            "x-opt-offset": "12352",
            "x-opt-enqueued-time": 1654122470329
        },
        "payload": {
            "version": 7,
            "tags": {
                "tag1": "new value"
            },
            "properties": {
                "desired": {
                    "property1": "new value",
                    "$metadata": {
                        "$lastUpdated": "2022-06-01T22:27:50.2612586Z",
                        "$lastUpdatedVersion": 6,
                        "property1": {
                            "$lastUpdated": "2022-06-01T22:27:50.2612586Z",
                            "$lastUpdatedVersion": 6
                        }
                    },
                    "$version": 6
                }
            }
        }
    }
}
```
