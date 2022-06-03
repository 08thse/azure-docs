---
title: Spacecraft Object - Azure Orbital
description: Learn about how you can represent your spacecraft details in Azure Orbital.
author: hrshelar
ms.service: orbital
ms.topic: conceptual
ms.custom: ga
ms.date: 06/03/2022
ms.author: hrshelar

---

# Spacecraft Object

Learn about how you can represent your spacecraft details in Azure Orbital. 

## Spacecraft details

The spacecraft object is used to capture the following three main types of information:

1. **Links** - RF details on center frequency, direction, and bandwidth for each link.
1. **Ephemeris** - The latest TLE.
1. **Licensing** - Authorizations held on a per link per ground station basis.

### Links

Make sure to capture each link that you wish to use with Azure Orbital at the time of spacecraft object creation. The following is required:

   | **Field** | **Values** |
   | --- | --- |
   | Direction | Uplink or Downlink |
   | Center Frequency | Center frequency in MHz |
   | Bandwidth | Bandwidth in MHz |
   | Polarization | RHCP, LHCP, or Linear Vertical |

Dual polarization schemes are represented by two links with their respective LHCP and RHCP polarizations.

### Ephemeris

The spacecraft ephemeris is captured in Azure Orbital using the Two-Line Element or TLE. 

A TLE is associated with the spacecraft to determine contact opportunities at the time of scheduling. The TLE is also used to determine the path the antenna must follow during the contact as the spacecraft passes over the groundstation during contact execution.

As TLEs are prone to expiration, the user must keep the TLE up-to-date using the [TLE update](update-tle.md) procedure.

### Licensing

In order to uphold regulatory requirements across the world, the spacecraft object contains authorizations on a per link and per site level that permits usage of the Azure Orbital groundstation sites.

The platform will deny scheduling or execution of contacts if none of the spacecraft object links are authorized, or if the requested contact profile contains links that aren't included in the spacecraft object authorized links.

For more information, refer to the Licensing (add link to: concepts-licensing.md when article is created) documentation.

## Managing Spacecraft Objects

Spacecraft objects can be created and deleted via the Portal and Azure Orbital SDKs. Once the object is created, modification to the object is dependent on the authorization status.

When the spacecraft is unauthorized then the spacecraft object can be modified. The SDK is the best way to make changes as the Portal only lets you make TLE updates.

When the spacecraft is unauthorized, then TLE updates are the only modifications possible. Other fields such as links become immutable. The TLE updates are possible via the Portal and Orbital SDK.

### Create spacecraft resource

For more information on how to create a spacecraft resource, refer to the details listed in the [register a spacecraft](register-spacecraft.md) article.

### Modify spacecraft resource

Refer to [Update TLE](update-tle.md) to make changes to the TLE.

Use the SDK to make changes to the links.

### Delete spacecraft resource

1. In the Azure portal search box, enter the name of the Spacecraft object you wish to delete and pull up the object.
1. Click delete and confirm the action. (to add screen shots)

## Next steps

- [Register a spacecraft](register-spacecraft.md)
