---
title: Prebuilt models - Form Recognizer | v3.0 Preview
titleSuffix: Azure Applied AI Services
description: Concepts encompassing data extraction using Form Reco prebuilt models (preview).
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 09/14/2021
ms.author: lajanuar
---

# Form Recognizer v3.0 prebuilt models | Preview

>[!NOTE]
> Form Recognizer studio is currently in public preview. Some features might not be supported or have limited capabilities.

Form Recognizer prebuilt models enable you to add intelligent form processing to your apps and flows without have to train and build your own models. Form Recognizer v3.0 (preview) introduces several new features and capabilities:

* [**Prebuilt document (v3.0)**](#prebuilt-document-model) model is a new API that extracts text, tables, structure, key-value pairs, and named entities from forms and documents.
* [**Prebuilt receipt (v3.0)**](#prebuilt-receipt-model) model supports single-page hotel receipt processing.
* [**Prebuilt ID document (v3.0)**](#prebuilt-id-document-model) model supports endorsements, restrictions, and vehicle classification extraction from US driver's licenses.
* [**Custom model API (v3.0)**](#custom-model) supports signature detection for custom forms.

| **Prebuilt model ID**   | **Description**   |
| --- | --- |
| 🆕prebuilt:document  | extract text, tables, structure, key-value pairs and named entities.  |
| prebuilt:layout  | Extracts text and layout information from documents.  |
| prebuilt:invoice  | Extract key information from English invoices.  |
| prebuilt:receipt  | Extract key information from English receipts.  |
| prebuilt:idDocument  | Extract key information from US driver licenses and international passports.  |
| prebuilt:businessCard  | Extract key information from English business cards.  |

### Prebuilt model data extraction

| **Model ID**   | **Text extraction** |**Key-Value pairs** |**Selection Marks**   | **Tables**   |**Entities** |
| --- | :---: |:---:| :---: | :---: |:---: |
|🆕prebuilt:document  | ✓  |  ✓ | ✓  | ✓  | ✓  |   |
| prebuilt:layout  | ✓  |   | ✓  | ✓  |   |   |
| prebuilt:invoice   | ✓ | ✓  | ✓  | ✓ |
|prebuilt:receipt  | ✓  |   ✓ |   |  |
| prebuilt:idDocument | ✓  |   ✓  |   |   |
| prebuilt:businessCard    | ✓  |   ✓ |   |   |

### Input requirements

* For best results, provide one clear photo or scan per document.
* Supported file formats: JPEG, PNG, PDF, BMP and TIFF.
* For PDF and TIFF, up to 2000 pages are processed. For free tier subscribers, only the first two pages are processed.
* The file size must be less than 50 MB.
* For images (JPEG, PNG, BMP, TIFF), the dimensions must be at least 50 x 50 pixels and at most 10,000 x 10,000 pixels.
* PDF dimensions cannot exceed 17 x 17 inches (Legal or A3 paper sizes).
* For more guidance, *see*  our [**Overview**](overview#input-requirements) documentation.

## Prebuilt document model

* The prebuilt document API will analyze your documents and associate values to the keys and entries to tables that it discovers. You can use it in place of [training a custom model without labels](overview#train-without-labels).

* The prebuilt document model also supports named entity recognition (NER) for several entity categories. NER is the ability to identify different entities in text and categorize them into pre-defined classes or types such as: person, location, event, product, and organization.

### Named entity recognition categories

| Category | Type | Description |
|-----------|-------|--------------------|
| Person | string | A person's partial or full name. |
|PersonType | string | A person's job type or role.  |
| Location | string | Natural and human-made landmarks, structures, geographical features, and geopolitical entities |
| Organization | string | Companies, political groups, musical bands, sport clubs, government bodies, and public organizations. |
| Event | string | Historical, social, and naturally occurring events. |
| Product | string |Physical objects of various categories. |
| Skill | string | A capability, skill, or expertise. |
| Address | string | Full mailing addresses. |
| Phone number | string| Phone numbers. | 
Email | string | Email address. |
| URL | string| Website URLs and links|
| IPAddress | string| Network IP addresses. |
| DateTime | string| Dates and times of day. |
| Quantity | string | Numerical measurements and units. |

## Prebuilt receipt model

 Azure Form Recognizer v3.0 prebuilt receipt model analyzes and extracts key information from sales receipts and supports processing single-page hotel receipts. 

### Hotel receipt key-value pair fields

|Name| Type | Description | Standardized output |
|:-----|:----|:----|:----| 
| ArrivalDate | date | Date of arrival | yyyy-mm-dd |
| Currency | currency | Currency unit of receipt amounts. For example USD, EUR, or MIXED if multiple values are found |
| DepartureDate | date | Date of departure | yyyy-mm-dd |
| Items | array | | | |
| Items.*.Category | string | Item category, e.g. Room, Tax, etc. |  |
| Items.*.Date | date | Item date | yyyy-mm-dd |
| Items.*.Description | string | Item description | |
| Items.*.TotalPrice |  number | Item total price | integer |
| Locale | string | Locale of the receipt, for example, en-US. | ISO language-county code   |
| MerchantAddress | string | Listed address of merchant | |
| MerchantAliases | array| | | |
| MerchantAliases.* | string | Alternative name of merchant |  |
| MerchantName | string | Name of the merchant issuing the receipt | |
| MerchantPhoneNumber | phoneNumber | Listed phone number of merchant | +1 xxx xxx xxxx|
| ReceiptType | string | Type of receipt, e.g. Hotel, Itemized | |
| Total | number | Full transaction total of receipt | Two-decimal float |

## Prebuilt ID document model 

Azure Form Recognizer v3.0 prebuilt ID document model analyzes and extracts key information from U.S. Driver's Licenses (all 50 states and District of Columbia.) and the biographical page from international passports (excluding visa and other travel documents).

### Key value pair fields

|Name| Type | Description | Standardized output <img width=500>|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | Country or region code compliant with ISO 3166 standard |  |
|  DateOfBirth | date | DOB | yyyy-mm-dd |
|  DateOfExpiration | date | Expiration date DOB | yyyy-mm-dd |
|  DocumentNumber | string | Relevant passport number, driver's license number, etc. |  |
|  FirstName | string | Extracted given name and middle initial if applicable |  |
|  LastName | string | Extracted surname |  |
|  Nationality | countryRegion | Country or region code compliant with ISO 3166 standard |  |
|  Sex | string | Possible extracted values include "M", "F" and "X" | |
|  MachineReadableZone | object | Extracted Passport MRZ including two lines of 44 characters each | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | string | Document type, for example, Passport, Driver's License | "passport" |
|  Address | string | Extracted address (Driver's License only) ||
|  Region | string | Extracted region, state, province, etc. (Driver's License only) |  |
| 🆕 Endorsements | string | Additional driving privileges granted to a driver such as Motorcycle or School bus.  | |
| 🆕 Restrictions | string | Restricted driving privileges applicable to suspended or revoked licenses.| |
| 🆕VehicleClassification | string | Types of vehicles that can be driven by a driver. ||

## Custom model

Azure Form Recognizer v3.0 custom model enable you to analyze and extract data from forms and documents specific to your business. When training custom models, you can specify certain fields as signatures.  When a document is analyzed with your custom model it will indicate whether a signature has been detected or not.

To try signature detection:

1. [**Build your training data set**](build-training-data-set.md#custom-model-input-requirements).

1. Navigate to the [**Form Recognizer sample labeling tool**](https://fott-preview-private.azurewebsites.net) and select **Use Custom to train a models with labels and get key value pairs**:

    :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="Screenshot: fott tool select custom option.":::

1. In the next window, select **New project**:

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="Screenshot: fott tool select new project."::: 

1. Follow the  [**Custom model input requirements**](build-training-data-set.md#custom-model-input-requirements).

1. Create a label with the type **Signature**.

1. **Label your documents**.  For signature fields, using region labeling is recommended for better accuracy.

1. Once your training set has been labeled, you can **train your custom model** and use it to analyze documents. The signature fields will specify whether a signature was detected or not.
