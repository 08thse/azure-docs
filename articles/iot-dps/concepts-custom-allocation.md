---
title: Using custom allocation policies with Azure IoT Hub Device Provisioning Service
description: Understand custom allocation policies with the Azure IoT Hub Device Provisioning Service (DPS)
author: kgremban
ms.author: kgremban
ms.date: 07/15/2022
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: devx-track-csharp, devx-track-azurecli
---

# Understand custom allocation policies with Azure IoT Hub Device Provisioning Service

A custom allocation policy gives you more control over how devices are assigned to an IoT hub. This is accomplished by using custom code in an [Azure Function](../azure-functions/functions-overview.md) to assign devices to an IoT hub. The device provisioning service calls your Azure Function code providing all relevant information about the device and the enrollment. Your function code is executed and returns the IoT hub information used to provisioning the device.

By using custom allocation policies, you define your own allocation policies when the policies provided by the Device Provisioning Service don't meet the requirements of your scenario.

For example, maybe you want to examine the certificate a device is using during provisioning and assign the device to an IoT hub based on a certificate property. Or, maybe you have information stored in a database for your devices and need to query the database to determine which IoT hub a device should be assigned to.

## Custom allocation policy request

DPS sends a POST request to your Azure Function on the following endpoint: `https://{your-function-app-name}.azurewebsites.net/api/{your-http-trigger}`

The request body is an **AllocationRequest** object:

| Property name | Description |
|---------------|-------------|
| individualEnrollment | Contains properties associated with the individual enrollment that the allocation request originated from. |
| enrollmentGroup | Contains the properties associated with the enrollment group that the allocation request originated from. |
| deviceRuntimeContext | A context that contains properties associated with the device that is registering. |
| linkedHubs | An array that contains the hostnames of the IoT hubs that are linked to the DPS instance that the allocation request originated from. The device may be assigned to any one of these IoT hubs. |

The **DeviceRuntimeContext** object has the following properties:

| Property | Type | Description |
|----------|------|-------------|
| registrationId | string | the registration ID provided by the device at runtime. Always present. |
| currentIoTHubHostName | string | The hostname of the IoT hub the device was previously assigned to (if any). Not present if this is an initial assignment. |
| currentDeviceId | string | THe device ID from the device's previous assignment (if any). Not present if this is an initial assignment. |
| x509 | X509DeviceAttestation | For X.509 attestation, contains certificate details. |
| symmetricKey | SymmetricKeyAttestation | For symmetric key attestation, contains primary and secondary key details. |
| tpm | TpmAttestation | For TPM attestation, contains endorsement key and storage root key details. |
| payload | object | Contains properties specified by the device in the payload property during registration. |

The following JSON shows the **AllocationRequest** object sent by DPS for a device registering through a symmetric key based enrollment group.

```json
{
   "enrollmentGroup":{
      "enrollmentGroupId":"contoso-custom-allocated-devices",
      "attestation":{
         "type":"symmetricKey"
      },
      "capabilities":{
         "iotEdge":false
      },
      "etag":"\"13003fea-0000-0300-0000-62d1d5e50000\"",
      "provisioningStatus":"enabled",
      "reprovisionPolicy":{
         "updateHubAssignment":true,
         "migrateDeviceData":true
      },
      "createdDateTimeUtc":"2022-07-05T21:27:16.8123235Z",
      "lastUpdatedDateTimeUtc":"2022-07-15T21:02:29.5922255Z",
      "allocationPolicy":"custom",
      "iotHubs":[
         "custom-allocation-toasters-hub.azure-devices.net",
         "custom-allocation-heatpumps-hub.azure-devices.net"
      ],
      "customAllocationDefinition":{
         "webhookUrl":"https://custom-allocation-function-app-3.azurewebsites.net/api/HttpTrigger1?****",
         "apiVersion":"2021-10-01"
      }
   },
   "deviceRuntimeContext":{
      "registrationId":"breakroom499-contoso-tstrsd-007",
      "symmetricKey":{
         
      }
   },
   "linkedHubs":[
      "custom-allocation-toasters-hub.azure-devices.net",
      "custom-allocation-heatpumps-hub.azure-devices.net"
   ]
}
```

Because this is the initial registration for the device, the **deviceRuntimeContext** property contains only the registration ID and the authentication details for the device. The following JSON shows the **deviceRuntimeContext** for a subsequent call to register the same device. Notice that the current IoT Hub hostname and device ID are included in the request.

```json
{
   "deviceRuntimeContext":{
      "registrationId":"breakroom499-contoso-tstrsd-007",
      "currentIotHubHostName":"custom-allocation-toasters-hub.azure-devices.net",
      "currentDeviceId":"breakroom499-contoso-tstrsd-007",
      "symmetricKey":{
         
      }
   },
}
```

## Custom allocation policy response

A successful request returns an **AllocationResponse** object.

| Property | Description |
|----------|-------------|
| initialTwin | An object that contains the desired properties and tags for the initial twin. If an initial twin is returned, it will override any twin migration settings in the enrollment. |
| iotHubHostname | The hostname of the IoT hub to assign the device to. This must be one of the IoT hubs passed in the **linkedHubs** property in the request. |
| payload | An object that contains data to be passed back to the device in the Registration response. The exact data will depend on the implicit contract defined by the developer between the device and the custom allocation function. | 

The following JSON shows the **AllocationResponse** object returned by a custom allocation function to DPS for the example registration above.

```json
{
   "iotHubHostName":"custom-allocation-toasters-hub.azure-devices.net",
   "initialTwin":{
      "properties":{
         "desired":{
            "state":"ready",
            "darknessSetting":"medium"
         }
      },
      "tags":{
         "deviceType":"toaster"
      }
   }
}
```

## Use device payloads in custom allocation

Devices can send a custom payload that is passed by DPS to your custom allocation webhook, which can then use that data in its logic. The webhook may use this data in a number of ways, perhaps to determine which IoT hub to assign the device to, or to look up information in an external database that might be used to set properties on the initial twin. Conversely, your webhook can return data back to the device through DPS, which may be used in the device's client-side logic.

For example, you may want to allocate devices based on the device model. In this case, you can configure the device to report its model information in the request payload when it registers with DPS. DPS will pass this payload to the custom allocation webhook, which will determine which IoT hub the device will be provisioned to based on the device model information. If needed, the webhook can return data back to the DPS as a JSON object in the webhook response, and DPS will return this data to your device in the registration response.

### Device sends data payload to DPS

A device calls the [register](/rest/api/iot-dps/device/runtime-registration/register-device) API to register with DPS. The request can be enhanced with the optional `pqyload` property. This property can contain any valid JSON object. The exact contents will depend on the requirements of your solution.

For attestation with TPM, the request body looks like the following:

```json
{ 
    "registrationId": "mydevice", 
    "tpm": { 
        "endorsementKey": "xxxx-device-endorsement-key-xxxxx", 
        "storageRootKey": "xxxx-device-storage-root-key-xxxxx" 
    }, 
    "payload": { "property1": "value1", "property2": {"propertyA":"valueA", "property2-2":1234}, ... } 
} 
```

### DPS sends data payload to custom allocation webhook

### Custom allocation webhook returns data to the DPS

If the custom allocation policy webhook wishes to return some data to the device, it will pass the data back as a JSON object in the webhook response. The change is in the payload section below.

```json
{ 
    "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
    "initialTwin": { 
        "tags": { 
            "tag1": true 
        }, 
        "properties": { 
            "desired": { 
                "prop1": true 
            } 
        } 
    }, 
    "payload": { A JSON object that contains the data returned by the webhook } 
} 
```

### DPS sends data payload to device
