---
# Mandatory fields.
title: Extending DTDL industry ontologies
titleSuffix: Azure Digital Twins
description: Learn about the reasons and strategies behind extending an industry ontology
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 1/12/2021
ms.topic: conceptual
ms.service: digital-twins

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Extend DTDL ontologies 

An industry ontology, such as the [DTDL-based RealEstateCore ontology for smart buildings](https://github.com/Azure/opendigitaltwins-building), is a great way to jumpstart building your IoT solution. These industry ontologies provide a rich set of base interfaces that are designed for your domain and engineered to work out of the box in Azure IoT services, such as Azure Digital Twins. However, it is possibly the case that your solution has specific needs that are not completely covered by the industry ontology. For example, you may want to link your digital twins to 3D models stored in a separate system. In this case, it's easy to extend one of these ontologies to add your own capabilities while retaining all the benefits of the original ontology.

In this article, we will look at extending a portion of the DTDL-based REC ontology with new DTDL Properties, but the techniques described here can be applied to any part of a DTDL-based ontology with any DTDL capability (Telemetry, Property, Relationship, Component, or Command). 

## RealEstateCore space hierarchy 

In the DTDL-based REC ontology, the Space hierarchy is used to define various kinds of spaces: Rooms, Buildings, Zone, etc. The hierarchy extends from each of these models to define various kinds of Rooms, Buildings, and Zones. 

A portion of the hierarchy looks like the diagram below. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="Flow diagram illustrating part of the RealEstateCore space hierarchy. At the top level, there's an element called Space; it is connected by an 'extends' arrow down a level to Room; Room is connected by two 'extends' arrows down a level to ConferenceRoom and Office."::: 

## Extending the RealEstateCore space hierarchy 

Sometimes your solution has specific needs that are not completely covered by the industry ontology. In this case, extending the hierarchy enables you to continue to use the industry ontology while customizing it for your needs. 

In this article, we discuss two different cases where extending the ontology's hierarchy is useful: 

* Adding new interfaces for concepts not in the industry ontology. 
* Adding some additional properties (or relationships, components, telemetry, or commands) to existing interfaces.

### Add new interfaces for new concepts 

In this case, you want to add interfaces for concepts needed for your solution but that are not present in the industry ontology. For example, if your solution has additional types of rooms not present in the DTDL-based REC ontology, then you may add them by extending directly from the REC interfaces. Here we imagine a solution that needs to represent "focus rooms" not present in the REC ontology. A focus room is a small space designed for people to focus on a task for a couple hours at a time. 

To extend the industry ontology with new concepts, you create your own interfaces that extend from the interfaces in the industry ontology. 

After adding the focus room interface, the extended hierarchy shows the additional room type. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="Flow diagram illustrating the RealEstateCore space hierarchy from above, with a new addition. At the bottom level with ConferenceRoom and Office, there is a new element called FocusRoom (also connected via an 'extends' arrow from Room)"::: 

### Add additional capabilities to existing interfaces 

In this case, you want to add some additional properties (or relationships, components, telemetry, or commands) to interfaces that are in the industry ontology.

We will look at two examples: 
* If you're building a solution that displays 3D drawings of spaces that you already have in an existing system, you might want to associate each digital twin to its 3D drawing (by id) so that when the solution displays information about the space, it can also retrieve the 3D drawing from the existing system. 
* If your solution needs to track the online/offline status of conference rooms, then you might want to track the conference room status for use in display or queries. 

Both examples can be implemented with new properties: a "drawingId" property that associates the 3D drawing with the digital twin and an "online" property that indicates whether the conference room is online or not. 

Typically, you don't want to modify the industry ontology directly because you'd like to be able to incorporate updates to it in your solution in the future (which would overwrite your additions). Instead, these kinds of additions can be made in your own interface hierarchy that extends from the DTDL-based REC ontology. Each interface you create uses multiple interface inheritance to extend its parent REC interface and its parent interface from your extended interface hierarchy. This approach enables you to make use of the industry ontology and your additions together. 

To extend the industry ontology, you create your own interfaces that extend from the interfaces in the industry ontology and add the new capabilities to your extended interfaces. For each interface that you want to extend, you create a new interface. The extended interfaces are written in DTDL (see the DTDL for Extended Interfaces section later in this document). 

After extending the portion of the hierarchy shown above, the extended hierarchy looks like the diagram below. Here the extended Space interface adds the "drawingId" property that will contain an id that associates the digital twin with the 3D drawing. Additionally, the ConferenceRoom interface adds an "online" property that will contain the online status of the conference room. Through inheritance the ConferenceRoom interface contains all capabilities from the REC ConferenceRoom interface as well as all capabilities from the extended Space interface. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="Flow diagram illustrating the extended RealEstateCore space hierarchy from above, with more new additions. Room now shares its level with a Space element, which connects with an 'extends' arrow down a level to a new Room element next to ConferenceRoom and Office.  The new elements are connected to the existing ontology with additional 'extends' relationships."::: 

## Using the extended space hierarchy 

When you create digital twins using the extended Space hierarchy, each digital twin's model will be one from the extended Space hierarchy (not the original industry ontology) and will include all the capabilities from the industry ontology and the extended interfaces though interface inheritance.

Each digital twin's model will be an interface from the extended hierarchy, shown in the diagram below. 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="An excerpt from the extended RealEstateCore space hierarchy, including Space (top level), one Room (middle level), and ConferenceRoom, Office, and FocusRoom (lower level). Names of models are connected to each element (for instance, Room is connected to a model called Room101)."::: 

When querying for digital twins using the model id (the `IS_OF_MODEL` operator), the model ids from the extended hierarchy should be used. For example, `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## Contributing back to the original ontology 

In some cases, you will extend the industry ontology in a way that is broadly useful to most users of the ontology. In this case, you should consider contributing your extensions back to the original ontology. Each ontology has a different process for contributing, so check the ontology's GitHub repository for contribution details. 

## DTDL for new interfaces 

The DTDL for new interfaces that extend directly from the industry ontology would look like this. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## DTDL for extended interfaces 

The DTDL for the extended interfaces, limited to the portion discussed above, would look like this. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## Next steps

Once you are done writing your models, see how to upload them (and do other management operations) with the DigitalTwinsModels APIs:
* [*How-to: Manage custom models*](how-to-manage-model.md)