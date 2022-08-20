---
title: CSV parser ingestion workflow concept #Required; page title is displayed in search results. Include the brand.
description: Learn how to use CSV parser ingestion. #Required; article description that is displayed in search results. 
author: bharathim #Required; your GitHub user alias, with correct capitalization.
ms.author: bselvaraj #Required; microsoft alias of author; optional team alias.
ms.service: azure #Required; service per approved list. slug assigned by ACOM.
ms.topic: conceptual #Required; leave this attribute/value as-is.
ms.date: 08/18/2022
ms.custom: template-concept #Required; leave this attribute/value as-is.
---

# CSV parser ingestion concepts

## Understanding CSV files

One of the simplest generic data formats that are supported by the Microsoft Energy Data Services ingestion process is the "comma separated values" format, which is called a CSV format. The CSV format is processed through a CSV Parser DAG definition. 

CSV Parser DAG implements an ELT approach to data loading, that is, data is loaded after it's extracted. Customers can use CSV Parser DAG to load data that doesn't match the [OSDU](https://osduforum.org) canonical schema. Customers need to create and register a custom schema using the schema service matching the format of the CSV file.

## What does CSV Ingestion do?

* **Schema validation** – ensure CSV file conforms to the schema.
* **Type conversion** – ensure that the type of a field is as defined and converts it to the defined type if otherwise.
* **ID generation** – used to upload into storage service. It helps in scenarios where the ingestion failed half-way as ID generation logic is idempotent, one avoids duplicate data on platform.
* **Reference handling** – enable customers to refer to actual data on the platform and access it.
* **Persistence** – It persists each row after validations by calling storage service API. Once persisted, the data is available for consumption through search and storage service APIs.

## CSV Parser ingestion functionality

The CSV Parser ingestion currently supports the following functionality as a one-step DAG:

1. CSV file is parsed as per the schema (one row in CSV = 1 record ingested into the data platform)
1. CSV file contents match the contents of the provided schema.
   1. **Success**: validate the schema vs. the header of the CSV file and the values of the first nrows. Use the schema for all downstream tasks to build the metadata.
   1. Fail: log the error(s) in the schema validation, proceed with ingestion if errors are non-breaking
1. Convert all characters to UTF8, and gracefully handle/replace characters that can't be converted to UTF8.
1. Unique data identity for an object in the Data Platform. - CSV Ingestion generates Unique Identifier (ID) for each record by combining source, entity type and a base64 encoded string formed by concatenating natural key(s) in the data. In case the schema used for CSV Ingestion doesn't contain any natural keys storage service will generate random IDs for every record
1. Typecast to JSON-supported data types:
   1. **Number** - Typecast integers, doubles, floats, etc. as described in the schema to "number". Some common spatial formats, such as Degrees/Minutes/Seconds (DMS) or Easting/Northing should be typecast to "String." Special Handling of these string formats will be handled in the Spatial Data Handling Task.
   1. **Date** - Typecast dates as described in the schema to a date, doing a date format conversion toISO8601TZ format (for fully qualified dates). Some date fragments (such as years) can't be easily converted to this format and should be typecast to a number instead, or if textual date representations, for example, "July" should be typecast to string.
   1. **Others** - All other encountered attributes should be typecast as string.
1. Stores a batch of records in the context of a particular ingestion job. Fragments/outputs from the previous steps are collected into a batch, and formatted in a way that is compatible with the Storage Service with the appropriate additional information, such as the ACL's, Legal tags, etc.
1. Support frame of reference handling:
   1. **Unit** -converting declared frame of reference information into the appropriate persistable reference as per the Unit Service. This information is stored in the meta[] block.
   1. **CRS** -the CRS Frame of Reference information should be included in the schema of the data, including the source CRS (either geographic or projected), and if projected, This CRS info and persistable reference (if provided in schema) information is stored in the meta[] block.
1. Creates relationships as declared in the source schema.
1. Supports publishing status of ingested/failed records on GSM article

## CSV Parser ingestion components

* **File service** – Facilitates management of files on data platform. Uploading, Secure discovery and downloading of files are capabilities provided by file service.
* **Schema service** – Facilitates management of Schemas on data platform. Creating, fetching and searching for schemas are capabilities provided by schema service.
* **Storage Service** – JSON object store that facilitates storage of metadata information for domain entities. Also raises storage events when records are saved using storage service.
* **Unit Service** – Facilitates management and conversion of Units
* **Workflow service** – Facilitates management of workflows on data platform. Wrapper over the workflow engine and abstract many technical nuances of the workflow engine from consumers.
* **Airflow engine** – Heart of the ingestion framework. Actual Workflow orchestrator.
* **DAGs** – Based on Direct Acyclic Graph concept, are workflows that are authored, orchestrated, managed and monitored by the workflow engine.

## CSV ingestion components diagram

:::image type="content" source="media/concepts-csv-parser-ingestion/csv-ingestion-components-diagram.png" alt-text="CSV ingestion components diagram":::

## CSV ingestion sequence diagram

:::image type="content" source="media/concepts-csv-parser-ingestion/csv-ingestion-sequence-diagram.png" alt-text="CSV ingestion sequence diagram":::

## CSV parser ingestion workflow

### Pre-requisites

* To trigger APIs, the user must have the below access and a valid authorization token
  * Access to services: Search, Storage, Schema, File Service, Entitlement, Legal
  * Access to Workflow service.
  * Following is list of service level groups that you need access to register and execute DAG using workflow service.
    * "service.workflow.creator"
    * "service.workflow.viewer"
    * "service.workflow.admin"

### Steps to execute a DAG using Workflow Service

* **Create schema** – Definition of the kind of records that will be created as outcome of ingestion workflow. The schema is uploaded through the schema service. The schema needs to be registered using schema service.
* **Uploading the file** – Use file Service to upload a file. The file service provides a signed URL, which enables the customers to upload the data without credential requirements.
* **Create Metadata record for the file** – Use file service to create meta data. The meta data enables discovery of file and secure downloads. It also provides a mechanism to provide information associated with the file that is needed during the processing of the file.  
* The file ID created is provided to the CSV parser, which takes care of downloading the file, ingesting the file, and ingesting the records with the help of workflow service. The customers also need to register the workflow, the CSV parser DAG is already deployed in the Airflow.
* **Trigger the Workflow service** – To trigger the workflow, the customer needs to provide the file ID, the kind of the file and data partition ID. Once the workflow is triggered, the customer gets a run ID.
Workflow service provides API to monitor the status of each workflow run. Once the csv parser run is completed, data is ingested into OSDU platform, and can be searched through search service

## Next steps
* [DDMS Concepts](concepts-ddms.md)