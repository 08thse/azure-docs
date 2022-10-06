---
title: How to manage linked IoT hubs with Device Provisioning Service (DPS)
description: This article shows how to link and manage IoT hubs with the Device Provisioning Service (DPS).
author: kgremban
ms.author: kgremban
ms.date: 09/19/2022
ms.topic: how-to
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
---

# How to link and manage IoT hubs

Device Provisioning Service can provision devices across one or more IoT hubs. Before DPS can provision devices to an IoT hub it must be linked to your DPS instance. Once linked, an IoT hub can be used in an allocation policy. Allocation policies determine how devices are assigned to IoT hubs by DPS. This article provides instruction on how to link IoT hubs and manage them in your DPS instance. 

## Linked IoT hubs and allocation policies

Allocation policies determine how DPS assigns devices to IoT hubs. Before an IoT hub can be used in an allocation policy, it must be linked to the DPS instance. Once an IoT hub is linked to DPS, it's eligible to participate in allocation. Whether it will participate in allocation depends on settings in the enrollment that a device is provisioning through. To learn about DPS allocation policies and how linked IoT hubs participate in them, see [Manage allocation policies](how-to-use-allocation-policies.md).

The following settings are important when working with linked IoT hubs:

* **Connection string**: Sets the IoT Hub connection string that DPS uses to connect to the linked IoT hub. The connection string is based on one of the IoT hub's shared access policies. DPS needs the following permissions on the IoT hub: RegistryWrite and ServiceConnect. The connection string must be for a shared access policy that has these permissions. To learn more about IoT Hub shared access policies, see  [IoT Hub access control and permissions](../iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).

* **Allocation weight**: Determines the likelihood of an IoT hub being selected when DPS hashes device assignment across a set of IoT hubs. The value can be between one and 1000. The default is one (or **null**). Higher values increase the IoT hub's probability of being selected.

* **Apply allocation policy**: Sets whether the IoT hub participates in allocation policy. The default is **Yes** (true). If set to **No** (false), devices won't be assigned to the IoT hub. The IoT hub can still be selected on an enrollment, it just won't participate in allocation. You can use this setting to temporarily or permanently remove an IoT hub from participating in allocation; for example, if it's approaching the allowed number of devices.

## Add a linked IoT hub

When you link an IoT hub to your DPS instance it becomes available to participate in allocation. You can add IoT hubs that are inside or outside of your subscription.

* For enrollments that don't explicitly set the IoT hubs to apply allocation policy to, a newly linked IoT hub immediately begins participating in allocation.

* For enrollments that do explicitly set the IoT hubs to apply allocation policy to, you'll need to manually or programmatically add the new IoT hub to the enrollment settings for it to participate in allocation.

### Use Azure portal to link an IoT hub

In Azure portal, you can link an IoT hub either from the left menu of your DPS instance or from the enrollment when creating or updating an enrollment. In both cases, the IoT hub is scoped to the DPS instance (not just the enrollment).

To link an IoT hub to your DPS instance in Azure portal:

1. On the left menu of your DPS instance, select **Linked IoT hubs**.

1. At the top of the page, select **+ Add**.

1. On the **Add link to IoT hub** page, select the subscription that contains the IoT hub and then choose the name of the IoT hub from the **IoT hub** list.

1. After you select the IoT hub, choose an access policy that DPS will use to connect to the IoT hub. The **Access Policy** list shows all shared access policies defined on the selected IoT Hub that have both **Registry Write** and **Service Connect** permissions defined. The default is the *iothubowner* policy. Select the policy you want to use.  

1. Select **Save**.

To link an IoT hub to your DPS instance from an enrollment in Azure portal:

1. On the left menu of your DPS instance, select **Manage enrollments**.

1. On the **Manage enrollments** page:

    * To create a new enrollment, select either **+ Add enrollment group** or **+ Add individual enrollment** at the top of the page.

    * To update an existing enrollment, select it from the list under either the **Enrollment Groups** or **Individual Enrollments** tab.

1. On the **Add Enrollment** page (on create) or the **Enrollment details** page (on update), select the **Link a new IoT hub** button to link a new IoT hub to the DPS instance.

1. On the **Add link to IoT hub** page, select the subscription that contains the IoT hub and then choose the name of the IoT hub from the **IoT hub** list.

1. After you select the IoT hub, choose an access policy that DPS will use to connect to the IoT hub. The **Access Policy** list shows all shared access policies defined on the selected IoT Hub that have both **Registry Write** and **Service Connect** permissions defined. The default is the *iothubowner* policy. Select the policy you want to use.  

1. Select **Save**.

1. You're returned to the previous page. If you want to, you can select the newly linked IoT hub on the enrollment.

1. After you finish specifying any other fields necessary for the enrollment, save your settings.

> [!NOTE]
>
> In Azure portal, you can't set the *Allocation weight* and *Apply allocation policy* settings when you add a linked IoT hub. Instead, You can update these settings after the IoT hub is linked. To learn more, see [Update a linked IoT hub](#update-a-linked-iot-hub).

