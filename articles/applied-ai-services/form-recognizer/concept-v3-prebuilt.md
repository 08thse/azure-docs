---
title: Prebuilt models - Form Recognizer | Preview
titleSuffix: Azure Applied AI Services
description: Concepts encompassing data extraction using prebuilt models (preview)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 09/14/2021
ms.author: lajanuar
---

# Form Recognizer v3.0 | Preview

>[!NOTE]
> Form Recognizer studio is currently in public preview. Some features might not be supported or have limited capabilities. 

| **Model ID**   | **Description**   |
| --- | --- |
| prebuilt:layout  | Extracts text and layout information from documents.  |
| prebuilt:document  | extract text, tables, structure, key-value pairs and named entities.  |
| prebuilt:invoice  | Extract key information from English invoices.  |
| prebuilt:receipt  | Extract key information from English receipts.  |
| prebuilt:idDocument  | Extract key information from US driver licenses and international passports.  |
| prebuilt:businessCard  | Extract key information from English business cards.  |

**Analysis Features**

| **Model ID**   | **Text Extraction**   | **Selection Marks**   | **Tables**   | **Key-Value Pairs**   | **Entities**   | **Document Analysis**   |
| --- | --- | --- | --- | --- | --- | --- |
| prebuilt:layout  | ✓  | ✓  | ✓  |   |   |   |
| prebuilt:document  | ✓  | ✓  | ✓  | ✓  | ✓  |   |
| prebuilt:invoice  | ✓  | ✓  | ✓  |   |   | ✓  |
| prebuilt:receipt  | ✓  |   |   |   |   | ✓  |
| prebuilt:idDocument  | ✓  |   |   |   |   | ✓  |
| prebuilt:businessCard  | ✓  |   |   |   |   | ✓  |


v3.0 consolidates the analysis operations for layout analysis, prebuilt models, and custom models into a single pair of operations by assigning modelIds to layout analysis and prebuilt models. 

* [**Prebuilt Layout**](#prebuilt-layout)
* [**Prebuilt Document**](#prebuilt-document)
* [**Prebuilt invoice**](#prebuilt-invoice)
* [**Prebuilt receipt**](#prebuilt-receipt)
* [**Prebuilt ID Document**](#prebuilt-idDocument)
* [**Prebuilt Business Card**](#prebuilt-businessCard)

POST /documentModels/{modelId}:analyze 

GET /documentModels/{modelId}/analyzeResults/{resultId} 

## prebuilt-layout

## prebuilt-document

The term “Entity” in Entity Extraction AI Model means predefined categories like Age, Color, Date and time etc.

### Analyze Document

###

## prebuilt-businessCard

Azure Form Recognizer can analyze and extract contact information from business cards using its prebuilt business cards model. It combines powerful Optical Character Recognition (OCR) capabilities with our business card understanding model to extract key information from business cards in English and is publicly available in the Form Recognizer v2.1.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="sample business card" lightbox="./media/overview-business-card.jpg":::

## prebuilt-idDocument

Azure Form Recognizer can analyze and extract information from government-issued identification documents (IDs) using its prebuilt IDs model. It combines our powerful Optical Character Recognition (OCR) capabilities with ID recognition capabilities to extract key information from Worldwide Passports and U.S. Driver's Licenses (all 50 states and D.C.). The IDs API extracts key information from these identity documents, such as first name, last name, date of birth, document number, and more. This API is available in the Form Recognizer v2.1 as a cloud service.

The Identity documents  (ID) model enables you to extract key information from world-wide passports and US driver licenses. It extracts data such as the document ID, expiration date of birth, date of expiration, name, country, region, machine-readable zone and more.

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="sample identification card" lightbox="./media/overview-id.jpg":::

## prebuilt-invoice

Azure Form Recognizer can analyze and extract information from sales invoices using its prebuilt invoice models. The Invoice API enables customers to take invoices in various formats and return structured data to automate the invoice processing. It combines our powerful Optical Character Recognition (OCR) capabilities with invoice understanding deep learning models to extract key information from invoices written in English. The prebuilt Invoice API is publicly available in the Form Recognizer v2.1.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="sample invoice" lightbox="./media/overview-invoices.jpg":::

## prebuilt-receipt

Azure Form Recognizer can analyze and extract information from sales receipts using its prebuilt receipt model. It combines our powerful Optical Character Recognition (OCR) capabilities with deep learning models to extract key information such as merchant name, merchant phone number, transaction date, transaction total, and more from receipts written in English.

The Prebuilt Receipt model is used for reading English sales receipts from Australia, Canada, Great Britain, India, and the United States&mdash;the type used by restaurants, gas stations, retail, and so on. This model extracts key information such as the time and date of the transaction, merchant information, amounts of taxes, line items, totals and more. In addition, the prebuilt receipt model is trained to analyze and return all of the text on a receipt.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="sample receipt" lightbox="./media/overview-receipt.jpg":::