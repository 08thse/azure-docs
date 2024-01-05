---
title: Relocation guidance overview for Microsoft Azure products and services
description: Relocation guidance overview for Microsoft Azure products and services. View Azure service specific relocation guides.
author: anaharris-ms
ms.service: reliability
ms.topic: conceptual
ms.date: 12/01/2023
ms.author: anaharris
ms.custom: subject-reliability
---

# Relocation guidance overview 

As Microsoft continues to expand Azure global infrastructure and launch new Azure regions worldwide, there's an increasing number options available for you to relocate your workloads into new regions.  Region relocation options vary by service and by workload architecture.  To successfully relocate a workload to another region, you need to plan your relocation strategy with an understanding of what each service in your workload requires and supports. 

Azure region relocation documentation contains [service-specific relocation guidance for Azure products and services](./relocation-guidance-overview.md). Each relocation guide contains the relocation options for each service that's in your workload.  For a deeper understanding of the relocation process and principles used in these service specific guides, see [Azure Cloud Adoption Framework - Relocate cloud workloads](/azure/cloud-adoption-framework/relocate/).

Each service specific guide includes service-specific information such as:

- [Relocation methods, such as Cold, Hot or Warm relocation](/azure/cloud-adoption-framework/relocate/select)
- [Service-relocation automation tools](/azure/cloud-adoption-framework/relocate/select#select-service-relocation-automation).
- [Data relocation automation](/azure/cloud-adoption-framework/relocate/select#select-data-relocation-automation).
- [Cutover approaches](/azure/cloud-adoption-framework/relocate/select#select-cutover-approach).
- Possible and actual service dependencies that also require relocation planning.
- Lists of considerations, features, and limitations in relation to relocation planning for that service.
- Links to how-tos and relevant product-specific relocation information.


## Service categories across region types

[!INCLUDE [Service categories across region types](../../includes/service-categories/service-category-definitions.md)]

## Azure services relocation guides

The following tables provide links to each Azure service relocation document.

### ![An icon that signifies this service is foundational.](./media/relocation/icon-foundational.svg) Foundational services 

| **Product**  |
| --- | 
[Azure Database for PostgreSQL](./relocation-postgresql-flexible-server.md)|
[Azure Key Vault]()|
[Azure Virtual Network](./relocation-virtual-network.md)|

### ![An icon that signifies this service is mainstream.](./media/relocation/icon-mainstream.svg) Mainstream services

| **Product**  |
| --- | 
[Azure Monitor - Log Analytics]()|



## Additional information

- [Azure Resources Mover documentation](/azure/resource-mover/)
- [Azure Resource Manager (ARM) documentation](/azure/azure-resource-manager/templates/)


