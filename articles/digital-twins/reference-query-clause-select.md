---
# Mandatory fields.
title: Azure Digital Twins query language reference - SELECT clauses
titleSuffix: Azure Digital Twins
description: Reference documentation for the Azure Digital Twins query language SELECT clause
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Azure Digital Twins query language reference: SELECT clause

This document contains reference information on the **SELECT clause** for the [Azure Digital Twins query language](concepts-query-language.md).

The SELECT clause is the first part of a query. It specifies the list of columns that the query will return.

This clause is required for all queries.

## SELECT *

Use the `*` character in a select statement to project the digital twin document as is, without assigning it to a property in the result set.

>[!NOTE]
> `SELECT *` is only valid syntax when the query does not use a `JOIN`. For more information on queries using `JOIN`, see [Azure Digital Twins query language reference: JOIN clause](reference-query-clause-join.md).

### Syntax

```sql
SELECT *
--FROM ...
```

### Returns

The set of properties which are returned from the query.

### Example

The following query returns all digital twins in the instance. 

```sql
SELECT *
FROM DIGITALTWINS
```

## SELECT columns with projections

You can use projections in the SELECT clause to choose which columns a query will return. You can specify named collections of twins and relationships, or properties of twins and relationships.

At this time, complex properties are not supported. To make sure that projection properties are valid, combine the projections with an `IS_PRIMITIVE` check.

### Syntax

To project a collection:
```sql
SELECT <twin-or-relationship-collection>
```

To project a property:
```sql
SELECT <twin-or-relationship-collection>.<property-name>
```

### Returns

A collection of twins, properties, or relationships.

### Examples

#### Example scenario

For the following examples, consider a twin graph that contains the following data elements:
* A Factory twin called `FactoryA`
    - Contains a property called `name` with a value of `FactoryA`
* A Consumer twin called `Contoso`
    - Contains a property called `name` with a value of `Contoso`
* A consumerRelationship relationship from `FactoryA` to `Contoso`, called `FactoryA-consumerRelationship-Contoso`
    - Contains a property called `managedBy` with a value of `Jeff`

Here's a diagram illustrating this scenario:

:::row:::
    :::column:::
        :::image type="content" source="media/reference-query-clause-select/projections-graph.png" alt-text="Diagram showing the sample graph described above.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### Project collection example

Below is an example query that projects a collection from this graph. The following query returns all digital twins in the instance, by naming the entire twin collection `T` and projecting `T` as the collection to return. 

```sql
SELECT T
FROM DIGITALTWINS T
```

Here is the JSON payload that's returned from this query:

```json
{
  "value": [
    {
      "result": [
        {
          "T": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          }
        },
        {
          "T": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### Project with JOIN example

Projection is commonly used to return a collection specified in a `JOIN`. The following query uses projection to return the data of the Consumer, Factory and Relationship. For more about the `JOIN` syntax used in the example, see [Azure Digital Twins query language reference: JOIN clause](reference-query-clause-join.md).

```sql
SELECT Consumer, Factory, Relationship
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.consumerRelationship Relationship
WHERE Factory.$dtId = 'FactoryA'
```

Here is the JSON payload that's returned from this query:

```json
{
  "value": [
    {
      "result": [
        {
          "Consumer": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          },
          "Factory": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          },
          "Relationship": {
            "$etag": "W/\"f01e07c1-19e4-4bbe-a12d-f5761e86d3e8\"",
            "$relationshipId": "FactoryA-consumerRelationship-Contoso",
            "$relationshipName": "consumerRelationship",
            "$sourceId": "FactoryA",
            "$targetId": "Contoso",
            "managedBy": "Jeff"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### Project property example

Here is an example that projects a property. The following query uses projection to return the `name` property of the Consumer twin, and the `managedBy` property of the relationship. Note that the query uses `IS_PRIMITIVE` to verify that the property names are of primitive types, since complex properties are not currently supported by projection.

```sql
SELECT Consumer.name, Relationship.managedBy
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.consumerRelationship Relationship
WHERE Factory.$dtId = 'FactoryA'
AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Relationship.managedBy)
```

Here is the JSON payload that's returned from this query:

```json
{
  "value": [
    {
      "result": [
        {
          "managedBy": "Jeff",
          "name": "Contoso"
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

## SELECT COUNT

Use this method to count the number of items in the result set and return that number.

### Syntax

```sql
SELECT COUNT()
```

### Arguments

None.

### Returns

An `int` value.

### Example

The following query returns the count of all digital twins in the instance.

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

## SELECT TOP

Use this method to return only a certain number of top items that meet the query requirements.

### Syntax

```sql
SELECT TOP(<number-of-return-items>)
```

### Arguments

An `int` value specifying the number of top items to select.

### Returns

A collection of twins.

### Example

The following query returns only the first five digital twins in the instance.

```sql
SELECT TOP(5)
FROM DIGITALTWINS
```