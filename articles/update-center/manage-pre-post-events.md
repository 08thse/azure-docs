---
title: Manage the pre and post maintenance configuration events (preview)
description: The article provides the steps to manage the pre and post maintenance events.
ms.service: azure-update-manager
ms.date: 09/15/2023
ms.topic: how-to
ms.author: sudhirsneha
author: SnehaSudhirG
---

# Manage the Pre and Post maintenance events (preview)

This article explains about how to manage the pre and post maintenance events.

## Prerequisite

1. [Register Event Grid resource provider on your subscription](https://learn.microsoft.com/azure/api-management/how-to-event-grid#register-the-event-grid-resource-provider).

### Create a maintenance configuration

To create a maintenance configuration, follow these steps:
1. Sign in to the [Azure portal](https://portal.azure.com) and go to **Azure Update Manager**.
1. Under **Manage**, select **Machines**, **Maintenance Configuration**.
1. Select the appropriate **Subscription ID** and **Resource Group**.
1. Provide an appropriate name to maintenance configuration. 
1. Select **any region** and select the scope as **Guest(Azure VM, Arc enabled VMs)**.
1. Add a schedule based on your testing requirements. We recommend that you remember the time as this would be useful in the testing steps listed below.
1. After the validations are passed, select **Create** to create the maintenance configuration and wait for the deployment to finish.

    :::image type="content" source="./media/manage-pre-post-events/create-maintenance-configuration.png" alt-text="Screenshot that shows how to create maintenance configuration.":::

> [!NOTE]
> When you select a time which is at least 40 minutes from the current time, you are trying to create/edit the schedule, as the Pre Maintenance Events gets triggered close to specified time of -40 minutes. For example, if you select the time as 3:00 p.m. to run the schedule, the pre maintenance event must be at least 40 minutes before 3:00 p.m. which is 2:20 p.m.


## Manage Maintenance events

To manage the maintenance events, follow these steps:

1. Sign in to the [Azure portal](https://portal.azure.com) and go to **Azure Update Manager**.
1. Under **Manage**, select **Machines**, **Maintenance Configurations**.
1. In the **Maintenance Configuration** page, select  the configuration.
1. In the **Events** page, under **Settings**, select **Events** to view the maintenance configurations that you have created.
1. Select **+Event Subscription** to create Pre/Post Maintenance Event.

    :::image type="content" source="./media/manage-pre-post-events/maintenance-events.png" alt-text="Screenshot that shows the maintenance events":::

 1. In the **Create Event Subscription** page, enter the following details:
    - In the **Event Subscription Details** section, provide an appropriate name. 
    - Keep the schema as **Event Grid Schema**.
    - In the **Event Types** section, **Filter to Event Types**, select the event types that you want to get pushed to the endpoint or destination. You can select more than one option here.
    - In the **Endpoint details** section, select the endpoint where you want to receive the response from the **EventGrid**. It would help customers to trigger their Prescript.  
    - Select **Storage Queues** and provide the appropriate details such as **Subscription ID&**, **Storage account** and the **Queue name** where you would be receiving the event.
    
      :::image type="content" source="./media/manage-pre-post-events/create-event-subscription.png" alt-text="Screenshot on how to create event subscription.":::
     
   > [!NOTE]
   > As per the time that you configured in the maintenance configuration, which is 40 minutes before the stipulated time, with a (+2 or -3 minutes), the pre event notification is triggered. For example, if you select the time as 3:00 p.m. to run the schedule, the pre-event notification is triggered between 2:17 p.m. to 2:22 p.m.

### View maintenance configuration 

You can view the details related to the Maintenance configuration from Azure Resource Graph (ARG) with the following query: 

```kusto
    maintenanceresources 
    | where type =~ "microsoft.maintenance/maintenanceconfigurations/applyupdates" 
    | where properties.correlationId has "/subscriptions/<your-s-id> /resourcegroups/<your-rg-id> /providers/microsoft.maintenance/maintenanceconfigurations/<mc-name> /providers/microsoft.maintenance/applyupdates/" 
    | order by name desc 
    ```
    Ensure that you replace the following list in the above sample API so that it works:
    - Subscription ID (s-id selected in the prerequisites)
    - Resource Group (rg-id selected and created in the prerequisites)
    - Maintenance configuration name (mc-name)

    **Sample query**
    ```kusto
    maintenanceresources 
    | where type =~ "microsoft.maintenance/maintenanceconfigurations/applyupdates" 
    | where properties.correlationId has "/subscriptions/eee2cef4-bc47-4278-b4f8-cfc65f25dfd8/resourcegroups/fp02centraluseuap/providers/microsoft.maintenance/maintenanceconfigurations/prepostdemo7/providers/microsoft.maintenance/applyupdates/" 
    | order by name desc 
```
  :::image type="content" source="./media/manage-pre-post-events/arg-query.png" alt-text="Screenshot for ARG Query":::
   

## Cancel the maintenance event

- To cancel the maintenance event, use the cancellation API. The error message in the JSON obtained from the Azure Resource Graph for the corresponding maintenance configuration would be **Maintenance cancelled using Cancellation API**.

Invoke Cancellation API as mentioned below:
    
    ```rest
    C:\ProgramData\chocolatey\bin\ARMClient.exe put https://management.azure.com/<your-c-id-obtained-from-above>?api-version=2023-09-01-preview "{\"Properties\":{\"Status\": \"Cancel\"}}" -Verbose 
    ```
> [!NOTE]
> You must replace the **Coorelation ID** received from the above ARG query and replace it in the Cancellation API.
    
    ```rest
    C:\ProgramData\chocolatey\bin\ARMClient.exe put https://management.azure.com/subscriptions/eee2cef4-bc47-4278-b4f8-cfc65f25dfd8/resourcegroups/fp02centraluseuap/providers/microsoft.maintenance/maintenanceconfigurations/prepostdemo7/providers/microsoft.maintenance/applyupdates/20230810085400?api-version=2023-09-01-preview "{\"Properties\":{\"Status\": \"Cancel\"}}" -Verbose
       
    ```
  :::image type="content" source="./media/manage-pre-post-events/cancellation-api-user.png" alt-text="Screenshot for cancellation done by the user.":::

- If the maintenance job is cancelled by the system due to any reason, the error message in the JSON obtained from Azure Resource Graph for the corresponding maintenance configuration would be **Maintenance schedule cancelled due to internal platform failure**.

> [!NOTE]
> When you invoke the cancellation flow, it will be honored from 40 mins prior when the pre-maintenance event has been triggered till the last 10 mins for the event. For example, if you select the time as 3:00 p.m. to run the schedule, you can cancel the pre-event from 2:20 pm onwards till 2:50 p.m.


## Next steps
For issues and workarounds, see [troubleshoot](troubleshoot.md)
