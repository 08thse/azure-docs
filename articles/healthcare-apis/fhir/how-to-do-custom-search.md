---
title:  How-to do custom search
description: This article describes how you can define your own custom search parameters to be used in the database. 
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/23/2021
ms.author: cavoeg
---
# Defining custom search parameters

The FHIR specification defines a set of search parameters for all resources and search parameters that are specific to a resource. However, there are scenarios where you might want to search against a field in a resource that isn’t defined by the specification as a standard search parameter. This article describes how you can define your own [search parameters](https://www.hl7.org/fhir/searchparameter.html) to be used in the database.

> [!NOTE]
> Each time you create, update, or delete a search parameter you’ll need to run a `reindex job` to enable the change in your database.

> [!Warning]
> If you update or delete a search parameter ensure that you immediately run a `reindex job`. There is the potential for your database to be in a abnormal state with updated or delete search parameters that still appear active due to needing to be indexed for the changes. 

## Create new search parameter

To create a new search parameter, you `POST` a new search parameter to the database. The code example below shows how to add the [US Core Race search parameter](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) to the Patient resource.

```json
POST {fhirurl}/SearchParameter
{
  "resourceType" : "SearchParameter",
  "id" : "us-core-race",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "Returns patients with a race extension matching the specified code.",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

``` 

> [!NOTE]
> The new search parameter will appear in your capability statement after you POST it to the database. It won’t be useable until you run a `reindex job`. It isn’t possible to tell if you have indexed a search parameter, so a best practice is to run the reindex job as soon as you complete your work to create the search parameter(s).

Required field descriptions:

* **url**: This is a unique key to describe the search parameter. Many organizations such as HL7 specify a standard for how this should be as shown above in the US Core race search parameter.

* **code**: The value here is what you’ll use when searching. For the example above, you would search with `GET {FHIR URL}/Patient?race=2028-9` to get all Asian patients. This value must be unique for the resources it applies to.

* **base**: This describes which resource(s) the search parameter applies to. If it applies to all resources, you can just use Resource; otherwise, you can list all the relevant resources.
 
* **type**: Describes the data type for the search parameter.

* **expression**: When describing a search parameter, you must include the expression. The Azure API for FHIR ignores xpath syntax right now. This describes how to find the value for the search. 

> [!NOTE]
> “Type” is limited by the support for the Azure API for FHIR. This means that you cannot define a search parameter of type Special or define a [composite search parameter](overview-fhir-search.md) unless it is of a type that we support.

Once you’ve added your search parameters, ensure to run or schedule your reindex job so they can be used in the database.

## Update a search parameter

To update a search parameter, use `PUT` as the new version of the search parameter.

`PUT {fhirurl}/SearchParameter/{SearchParameter ID}`

You must include the `SearchParameter ID` in the ID field of the body of the `PUT` request.

```json
{
  "resourceType" : "SearchParameter",
  "id" : "SearchParameter ID",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "New Description!",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

```

The result will be an updated `SearchParamter` and the version will increment.

  > [!Warning]
> Be careful when updating SearchParameters that have already been indexed in your database. Changing an existing SearchParameter’s behavior could have impacts on the expected behavior. 

## Delete a search parameter

If you need to delete a search parameter, you use the following:

`Delete {fhirurl}/SearchParameter/{SearchParameter ID}`

> [!Warning]
> Be careful when deleting SearchParameters that have already been indexed in your database. Changing an existing SearchParameter’s behavior could have impacts on the expected behavior.

## Next Steps

Now that you’ve created a search parameter, you can reindex your database to start using it in search.  