### Use Azure CLI to link an IoT hub

Use the [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create) Azure CLI command to link an IoT hub to your DPS instance.

For example, the following command links an IoT hub named *MyExampleHub* using a connection string for its *iothubowner* shared access policy:

```azurecli
az iot dps linked-hub create --dps-name MyExampleDps --resource-group MyResourceGroup --connection-string "HostName=MyExampleHub.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XNBhoasdfhqRlgGnasdfhivtshcwh4bJwe7c0RIGuWsirW0=" --location westus
```

DPS also supports creating linked IoT Hubs using the [Create or Update DPS resource](/rest/api/iot-dps/iot-dps-resource/create-or-update?tabs=HTTP) REST API, [Resource Manager templates](/azure/templates/microsoft.devices/provisioningservices?pivots=deployment-language-arm-template), and the [DPS Management SDKs](libraries-sdks.md#management-sdks).

## Update a linked IoT hub

You can update the settings on a linked IoT hub to change its allocation weight, whether it can have allocation policies applied to it, and the connection string that DPS uses to connect to it. When you update the settings for an IoT hub, the changes take effect immediately, whether the IoT hub is specified on an enrollment or used by default.

### Use Azure portal to update a linked IoT hub

In Azure portal, you can update the *Allocation weight* and *Apply allocation policy* settings.

To update the settings for a linked IoT hub using Azure portal:

1. On the left menu of your DPS instance, select **Linked IoT hubs**, then select the IoT hub from the list.

1. On the **Linked IoT hub details** page:

    :::image type="content" source="media/how-to-manage-linked-iot-hubs/set-linked-iot-hub-properties.png" alt-text="Screenshot that shows the linked IoT hub details page":::.

    * Use the **Allocation weight** slider or text box to choose a weight between one and 1000. The default is one.

    * Set the **Apply allocation policy** switch to specify whether the linked IoT hub should be included in allocation.

1. Complete any other required fields for your enrollment and save your settings.

> [!NOTE]
>
> You can't update the connection string that DPS uses to connect to the IoT hub from Azure portal. Instead, you can use the Azure CLI to update the connection string, or you can delete the linked IoT hub from your DPS instance and relink it. To learn more, see [Update keys for linked IoT hubs](#update-keys-for-linked-iot-hubs).

### Use Azure CLI to update a linked IoT hub

With the Azure CLI, you can update the *Allocation weight*, *Apply allocation policy*, and *Connection string* settings. For more information about updating the connection string setting.

Use the [az iot dps linked-hub update](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-update) command to update the allocation weight or apply allocation policies settings. For example, the following command sets the allocation weight and apply allocation policy for a linked IoT hub:

```azurecli
az iot dps linked-hub update --dps-name MyExampleDps --resource-group MyResourceGroup --linked-hub MyExampleHub --allocation-weight 2 --apply-allocation-policy true
```

Use the [az iot dps update](/cli/azure/iot/dps#az-iot-dps-update) command to update the connection string for the linked IoT hub. You can use the `--set` parameter along with the connection string for the IoT hub shared access policy you want to use. For details, see [Update keys for linked IoT hubs](#update-keys-for-linked-iot-hubs).

DPS also supports updating linked IoT Hubs using the [Create or Update DPS resource](/rest/api/iot-dps/iot-dps-resource/create-or-update?tabs=HTTP) REST API, [Resource Manager templates](/azure/templates/microsoft.devices/provisioningservices?pivots=deployment-language-arm-template), and the [DPS Management SDKs](libraries-sdks.md#management-sdks).

## Delete a linked IoT hub

When you delete a linked IoT hub from your DPS instance it will no longer be available to set in future enrollments. However, it may or may not be removed from allocations in current enrollments:

* For enrollments that don't explicitly set the IoT hubs to apply allocation policy to, a deleted linked IoT hub is no longer available for allocation.

* For enrollments that do explicitly set the IoT hubs to apply allocation policy to, you'll need to manually or programmatically remove the IoT hub from the enrollment settings for it to be removed from participation in allocation.

### Use Azure portal to delete a linked IoT hub

To delete a linked IoT hub from your DPS instance in Azure portal:

1. On the left menu of your DPS instance, select **Linked IoT hubs**.

1. From the list of IoT hubs, select the check box next to the IoT hub or IoT hubs you want to delete. Then select **Delete** at the top of the page and confirm your choice when prompted.

### Use Azure CLI to delete a linked IoT hub

Use the [az iot dps linked-hub delete](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-delete) command to remove a linked IoT hub from the DPS instance. For example, the following command removes the IoT hub named MyExampleHub:

```azurecli
az iot dps linked-hub update --dps-name MyExampleDps --resource-group MyResourceGroup --linked-hub MyExampleHub
```

DPS also supports deleting linked IoT Hubs from the DPS instance using the [Create or Update DPS resource](/rest/api/iot-dps/iot-dps-resource/create-or-update?tabs=HTTP) REST API, [Resource Manager templates](/azure/templates/microsoft.devices/provisioningservices?pivots=deployment-language-arm-template), and the [DPS Management SDKs](libraries-sdks.md#management-sdks).

## Update keys for linked IoT hubs

It may become necessary to either rotate or update the symmetric keys for an IoT hub that's been linked to DPS. In this case, you'll also need to update the connection string setting in DPS for the linked IoT hub. Be aware that provisioning to an IoT hub will fail during the interim between updating a key on the IoT hub and updating your DPS instance with the new connections string based on that key.

### Use Azure CLI to update keys

To update symmetric keys for a linked IoT hub with Azure CLS:

1. Use the [az iot hub policy renew-key](/cli/azure/iot/hub/policy#az-iot-hub-policy-renew-key) command to swap or regenerate the symmetric keys for the shared access policy on the IoT hub. For example, the following command renews the primary key for the *iothubowner* shared access policy on an IoT hub:

    ```azurecli
    az iot hub policy renew-key --hub-name MyExampleHub --name owner --rk primary
    ```

1. Use the [az iot hub connection-string show](/cli/azure/iot/hub/policy#az-iot-hub-az-iot-hub-connection-string-show) command to get the new connection string for the shared access policy. For example, the following command gets the primary connection string for the *iothubowner* shared access policy that the primary key was regenerated for in the previous command:

    ```azurecli
    az iot hub connection-string show --hub-name MyExampleHub --policy-name owner --key-type primary
    ```

1. Use the [az iot dps linked-hub list](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-show) command to find the position of the IoT hub in the collection of linked IoT hubs for your DPS instance. For example, the following command gets the primary connection string for the *owner* shared access policy that the primary key was regenerated for in the previous command:

    ```azurecli
    az iot dps linked-hub list --dos-name MyExampleDps
    ```

    The output will show the position of the linked IoT hub you want to update the connection string for in the table of linked IoT hubs maintained by your DPS instance. In this case, it's the first IoT hub in the list, *MyExampleHub*.

    ```json
    [
    {
        "allocationWeight": null,
        "applyAllocationPolicy": null,
        "connectionString": "HostName=MyExampleHub.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=****",
        "location": "centralus",
        "name": "MyExampleHub.azure-devices.net"
    },
    {
        "allocationWeight": null,
        "applyAllocationPolicy": null,
        "connectionString": "HostName=MyExampleHub-2.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=****",
        "location": "centralus",
        "name": "NyExampleHub-2.azure-devices.net"
    }
    ]
    ```

1. Use the [az iot dps update](/cli/azure/iot/dps#az-iot-dps-update) command to update the connection string for the linked IoT hub. You use the `--set` parameter and the position of the linked IoT hub in the `properties.iotHubs[]` table to target the IoT hub. For example, the following command updates the connection string for *MyExampleHub* returned first in the previous command:

    ```azurecli
    az iot dps update --name MyExampleDps --set properties.iotHubs[0].connectionString="HostName=MyExampleHub-2.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=NewTokenValue"
    ```

### Use Azure portal to update keys

You can't update the connection string setting for a linked IoT Hub when using Azure portal. Instead, you need to delete the linked IoT hub from your DPS instance and the re-add it.

To update symmetric keys for a linked IoT hub with Azure portal:

1. On the left menu of your DPS instance in Azure portal, select the IoT hub that you want to update the key(s) for.

1. On the **Linked IoT hub details** page, note down the values for *Allocation weight* and *Apply allocation policy*, you'll need these when you re-link the IoT hub to your DPS instance later. Then, select **Manage Resource** to go to the IoT hub.

1. On the left menu of the IoT hub, under **Hub settings**, select **Shared access policies**.

1. On **Shared access policies**, under **Manage shared access policies**, select the policy that your DPS instance uses to connect to the linked IoT hub.

1. At the top of the page, select **Regenerate primary key**, **Regenerate secondary key**, or **Swap keys**.

1. Navigate back to your DPS instance and select **Linked IoT hubs** on the left menu.

1. On the left menu of your DPS instance, select **Linked IoT hubs**.

1. Select the checkbox next to the IoT hub that you're updating the keys for, then select **Delete** at the top of the page and confirm the changes.

1. Follow the steps in [Link an IoT hub](#use-azure-portal-to-link-an-iot-hub) to re-link the IoT hub to your DPS instance.

1. If you need to restore the allocation weight and apply allocation policy settings, follow the steps in [Update a linked IoT hub](#use-azure-portal-to-update-a-linked-iot-hub) using the values you saved in step 2.

## Limitations

There are some limitations when working with linked IoT hubs and private endpoints. For more information, see [Private endpoint limitations](virtual-network-support.md#private-endpoint-limitations).

## Next steps

* To learn more about allocation policies, see [Manage allocation policies](how-to-use-allocation-policies.md).
