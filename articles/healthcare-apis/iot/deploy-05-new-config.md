---
title: Configuring the MedTech service for deployment using the Azure portal - Azure Health Data Services
description: In this article, you'll learn how to configure the MedTech service for manual deployment using the Azure portal.
author: mcevoy-building7
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 09/17/2022
ms.author: v-smcevoy
---

# Configure the MedTech service for manual deployment using the Azure portal

Before you can deploy the MedTech service manually, you must configure several things:

- Configure MedTech service to ingest data
- Configure device mapping
- Configure destination mapping
- Configure (optional) tags

## Configure the MedTech service to ingest data

Follow these steps to configure the MedTech service to ingest data:

1. From the workspace you created in the [Prerequisites](deploy-04-new-prereq.md) section of manual deployment, click on the Create MedTech service box.

2. You will be taken to a new page for the MedTech service.

3. Click on the "Add MedTech service" button.

4. You will then be taken to the "Create MedTech service" blade.

### Fill in the Basics tab

Follow these steps in the Basics tab:

1. Enter the **MedTech service name**.

    The **MedTech service name** is a friendly, unique name for your MedTech service. For this example, we'll name the MedTech service `mt-azuredocsdemo`.

2. Enter the **Event Hubs Namespace**.

    The Event Hubs Namespace is the name of the **Event Hubs Namespace** that you've previously deployed. For this example, we'll use `eh-azuredocsdemo` for use with our MedTech service device messages. 

    > [!TIP]
    >
    > For information about deploying an Azure Event Hubs Namespace, see [Create an Event Hubs Namespace](../../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace).
    >
    > For more information about Azure Event Hubs Namespaces, see [Namespace](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#namespace) in the Features and terminology in Azure Event Hubs document.

3. Enter the **Events Hubs name**.

   The Event Hubs name is the event hub that you previously deployed within the Event Hubs Namespace. For this example, we'll use `devicedata` for use with our MedTech service device messages.  

   > [!TIP]
   >
   > For information about deploying an Azure event hub, see [Create an event hub](../../event-hubs/event-hubs-create.md#create-an-event-hub).

4. Enter the **Consumer group**.

    The Consumer group name is located by going to the **Overview** page of the Event Hubs Namespace and selecting the event hub to be used for the MedTech service device messages. In this example, the event hub is named `devicedata`.

5. Once inside of the event hub, select the **Consumer groups** button under **Entities** to display the name of the consumer group to be used by your MedTech service.

6. By default, a consumer group named **$Default** is created during the deployment of an event hub. Use this consumer group for your MedTech service deployment.

   > [!IMPORTANT]
   >
   > If you're going to allow access from multiple services to the device message event hub, it is highly recommended that each service has its own event hub consumer group. 
   >
   > Consumer groups enable multiple consuming applications to each have a separate view of the event stream, and to read the stream independently at their own pace and with their own offsets. For more information, see [Consumer groups](../../event-hubs/event-hubs-features.md#consumer-groups).
   >
   > Examples:

   > - Two MedTech services accessing the same device message event hub.

   > - A MedTech service and a storage writer application accessing the same device message event hub.

The Basics tab should now look like this:

  :::image type="content" source="media\iot-deploy-manual-in-portal\select-device-mapping-button.png" alt-text="Screenshot of Basics tab filled out correctly." lightbox="media\iot-deploy-manual-in-portal\select-device-mapping-button.png":::

You are now ready to go on to the Device mapping tab.

## Configure the Device mapping properties

> [!TIP]
>
> The IoMT Connector Data Mapper is an open source tool to visualize the mapping configuration for normalizing a device's input data, and then transforming it into FHIR resources. You can use this tool to edit and test Device and FHIR destination mappings, and to export the mappings to be uploaded to a MedTech service in the Azure portal. This tool also helps you understand your device's Device and FHIR destination mapping configurations.
>
> For more information regarding Device mappings, see our GitHub open source and Azure Docs documentation:
>
> [IoMT Connector Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)
>
> [Device Content Mapping](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#device-content-mapping)
>
> [How to use Device mappings](how-to-use-device-mappings.md)

Select the Device mapping tab under Create MedTech service. Follow these steps:

1. Get the appropriate JSON code from the IoMT Connector Data Mapper. Under the **Device mapping** tab, enter the JSON code for the template you want to use. When you enter the template code, the Device mapping code will be displayed.

2. If the Device code looks correct, select the **Next: Destination >** button to display a new box you need to enter the destination properties associated with your MedTech service.

## Configure FHIR Destination properties

Follow these two steps to configure the FHIR destination properties:

1. Under the **Destination** tab, enter the destination properties associated with your MedTech service:

    - Enter the name of your **FHIR server**.

      The **FHIR Server** name (also known as the **FHIR service**) is located by using the **Search** bar at the top of the screen to go to the FHIR service that you've deployed and by selecting the **Properties** button. Copy and paste the **Name** string into the **FHIR Server** text field. In this example, the **FHIR Server** name is `fs-azuredocsdemo`.

    - Enter the **Destination Name**.

      The **Destination Name** is a friendly name for the destination. Enter a unique name for your destination. In this example, the **Destination Name** is `fs-azuredocsdemo`.

    - Select **Create** or **Lookup** for the **Resolution Type**.

      > [!NOTE]
      >
      > For the MedTech service destination to create a valid observation resource in the FHIR service, a device resource and patient resource **must** exist in the FHIR service, so the observation can properly reference the device that created the data, and the patient the data was measured from. There are two modes the MedTech service can use to resolve the device and patient resources.

      - If you select **Create**:

        The MedTech service destination attempts to retrieve a device resource from the FHIR service using the [device identifier](https://www.hl7.org/fhir/device-definitions.html#Device.identifier) included in the normalized message. It also attempts to retrieve a patient resource from the FHIR service using the [patient identifier](https://www.hl7.org/fhir/patient-definitions.html#Patient.identifier) included in the normalized message. If either resource isn't found, new resources will be created (device, patient, or both) containing just the identifier contained in the normalized message. When you use the **Create** option, both a device identifier and a patient identifier can be configured in the device mapping. In other words, when the MedTech service destination is in **Create** mode, it can function normally **without** adding device and patient resources to the FHIR service.

      - If you select **Lookup**:

        The MedTech service destination attempts to retrieve a device resource from the FHIR service using the device identifier included in the normalized message. If the device resource isn't found, an error will occur, and the data won't be processed. For **Lookup** to function properly, a device resource with an identifier matching the device identifier included in the normalized message **must** exist and the device resource **must** have a reference to a patient resource that also exists. In other words, when the MedTech service destination is in the Lookup mode, device and patient resources **must** be added to the FHIR service before data can be processed. If the MedTech service attempts to look up resources that don't exist on the FHIR service, a **DeviceNotFoundException** and/or a **PatientNotFoundException** error(s) will be generated based on which resources aren't present.

       > [!TIP]
       > 
       > For more information regarding Destination mappings, see the GitHub and Azure Docs documentation:
       >
       > [FHIR mapping](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#fhir-mapping).
       >
       > [How to us FHIR destination mappings](how-to-use-fhir-mappings.md)

2. After you have entered the FHIR server, Destination name, and Resolution type, you then must enter an appropriate JSON template request in the large box below the first three boxes. Get this code from the [IoMT Connector Data Mapper Tool](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper). You will then receive the FHIR Destination mapping code.

Before you go on to the **Review + create** button, you have the option select the **Next: Tags >** button if you want to configure tags.

## (Optional) Configure Tags

Tags are name and value pairs used for categorizing resources. For more information about tags, see [Use tags to organize your Azure resources and management hierarchy](../../azure-resource-manager/management/tag-resources.md).

Follow these steps to create tags:

1. Under the **Tags** tab, enter the tag properties associated with the MedTech service.

    - Enter a **Name**.
    - Enter a **Value**.

2. Once you've entered your tag(s), you are ready to go on.

## Create the MedTech service

The final step for creating the MedTech service is to select the **Review + create** button.

If there were no errors, you should see a final screen that displays a **Validation success** message. You should see below that the values for the following values:

### Basics

- MedTech service name
- Event Hubs name
- Consumer group
- Event Hubs namespace

### Destination

- FHIR server
- Destination name
- Resolution type

Your screen should look something like this:

   :::image type="content" source="media\iot-deploy-manual-in-portal\validate-and-review-medtech-service.png" alt-text="Screenshot of validation success and a red box around the Create button." lightbox="media\iot-deploy-manual-in-portal\validate-and-review-medtech-service.png":::

   > [!NOTE]
   >
   > If your MedTech service didn't validate, review the validation failure message, and troubleshoot the issue. It's recommended that you review the properties under each MedTech service tab that you've configured.

## Next steps

In this article, you were shown how to configure MedTech service in preparation for deployment. To learn about the next step, see

>[!div class="nextstepaction"]
>[Creating a manual deployment of the MedTech service using the Azure portal](deploy-06-new-deploy.md)

FHIR&#174; is a registered trademark of Health Level Seven International, registered in the U.S. Trademark Office and is used with their permission.
