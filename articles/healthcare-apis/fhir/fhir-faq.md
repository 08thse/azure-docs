---
title: FAQs about FHIR services in Azure Healthcare APIs
description: Get answers to frequently asked questions about the FHIR service, such as the storage location of data behind FHIR APIs and version support.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 04/30/2021
ms.author: cavoeg
---

# Frequently asked questions about the FHIR service

This section covers some of the frequently asked questions about Healthcare APIs FHIR service.

## FHIR service: The Basics

### What is FHIR?

The Fast Healthcare Interoperability Resources (FHIR - Pronounced "fire") is an interoperability standard intended to enable the exchange of healthcare data between different health systems. This standard was developed by the HL7 organization and is being adopted by healthcare organizations around the world. The most current version of FHIR available is R4 (Release 4). The FHIR service supports R4 and also supports the previous version STU3 (Standard for Trial Use 3). For more information on FHIR, visit [HL7.org](http://hl7.org/fhir/summary.html).

### Is the data behind the FHIR APIs stored in Azure?

Yes, the data is stored in managed databases in Azure. The FHIR service in the Azure Healthcare APIs does not provide direct access to the underlying data store.

### What identity provider do you support?

We currently support Microsoft Azure Active Directory as the identity provider.

### How quickly will the Healthcare APIs be restored at the request of customers or when there is a regional or global service outage?
 
Customers may request that the Healthcare APIs be restored through the backup and restore procedure based on the service request priority. For customers that have the disaster recovery feature enabled, a fully operational environment is configured and activated in a secondary region and will be used as the primary region in the event of disaster. **Currently Healthcare APIs is in public preview; more details on our disaster recovery plan will be available by GA.**

### What FHIR version do you support?

We support versions 4.0.0 and 3.0.1.

For details, see [Supported features](fhir-features-supported.md). Read about what has changed between FHIR versions (i.e. STU3 to R4) in the [version history for HL7 FHIR](https://hl7.org/fhir/R4/history.html).

### What is the difference between the Azure API for FHIR and the FHIR service in the Healthcare APIs?

The FHIR service is our implementation of the FHIR specification that sits in the Azure Healthcare APIs, which allows you to have a FHIR service and a DICOM service within a single workspace. The Azure API for FHIR was our initial GA product and is still available as a stand-alone product. The main feature differences are:

* The FHIR service has a limit of 4TB and is in public preview while the Azure API for FHIR supports more than 4TB and is GA.
* The FHIR service support [transaction bundles](https://www.hl7.org/fhir/http.html#transaction).
* Chained searching and reverse chained searching does not have a limit on number of resources returned
* The Azure API for FHIR has more platform features (such as Private Link, CMK) that are not yet available in the FHIR service in the Azure Healthcare APIs. More details will follow on these features by GA.

### What's the difference between 'FHIR service in the Azure Healthcare APIs' and the 'FHIR server'?

The FHIR service is a hosted and managed version of the open-source Microsoft FHIR Server for Azure. In the managed service, Microsoft provides all maintenance and updates.

When you run the FHIR Server for Azure, you have direct access to the underlying services, but are responsible for maintaining and updating the server and all required compliance work if you're storing PHI data.

For a development standpoint, every feature that doesn't apply only to the managed service is first deployed to the open-source Microsoft FHIR Server for Azure. Once it has been validated in open-source, it will be released to the PaaS FHIR service. The time between the release in open-source and PaaS depends on the complexity of the feature and other roadmap priorities.

### In which regions is the FHIR service available?

We are expanding the global footprints of the Healthcare APIs continually based on customer demands. The FHIR service is currently available in 25 regions including two U.S. government regions: Australia East, Brazil South*, Canada Central, Central India*, East Asia*, East US 2, East US, Germany North**, Germany West Central, Germany North**, Japan East, Japan West*, Korea Central*, North Central US, North Europe, South Africa North, South Central US, Southeast Asia, Switzerland North, UK South, UK West, West Central US, West Europe, West US 2, USGov Virginia, USGov Arizona.

*denotes private preview regions; ** denotes disaster recovery region only.

### Where can I see what is releasing into the FHIR service?

To see some of what is releasing into the FHIR service, please refer to the [release](https://github.com/microsoft/fhir-server/releases) of the open-source FHIR Server. We have worked to tag items with FHIR-Service if they will release to the managed service and are usually available two weeks after they are on the release page in open-source. We have also included instructions on how to test the build [here](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) if you would like to test in your own environment. We are evaluating how to best share additional managed service updates.

### What is SMART on FHIR?

SMART (Substitutable Medical Applications and Reusable Technology) on FHIR is a set of open specifications to integrate partner applications with FHIR Servers and other Health IT systems, such as Electronic Health Records and Health Information Exchanges. By creating a SMART on FHIR application, you can ensure that your application can be accessed and leveraged by a plethora of different systems.
To learn more about SMART, visit [SMART Health IT](https://smarthealthit.org/).

### Where can I find what version of FHIR is running on my database.

You can find the exact FHIR version exposed in the capability statement under the "fhirVersion" property (FHIR URL/metadata)

## FHIR Implementations and Specifications

### Can I create a custom FHIR resource?

We do not allow custom FHIR resources. If you need a custom FHIR resource, you can build a custom resource on top of the [Basic resource](http://www.hl7.org/fhir/basic.html) with extensions. 

### Are [extensions](https://www.hl7.org/fhir/extensibility.html) supported on the FHIR service?

We allow you to load any valid FHIR JSON data into the server. If you want to store the structure definition that defines the extension, you could save this as a structure definition resource. To search on extensions, you'll need to [define your own search parameters](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fhealthcare-apis%2Ffhir%2Fhow-to-do-custom-search&data=04%7C01%7Cv-stevewohl%40microsoft.com%7Cc6a08c7f0c86433f248c08d925377d85%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637581742517376233%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=Ws%2FVQ2N33sMagzs393hmR67M9dNaL6WCLXyxXtor6PM%3D&reserved=0). 

### What is the limit on _count?

The current limit on _count is 1000. If you set _count to more than 1000, you'll receive a warning in the bundle that only 1000 records will be shown.

### Are there any limitations on the Group Export functionality?

For Group Export we only export the included references from the group, not all the characteristics of the [group resource](https://www.hl7.org/fhir/group.html).

### Can I post a bundle to the FHIR service?

We currently support posting [batch bundles](https://www.hl7.org/fhir/valueset-bundle-type.html) and posting [transaction bundles](https://www.hl7.org/fhir/http.html#transaction) in the FHIR service.

### How can I get all resources for a single patient in the FHIR service?

We support the $patient-everything operation which will get you all data related to a single patient. 

### What is the default sort when searching for resources in the FHIR service?

We support sorting by strings and dates for single fields at a time. For more information about other supported search parameters, see [Overview of FHIR Search](overview-of-search.md).

### How does $export work?

$export is part of the [FHIR specification](https://hl7.org/fhir/uv/bulkdata/export/index.html). If the FHIR service is configured with a managed identity and a storage account, and if the managed identity has access to that storage account - you can simply call $export on the FHIR API and all the FHIR resources will be exported to the storage account. For more information, check out our [article on $export](../data-transformation/export-data.md).

### Is de-identified export available at Patient and Group level as well?

Anonymized export is currently supported only on a full system export (/$export), and not for Patient export (/Patient/$export). We are working on making it available at the Patient level as well.

## Using the FHIR service

### How do I enable log analytics for Azure Healthcare APIs?

We enable audit and diagnostic logs and allow reviewing the logs from the Azure portal. For details on enabling audit logs and sample queries, check out [this section](enable-diagnostic-logging.md). If you want to include additional information in the logs, check out [using custom HTTP headers](use-custom-headers.md).

Customers can also choose to export the logs to their designated locations such as an Azure Storage account. **Currently Healthcare APIs is in public preview; we will provide more details on logging by GA.**

### Where can I see some examples of using the FHIR service within a workflow?

We have a collection of reference architectures available on the [Health Architecture GitHub page](https://github.com/microsoft/health-architectures).

### Where can I see an example of connecting a web application to FHIR service?

We have a [Health Architecture GitHub page](https://aka.ms/health-architectures) that contains example applications and scenarios. It illustrates how to connect a web application to FHIR service.  
 
### Does the Healthcare APIs support encryption with customer managed keys (CMK)?
 
By default all data stored in the Healthcare APIs is encrypted by Microsoft managed keys. However, customers can choose to use their own keys for encryption when the Healthcare APIs are provisioned. **Currently Healthcare APIs is in public preview; we will provide more details on encryption with customer managed keys by GA.**

For reference, Azure API for FHIR allows configuring customer-managed keys, leveraging support from Cosmos DB. For more information about encrypting your data with a personal key for Azure API for FHIR, check out [this section](../azure-api-for-fhir/customer-managed-key.md).
