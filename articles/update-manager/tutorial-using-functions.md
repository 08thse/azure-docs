---
title: Create pre and post events using Azure Functions.
description: In this tutorial, you learn how to create the pre and post events using Azure Functions.
ms.service: azure-update-manager
ms.date: 11/12/2023
ms.topic: tutorial 
author: SnehaSudhirG
ms.author: sudhirsneha
#Customer intent: As an IT admin, I want  create pre and post events using Azure Functions.
---

# Tutorial: Create pre and post events using Azure Functions

**Applies to:** :heavy_check_mark: Windows VMs :heavy_check_mark: Linux VMs :heavy_check_mark: On-premises environment :heavy_check_mark: Azure VMs :heavy_check_mark: Azure Arc-enabled servers.
 

This tutorial explains how to create pre and post events to start and stop a VM in a schedule patch workflow using Azure Functions.

In this tutorial, you learn how to:

> [!div class="checklist"]
> - Create a function app
> - Create a function
> - Create an event subscription


## Create a function app

1. [Create a function app](../azure-functions/functions-create-function-app-portal.md#create-a-function-app). For example, if you're using PowerShell 7.2 as the Runtime stack, you can use a new storage account of link an existing storage account.
1. If you're creating the storage account for the first time, ensure that you load the dependencies by following these steps:
    1. On the **Function App**, select **App files**.
    1. Under the **host.json**, enable **ManagedDependecy** to **True**.
    1. Under the **requirements.psd1**, paste the following code: 
        ```
        { 
		For latest supported version, go to 'https://www.powershellgallery.com/packages/Az'.        
         Uncomment the next line and replace the MAJOR_VERSION,
         for example,'Az'='5.*' 
		 'Az'='5.*' 
		 'Az.ResourceGraph'='0.13.0' 
		 'Az.Resources'='6.*' 
            }
        ```
       
[Learn more](https://aka.ms/functions) on how to enable modules so that they're automatically managed by the Functions service. 

1. To query ARG through your function app, you must enable system-assigned identity, user-assigned identity and assign the right permissions on the subscriptions. [Learn more](../app-service/overview-managed-identity.md).
1. Restart the function app from the **Overview** tab to load the dependencies that are mentioned in the *requirments.psd1* file.


## Create a function

1. After you create the function app, go to **Resource**, and in **Overview**, select **Create in Azure portal**.
1. In the **Create function** window, make the following selections:
    1. For the **Development environment property**, select *Develop in portal* 
    1. In **Select a template**, select *Event Grid*.
    1. In **Template details**, enter the name in **New Function** and then select **Create**.
       :::image type="content" source="./media/tutorial-using-functions/create-function.png" alt-text="Screenshot that shows the options to select while creating a function." lightbox="./media/tutorial-using-functions/create-function.png"::: 

1. In the **Event grid function**, select **Code+Test** from the left menu, paste the following code and select **Save**.

   To start and stop VMs for all the machines that are part of a given maintenance configuration/schedule:
   
   ``` 
   param($preEvent $TriggerMetadata) 
    # Makesure to pass hashtables to Out-String so they're logged correctly
    $preEvent|Out-String|Write-Host 
    $correlationId=$preEvent.id 
    $maintenanceConfigurationId=$preEvent.topic 
    $resourceSubscriptionIds=$preEvent.data.ResourceSubscriptionIds 
    $queryStr ="maintenanceresources 
	|where type=='microsoft.maintenance/applyupdates' 
	|where properties.correlationId=~'$correlationId' 
	|project name, resourceId=properties.resourceId" 
    $argQueryResult=Search-Azgraph-Query$queryStr-Subscription$preEvent.data.ResourceSubscriptionIds 
    $mcName=($maintenanceConfigurationId-split'/')
    [8].ToLower() 
    $tagKey="preevent_$mcName" 
   ```
1. Select **Integration** from the left menu and edit the **Event Trigger parameter name** under **Trigger**. Use the same parameter name given in the **Code+Test** window. In the example, the parameter is pre-Event. 
1. Select **Save**

## Create an Event subscription 

1. Sign in to the [Azure portal](https://portal.azure.com) and go to your **Azure Update Manager**.
1. Under **Manage**, select **Machines**, **Maintenance Configuration**.
1. On the **Maintenance Configuration** page, select the configuration. 
1. Under **Settings**, select **Events**. 
1. Select **+Event Subscription** to create a pre/post maintenance event.
1. On the **Create Event Subscription** page, enter the following details:
    1. In the **Event Subscription Details** section, provide an appropriate name. 
    1. Keep the schema as **Event Grid Schema**.
    1. In the **Event Types** section, **Filter to Event Types**. 
        1. Select **Pre Maintenance Event** for a pre-event.
           - In the **Endpoint details** section, select the **Azure Function** endpoint and select **Configure and Endpoint**.
           - Provide the appropriate details such as Resource groups, function app to trigger the event.
        1. Select **Post Maintenance Event** for a post-event.
            - In the **Endpoint details** section, the **Azure Function** endpoint and select **Configure and Endpoint**.
            - Provide the appropriate details such as **Resource group**, **Function app** to trigger the event.
1. Select **Create**.

You can also use Azure Storage accounts and Event hub to store, send, and receive events. Learn more on [how to create Event hub](../event-hubs/event-hubs-create.md) and [Storage queues](../event-hubs/event-hubs-create.md).

## Next steps
Learn about [managing multiple machines](manage-multiple-machines.md).
 
