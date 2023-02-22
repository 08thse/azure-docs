---
title: Reliability in Azure App Service
description: Find out about reliability in Azure App Service  
author: anaharris-ms 
ms.author: anaharris
ms.topic: overview
ms.custom: subject-reliability
ms.service: app-service
ms.date: 02/22/2022 
---


# Reliability in Azure App Service


This article describes reliability support in [Azure App Service](/azure/app-service/overview), and covers intra-regional resiliency with availability zones. Azure App Service is an HTTP-based service for hosting web applications, REST APIs, and mobile back ends. 

Azure App Service adds the power of Microsoft Azure to your application, such as:

- Security
- Load balancing
- Autoscaling
- Automated management

To explore how Azure App Service can bolster the resiliency of your application workload, see [Why use App Service?](/azure/app-service/overview#why-use-app-service)

## Availability zone support

Azure availability zones are at least three physically separate groups of datacenters within each Azure region. Datacenters within each zone are equipped with independent power, cooling, and networking infrastructure. In the case of a local zone failure, availability zones are designed so that if the one zone is affected, regional services, capacity, and high availability are supported by the remaining two zones.  Failures can range from software and hardware failures to events such as earthquakes, floods, and fires. Tolerance to failures is achieved with redundancy and logical isolation of Azure services. For more detailed information on availability zones in Azure, see [Regions and availability zones](/azure/availability-zones/az-overview).

Azure App Service Environment can be deployed across [availability zones (AZ)](../reliability/availability-zones-overview.md) to help you achieve resiliency and reliability for your business-critical workloads. This architecture is also known as zone redundancy.

When you configure to be zone redundant, the platform automatically spreads the instances of the Azure App Service plan across three zones in the selected region. This means that the minimum App Service Plan instance count will always be three. If you specify a capacity larger than three, and the number of instances is divisible by three, the instances are spread evenly. Otherwise, instance counts beyond 3*N are spread across the remaining one or two zones.

### Prerequisites

Availability zone support is a property of the App Service plan. The following are the current requirements/limitations for enabling availability zones:

- Both Windows and Linux are supported.

- Your App Services plan must be one of the following plans that support availability zones:

     - Premium v2 
     - Premium v3 
     - Isolated
     - Isolated v2

- Minimum instance count of three zones is enforced. The platform will enforce this minimum count behind the scenes if you specify an instance count fewer than three.

- Availability zones can only be specified when creating a **new** App Service plan. A pre-existing App Service plan can't be converted to use availability zones.

- Your App Service Environment must be either v2 or v3. ASE v1 doesn't support availability zones.
    
- Your App Service Environment must be running in a region that supports availability zones. To see which regions support availability zones, see [Regions](/azure/app-service/environment/overview#regions).

## Important considerations when using availability zones

Traffic is routed to all of your available App Service instances. In the case when a zone goes down, the App Service platform will detect lost instances and automatically attempt to find new replacement instances and spread traffic as needed. If you have [autoscale](../app-service/manage-scale-up.md) configured, and if it decides more instances are needed, autoscale will also issue a request to App Service to add more instances. Note that [autoscale behavior is independent of App Service platform behavior](../azure-monitor/autoscale/autoscale-overview.md) and that your autoscale instance count specification doesn't need to be a multiple of three. It's also important to note there's no guarantee that requests for additional instances in a zone-down scenario will succeed since back filling lost instances occurs on a best-effort basis. The recommended solution is to create and configure your App Service plans to account for losing a zone as described in the next section.

Applications that are deployed in an App Service plan that has availability zones enabled will continue to run and serve traffic even if other zones in the same region suffer an outage. However it's possible that non-runtime behaviors including App Service plan scaling, application creation, application configuration, and application publishing may still be impacted from an outage in other Availability Zones. Zone redundancy for App Service plans only ensures continued uptime for deployed applications.

When the App Service platform allocates instances to a zone redundant App Service plan, it uses [best effort zone balancing offered by the underlying Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing). An App Service plan will be "balanced" if each zone has either the same number of VMs, or +/- one VM in all of the other zones used by the App Service plan.

### Create a resource with availability zone enabled

- For *Premium v2*, *Premium v3*, and *Isolated v2* plans, hosted on App Service Environment v3, see [Create an App Service Environment](/azure/app-service/environment/creation).

- For *Isolated* plan hosted on App Service Environment v2, see [Availability zone support for App Service Environment v2](/azure/app-service/environment/zone-redundancy).


>[!IMPORTANT]
>[App Service Environment v2 will be retired on 31 August 2024](https://azure.microsoft.com/updates/app-service-environment-v1-and-v2-retirement-announcement/). App Service Environment v3 is easier to use and runs on more powerful infrastructure. To learn more about App Service Environment v3, see [App Service Environment overview](azure/app-service/environment/overview). If you're currently using App Service Environment v2 and you want to upgrade to v3, please follow the [steps in this article](/azure/app-service/environment/migration-alternatives) to migrate to the new version.

#### Deploy a zone-redundant App Service

**To deploy a zone-redundant App Service:**

1. Create a new App Service plan in one of the supported [regions](/azure/app-service/environment/overview#regions) using the **new** resource group.

1. Ensure that the `zoneRedundant` property (described below) is set to `true` when creating the new App Service plan.

1. Create your apps in the new App Service plan using your desired deployment method. 
# [Azure CLI](#tab/cli)

To enable availability zones using the Azure CLI, include the `--zone-redundant` parameter when you create your App Service plan. You can also include the `--number-of-workers` parameter to specify capacity. If you don't specify a capacity, the platform defaults to three. Capacity should be set based on the workload requirement, but no less than three. A good rule of thumb to choose capacity is to ensure sufficient instances for the application such that losing one zone of instances leaves sufficient capacity to handle expected load.

```azurecli
az appservice plan create --resource-group MyResourceGroup --name MyPlan --sku P1v2 --zone-redundant --number-of-workers 6
```
    

> [!TIP]
> To decide instance capacity, you can use the following calculation:
>
> Since the platform spreads VMs across three zones and you need to account for at least the failure of one zone, multiply peak workload instance count by a factor of zones/(zones-1), or 3/2. For example, if your typical peak workload requires four instances, you should provision six instances: (2/3 * 6 instances) = 4 instances.
>

# [Azure Portal](#tab/portal)


To create an App Service with availability zones using the Azure portal, enable the zone redundancy option during the "Create Web App" or "Create App Service Plan" experiences.

:::image type="content" source="../app-service/media/how-to-zone-redundancy/zone-redundancy-portal.png" alt-text="Screenshot of zone redundancy enablement using the portal.":::

The capacity/number of workers/instance count can be changed once the App Service Plan is created by navigating to the **Scale out (App Service plan)** settings.

:::image type="content" source="../app-service/media/how-to-zone-redundancy/capacity-portal.png" alt-text="Screenshot of a capacity update using the portal.":::


# [Azure Resource Manager (ARM)](#tab/arm)


The only changes needed in an Azure Resource Manager template to specify an App Service with availability zones are the ***zoneRedundant*** property (required) and optionally the App Service plan instance count (***capacity***) on the [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms?tabs=json) resource. The ***zoneRedundant*** property should be set to ***true*** and ***capacity*** should be set based on the same conditions described previously.

The Azure Resource Manager template snippet below shows the new ***zoneRedundant*** property and ***capacity*** specification.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "your-appserviceplan-name-here",
    "location": "West US 3",
    "sku": {
        "name": "P1v3",
        "tier": "PremiumV3",
        "size": "P1v3",
        "family": "Pv3",
        "capacity": 3
    },
    "kind": "app",
    "properties": {
        "zoneRedundant": true
    }
  }
]
```
---


#### Deploy a zonal App Service

To learn how to deploy a zonal App Service, see [Availability zone support for App Service Environment v2](/azure/app-service/environment/zone-redundancy).

### Fault tolerance

To prepare for availability zone failure, you should over-provision capacity of service to ensure that the solution can tolerate 1/3 loss of capacity and continue to function without degraded performance during zone-wide outages. Since the platform spreads VMs across three zones and you need to account for at least the failure of one zone, multiply peak workload instance count by a factor of zones/(zones-1), or 3/2. For example, if your typical peak workload requires four instances, you should provision six instances: (2/3 * 6 instances) = 4 instances.

### Zone down experience

- For zone-redundant App Service deployments, see [Downtime requirements](/azure/reliability/migrate-app-service#downtime-requirements).

- Zonal ILB (Internal Load Balancing) ASE deployments will continue to run and serve traffic on that ASE even when other zones in the same region suffer an outage. 

In order to attain end-to-end zone resiliency for apps created on a zonal ILB ASE, you must do the following:

1. Deploy at least two zonal ILB ASEs, where each ILB ASE is pinned to a different zone. 
1. Create and publish copies of your application onto each of the zonal ILB ASEs.  
1. Deploy a load balancing solution upstream of the zonal ILB ASEs so that traffic bound for an application is load-balanced and distributed across all of the zonal ILB ASEs. The recommended solution is to deploy a zone redundant Application Gateway upstream of the zonal ILB ASEs. To learn more details about Application Gateway v2 and its zone redundant configuration, see [Scaling Application Gateway v2 and WAF v2](/azure/application-gateway/application-gateway-autoscaling-zone-redundant).

### Availability zone migration

You cannot migrate or resources from non-availability zone support to availability zone support. To gain support for availability zones, you'll need to [redeploy your apps into a newly created availability zone enabled App Service plan](#deploy-a-zone-redundant-app-service).
## Pricing

There's no additional cost associated with enabling availability zones. Pricing for a zone redundant App Service is the same as a single zone App Service. You'll be charged based on your App Service plan SKU, the capacity you specify, and any instances you scale to based on your autoscale criteria. If you enable availability zones but specify a capacity less than three, the platform will enforce a minimum instance count of three and charge you for those three instances.


## Next steps

> [!div class="nextstepaction"]
> [Reliability in Azure](/azure/availability-zones/overview)
