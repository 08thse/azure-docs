---
title: Routing coverage
titleSuffix: Microsoft Azure Maps
description: Learn what level of coverage Azure Maps provides in various regions for routing, routing with traffic, and truck routing. 
author: eriklindeman
ms.author: eriklind
ms.date: 10/21/2022
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
---

# Azure Maps routing coverage

This article provides coverage information for Azure Maps routing. Upon a search query, Azure Maps returns an optimal route from location A to location B. You're provided with accurate travel times, live updates of travel information, and route instructions. You can also add more search parameters such as current traffic, vehicle type, and conditions to avoid. The optimization of the route depends on the region. That's because, Azure Maps has various levels of information and accuracy for different regions. The tables in this article list the regions and what kind of information you can request for them.

## Routing information supported

In the [Azure Maps routing coverage tables], the following information is available.

### Calculate Route

The Calculate Route service calculates a route between an origin and a destination, passing through waypoints if they're specified. For more information, see [Get Route Directions] in the REST API documentation.

### Calculate Reachable Range

The Calculate Reachable Range service calculates a set of locations that can be reached from the origin point. For more information, see [Get Route Range] in the REST API documentation.

### Matrix Routing

The Matrix Routing service calculates travel time and distance between all possible pairs in a list of origins and destinations. It doesn't provide any detailed information about the routes. You can get one-to-many, many-to-one, or many-to-many route options simply by varying the number of origins and/or destinations. For more information, see [Matrix Routing service] in the REST API documentation.

### Real-time Traffic

Delivers real-time information about traffic jams, road closures, and a detailed view of the current speed and travel times across the entire road network. For more information, see [Traffic service] in the REST API documentation.

### Truck routes

The Azure Maps Truck Routing API provides travel routes that take truck attributes into consideration. Truck attributes include things such as width, height, weight, turning radius and type of cargo. This is important as not all trucks can travel the same routes as other vehicles. Here are some examples:

- Bridges have heights and weight limits.
- Tunnels often have restrictions on flammable or hazardous materials.
- Longer trucks have difficulty making tight turns.
- Highways often have a separate speed limit for trucks.
- Certain trucks may want to avoid roads that have steep gradients.

Azure Maps supports truck routing in the countries/regions indicated in the following tables.

::: zone pivot="route"

<!------------------------------------------------------------------
### Legend

| Symbol | Meaning                          |
|:------:|----------------------------------|
|   ✓    | Region has full routing data.    |
|   ◑    | Region has partial routing data. |
---------------------------------------------------------------->

## Azure Maps routing coverage tables

The following tables provide coverage information for Azure Maps routing.
<!-------------------------------ROUTING------------------------------------------------------------------------------->
### Americas

| Country/Region         | Calculate Route, Reachable Range & Matrix Routing | Real-time Traffic | Truck Route |
|----------------------------------------|:---------------------------------:|:-----------------:|:-----------:|
| Anguilla                               |                ✓                  |                   |             |
| Antigua & Barbuda                      |                ✓                  |                   |             |
| Argentina                              |                ✓                  |         ✓         |     ✓      |
| Aruba                                  |                ✓                  |                   |             |
| Bahamas                                |                ✓                  |                   |             |
| Barbados                               |                ✓                  |                   |             |
| Belize                                 |                ✓                  |                   |             |
| Bermuda                                |                ✓                  |                   |             |
| Bolivia                                |                ✓                  |                   |             |
| Bonaire, Sint Eustatius, & Saba        |                ✓                  |                   |             |
| Brazil                                 |                ✓                  |         ✓         |     ✓      |
| British Virgin Islands                 |                ✓                  |                   |             |
| Canada                                 |                ✓                  |         ✓         |     ✓      |
| Cayman Islands                         |                ✓                  |                   |             |
| Chile                                  |                ✓                  |         ✓         |             |
| Colombia                               |                ✓                  |         ✓         |             |
| Costa Rica                             |                ✓                  |                   |             |
| Cuba                                   |                ✓                  |                   |             |
| Curaçao                                |                ✓                  |                   |             |
| Dominica                               |                ✓                  |                   |             |
| Dominican Republic                     |                ✓                  |                   |             |
| Ecuador                                |                ✓                  |                   |             |
| El Salvador                            |                ✓                  |                   |             |
| Falkland Islands                       |                ✓                  |                   |             |
| French Guiana                          |                ✓                  |                   |             |
| Grenada                                |                ✓                  |                   |             |
| Guadeloupe                             |                ✓                  |                   |             |
| Guatemala                              |                ✓                  |                   |             |
| Guyana                                 |                ✓                  |                   |             |
| Haiti                                  |                ✓                  |                   |             |
| Honduras                               |                ✓                  |                   |             |
| Jamaica                                |                ✓                  |                   |             |
| Martinique                             |                ✓                  |                   |             |
| Mexico                                 |                ✓                  |         ✓         |     ✓      |
| Montserrat                             |                ✓                  |                   |             |
| Nicaragua                              |                ✓                  |                   |             |
| Panama                                 |                ✓                  |                   |             |
| Paraguay                               |                ✓                  |                   |             |
| Peru                                   |                ✓                  |         ✓         |             |
| Puerto Rico                            |                ✓                  |                   |             |
| Sint Maarten                           |                ✓                  |                   |             |
| South Georgia & South Sandwich Islands |                ✓                  |                   |             |
| St. Barthélemy                         |                ✓                  |                   |             |
| St. Kitts & Nevis                      |                ✓                  |                   |             |
| St. Lucia                              |                ✓                  |                   |             |
| St. Martin                             |                ✓                  |                   |             |
| St. Pierre & Miquelon                  |                ✓                  |                   |             |
| St. Vincent & Grenadines               |                ✓                  |                   |             |
| Suriname                               |                ✓                  |                   |             |
| Trinidad & Tobago                      |                ✓                  |                   |             |
| Turks & Caicos Islands                 |                ✓                  |                   |             |
| U.S. Virgin Islands                    |                ✓                  |                   |             |
| United States                          |                ✓                  |         ✓         |     ✓      |
| Uruguay                                |                ✓                  |         ✓         |     ✓      |
| Venezuela                              |                ✓                  |                   |             |

### Asia Pacific

| Country/Region         | Calculate Route, Reachable Range & Matrix Routing | Real-time Traffic | Truck Route |
|----------------------------------------|:---------------------------------:|:-----------------:|:-----------:|
| American Samoa                         |                ✓                  |                   |             |
| Australia                              |                ✓                  |         ✓         |     ✓      |
| Bangladesh                             |                ✓                  |                   |             |
| Bhutan                                 |                ✓                  |                   |             |
| Brunei                                 |                ✓                  |         ✓         |             |
| Cambodia                               |                ✓                  |                   |             |
| China                                  |                ✓                  |                   |             |
| Christmas Island                       |                ✓                  |                   |             |
| Cocos (Keeling) Islands                |                ✓                  |                   |             |
| Comoros                                |                ✓                  |                   |             |
| Cook Islands                           |                ✓                  |                   |             |
| Fiji                                   |                ✓                  |                   |             |
| French Polynesia                       |                ✓                  |                   |             |
| Guam                                   |                ✓                  |                   |             |
| Hong Kong SAR                          |                ✓                  |         ✓         |             |
| India                                  |                ✓                  |         ✓         |             |
| Indonesia                              |                ✓                  |         ✓         |     ✓      |
| Kiribati                               |                ✓                  |                   |             |
| Laos                                   |                ✓                  |                   |             |
| Macao SAR                              |                ✓                  |         ✓         |             |
| Malaysia                               |                ✓                  |         ✓         |      ✓     |
| Micronesia                             |                ✓                  |                   |             |
| Mongolia                               |                ✓                  |                   |             |
| Myanmar                                |                ✓                  |                   |             |
| Nauru                                  |                ✓                  |                   |             |
| Nepal                                  |                ✓                  |                   |             |
| New Caledonia                          |                ✓                  |                   |             |
| New Zealand                            |                ✓                  |         ✓         |     ✓      |
| Niue                                   |                ✓                  |                   |             |
| Norfolk Island                         |                ✓                  |                   |             |
| Northern Mariana Islands               |                ✓                  |                   |             |
| Pakistan                               |                ✓                  |                   |             |
| Palau                                  |                ✓                  |                   |             |
| Papua New Guinea                       |                ✓                  |                   |             |
| Philippines                            |                ✓                  |         ✓         |      ✓     |
| Pitcairn Islands                       |                ✓                  |                   |             |
| Samoa                                  |                ✓                  |                   |             |
| Singapore                              |                ✓                  |         ✓         |     ✓      |
| Solomon Islands                        |                ✓                  |                   |             |
| Sri Lanka                              |                ✓                  |                   |             |
| Taiwan                                 |                ✓                  |         ✓         |      ✓     |
| Thailand                               |                ✓                  |         ✓         |      ✓     |
| Timor-Leste                            |                ✓                  |                   |             |
| Tokelau                                |                ✓                  |                   |             |
| Tonga                                  |                ✓                  |                   |             |
| Tuvalu                                 |                ✓                  |                   |             |
| Vanuatu                                |                ✓                  |                   |             |
| Vietnam                                |                ✓                  |         ✓         |      ✓     |
| Wallis & Futuna                        |                ✓                  |                   |             |

### Europe

| Country/Region         | Calculate Route, Reachable Range & Matrix Routing | Real-time Traffic | Truck Route |
|----------------------------------------|:---------------------------------:|:-----------------:|:-----------:|
| Albania                                |                ✓                  |                   |     ✓       |
| Andorra                                |                ✓                  |         ✓         |     ✓      |
| Armenia                                |                ✓                  |                   |             |
| Austria                                |                ✓                  |         ✓         |     ✓      |
| Azerbaijan                             |                ✓                  |                   |             |
| Belarus                                |                ✓                  |         ✓         |             |
| Belgium                                |                ✓                  |         ✓         |     ✓      |
| Bosnia & Herzegovina                   |                ✓                  |         ✓         |     ✓      |
| Bouvet Island                          |                ✓                  |                   |             |
| Bulgaria                               |                ✓                  |         ✓         |     ✓      |
| Croatia                                |                ✓                  |         ✓         |     ✓      |
| Cyprus                                 |                ✓                  |                   |     ✓       |
| Czech Republic                         |                ✓                  |         ✓         |     ✓      |
| Denmark                                |                ✓                  |         ✓         |     ✓      |
| Estonia                                |                ✓                  |         ✓         |     ✓      |
| Faroe Islands                          |                ✓                  |                   |             |
| Finland                                |                ✓                  |         ✓         |     ✓      |
| France                                 |                ✓                  |         ✓         |     ✓      |
| Georgia                                |                ✓                  |                   |             |
| Germany                                |                ✓                  |         ✓         |     ✓      |
| Gibraltar                              |                ✓                  |         ✓         |     ✓      |
| Greece                                 |                ✓                  |         ✓         |     ✓      |
| Greenland                              |                ✓                  |                   |             |
| Guernsey                               |                ✓                  |                   |             |
| Hungary                                |                ✓                  |         ✓         |     ✓      |
| Iceland                                |                ✓                  |         ✓         |             |
| Ireland                                |                ✓                  |         ✓         |     ✓      |
| Isle of Man                            |                ✓                  |                   |             |
| Italy                                  |                ✓                  |         ✓         |     ✓      |
| Jersey                                 |                ✓                  |                   |             |
| Kazakhstan                             |                ✓                  |         ✓         |             |
| Kyrgyzstan                             |                ✓                  |                   |             |
| Latvia                                 |                ✓                  |         ✓         |     ✓      |
| Liechtenstein                          |                ✓                  |         ✓         |     ✓      |
| Lithuania                              |                ✓                  |         ✓         |     ✓      |
| Luxembourg                             |                ✓                  |         ✓         |     ✓      |
| Malta                                  |                ✓                  |         ✓         |     ✓      |
| Moldova                                |                ✓                  |                   |             |
| Monaco                                 |                ✓                  |         ✓         |     ✓      |
| Montenegro                             |                ✓                  |                   |     ✓       |
| Netherlands                            |                ✓                  |         ✓         |     ✓      |
| North Macedonia                        |                ✓                  |                   |             |
| Norway                                 |                ✓                  |         ✓         |     ✓      |
| Poland                                 |                ✓                  |         ✓         |     ✓      |
| Portugal                               |                ✓                  |         ✓         |     ✓      |
| Romania                                |                ✓                  |         ✓         |     ✓      |
| Russia                                 |                ✓                  |         ✓         |     ✓      |
| San Marino                             |                ✓                  |         ✓         |     ✓      |
| Serbia                                 |                ✓                  |         ✓         |     ✓      |
| Slovakia                               |                ✓                  |         ✓         |     ✓      |
| Slovenia                               |                ✓                  |         ✓         |     ✓      |
| Spain                                  |                ✓                  |         ✓         |     ✓      |
| Sweden                                 |                ✓                  |         ✓         |     ✓      |
| Switzerland                            |                ✓                  |         ✓         |     ✓      |
| Tajikistan                             |                ✓                  |                   |             |
| Türkiye                                |                ✓                  |         ✓         |     ✓      |
| Turkmenistan                           |                ✓                  |                   |             |
| Ukraine                                |                ✓                  |         ✓         |             |
| United Kingdom                         |                ✓                  |         ✓         |     ✓      |
| Uzbekistan                             |                ✓                  |                   |             |
| Vatican City                           |                ✓                  |         ✓         |     ✓      |

### Middle East & Africa

| Country/Region         | Calculate Route, Reachable Range & Matrix Routing | Real-time Traffic | Truck Route |
|----------------------------------------|:---------------------------------:|:-----------------:|:-----------:|
| Afghanistan                            |                ✓                  |                   |             |
| Algeria                                |                ✓                  |                   |             |
| Angola                                 |                ✓                  |                   |             |
| Bahrain                                |                ✓                  |         ✓         |             |
| Benin                                  |                ✓                  |                   |             |
| Botswana                               |                ✓                  |                   |             |
| Burkina Faso                           |                ✓                  |                   |             |
| Burundi                                |                ✓                  |                   |             |
| Cameroon                               |                ✓                  |                   |             |
| Cape Verde                             |                ✓                  |                   |             |
| Central African Republic               |                ✓                  |                   |             |
| Chad                                   |                ✓                  |                   |             |
| Congo                                  |                ✓                  |                   |             |
| Congo (DRC)                            |                ✓                  |                   |             |
| Côte d'Ivoire                          |                ✓                  |                   |             |
| Djibouti                               |                ✓                  |                   |             |
| Egypt                                  |                ✓                  |         ✓         |             |
| Equatorial Guinea                      |                ✓                  |                   |             |
| Eritrea                                |                ✓                  |                   |             |
| Ethiopia                               |                ✓                  |                   |             |
| French Southern Territories            |                ✓                  |                   |             |
| Gabon                                  |                ✓                  |                   |             |
| Gambia                                 |                ✓                  |                   |             |
| Ghana                                  |                ✓                  |                   |             |
| Guinea                                 |                ✓                  |                   |             |
| Guinea-Bissau                          |                ✓                  |                   |             |
| Iran                                   |                ✓                  |                   |             |
| Iraq                                   |                ✓                  |                   |             |
| Israel                                 |                ✓                  |         ✓         |      ✓     |
| Jordan                                 |                ✓                  |                   |             |
| Kenya                                  |                ✓                  |         ✓         |             |
| Kuwait                                 |                ✓                  |         ✓         |             |
| Lebanon                                |                ✓                  |                   |             |
| Lesotho                                |                ✓                  |         ✓         |             |
| Liberia                                |                ✓                  |                   |             |
| Libya                                  |                ✓                  |                   |             |
| Madagascar                             |                ✓                  |                   |             |
| Malawi                                 |                ✓                  |                   |             |
| Maldives                               |                ✓                  |                   |             |
| Mali                                   |                ✓                  |                   |             |
| Marshall Islands                       |                ✓                  |                   |             |
| Mauritania                             |                ✓                  |                   |             |
| Mauritius                              |                ✓                  |                   |             |
| Mayotte                                |                ✓                  |                   |             |
| Morocco                                |                ✓                  |         ✓         |             |
| Mozambique                             |                ✓                  |         ✓         |             |
| Namibia                                |                ✓                  |                   |             |
| Niger                                  |                ✓                  |                   |             |
| Nigeria                                |                ✓                  |         ✓         |             |
| Oman                                   |                ✓                  |         ✓         |             |
| Qatar                                  |                ✓                  |         ✓         |             |
| Réunion                                |                ✓                  |                   |             |
| Rwanda                                 |                ✓                  |                   |             |
| São Tomé & Príncipe                    |                ✓                  |                   |             |
| Saudi Arabia                           |                ✓                  |         ✓         |             |
| Senegal                                |                ✓                  |                   |             |
| Seychelles                             |                ✓                  |                   |             |
| Sierra Leone                           |                ✓                  |                   |             |
| Somalia                                |                ✓                  |                   |             |
| South Africa                           |                ✓                  |         ✓         |      ✓     |
| South Sudan                            |                ✓                  |                   |             |
| St. Helena                             |                ✓                  |                   |             |
| Sudan                                  |                ✓                  |                   |             |
| Swaziland                              |                ✓                  |                   |             |
| Syria                                  |                ✓                  |                   |             |
| Tanzania                               |                ✓                  |                   |             |
| Togo                                   |                ✓                  |                   |             |
| Tunisia                                |                ✓                  |                   |             |
| Uganda                                 |                ✓                  |                   |             |
| United Arab Emirates                   |                                   |                   |             |
| Yemen                                  |                ✓                  |                   |             |
| Zambia                                 |                ✓                  |                   |             |
| Zimbabwe                               |                ✓                  |                   |             |

::: zone-end

<!-----------------------------ROUTING V2 ------------------------------------------------------------------------------>
::: zone pivot="route-v2"

| Country/Region                             | Road Data / Routing | Traffic | Truck Routing |
|--------------------------------------------|---------------------|---------|---------------|
| Afghanistan                                | Major Roads Only    |         |               |
| Albania                                    | Good                |         | ✓             |
| Algeria                                    | Good                |         |               |
| American Samoa                             | Good                |         |               |
| Andorra                                    | Good                | ✓       | ✓             |
| Angola                                     | Good                |         |               |
| Anguilla                                   | Good                |         |               |
| Antarctica                                 | Major Roads Only    |         |               |
| Antigua & Barbuda                          | Good                |         |               |
| Argentina                                  | Good                | ✓       | ✓             |
| Armenia                                    | Good                |         |               |
| Aruba                                      | Good                |         |               |
| Australia                                  | Good                | ✓       | ✓             |
| Austria                                    | Good                | ✓       | ✓             |
| Azerbaijan                                 | Good                |         |               |
| Bahamas                                    | Good                |         |               |
| Bahrain                                    | Good                | ✓       |               |
| Bangladesh                                 | Major Roads Only    |         |               |
| Barbados                                   | Good                |         |               |
| Belarus                                    | Good                |         |               |
| Belgium                                    | Good                | ✓       | ✓             |
| Belize                                     | Good                |         |               |
| Benin                                      | Fair                |         |               |
| Bermuda                                    | Good                |         |               |
| Bhutan                                     | Major Roads Only    |         |               |
| Bolivia                                    | Good                |         |               |
| Bonaire                                    | Good                |         |               |
| Bosnia & Herzegovina                       | Good                |         | ✓             |
| Botswana                                   | Good                |         |               |
| Bouvet Island                              | Major Roads Only    |         |               |
| Brazil                                     | Good                | ✓       | ✓             |
| British Indian Ocean Territory             | Major Roads Only    |         |               |
| British Virgin Islands                     | Good                |         |               |
| Brunei                                     | Good                | ✓       |               |
| Bulgaria                                   | Good                | ✓       | ✓             |
| Burkina Faso                               | Fair                |         |               |
| Burundi                                    | Good                |         |               |
| Cape Verde                                 | Good                |         |               |
| Cambodia                                   | Good                |         |               |
| Cameroon                                   | Fair                |         |               |
| Canada                                     | Good                | ✓       | ✓             |
| Cayman Islands                             | Good                |         |               |
| Central African Republic                   | Major Roads Only    |         |               |
| Chad                                       | Major Roads Only    |         |               |
| Chile                                      | Good                | ✓       |               |
| China                                      | Good                | ✓       |               |
| Christmas Island                           | Good                |         |               |
| Cocos (Keeling) Islands                    | Major Roads Only    |         |               |
| Colombia                                   | Good                | ✓       |               |
| Comoros                                    | Major Roads Only    |         |               |
| Congo                                      | Fair                |         |               |
| Congo (DRC)                                | Fair                |         |               |
| Cook Islands                               | Major Roads Only    |         |               |
| Costa Rica                                 | Good                |         |               |
| Côte d’Ivoire                              | Fair                |         |               |
| Croatia                                    | Good                | ✓       | ✓             |
| Cuba                                       | Good                |         |               |
| Curaçao                                    | Good                |         |               |
| Cyprus                                     | Good                |         | ✓             |
| Czech Republic                             | Good                | ✓       | ✓             |
| Denmark                                    | Good                | ✓       | ✓             |
| Djibouti                                   | Major Roads Only    |         |               |
| Dominica                                   | Good                |         |               |
| Dominican Republic                         | Good                |         |               |
| Ecuador                                    | Good                |         |               |
| Egypt                                      | Good                | ✓       |               |
| El Salvador                                | Good                |         |               |
| Equatorial Guinea                          | Major Roads Only    |         |               |
| Eritrea                                    | Major Roads Only    |         |               |
| Estonia                                    | Good                | ✓       | ✓             |
| Eswatini                                   | Good                |         |               |
| Ethiopia                                   | Major Roads Only    |         |               |
| Falkland Islands                           | Major Roads Only    |         |               |
| Faroe Islands                              | Good                |         |               |
| Fiji                                       | Good                |         |               |
| Finland                                    | Good                | ✓       | ✓             |
| France                                     | Good                | ✓       | ✓             |
| French Guiana                              | Good                |         |               |
| French Polynesia                           | Major Roads Only    |         |               |
| French Southern Territories                | Major Roads Only    |         |               |
| Gabon                                      | Fair                |         |               |
| Gambia                                     | Fair                |         |               |
| Georgia                                    | Good                |         |               |
| Germany                                    | Good                | ✓       | ✓             |
| Ghana                                      | Good                |         |               |
| Gibraltar                                  | Good                | ✓       | ✓             |
| Greece                                     | Good                | ✓       | ✓             |
| Greenland                                  | Major Roads Only    |         |               |
| Grenada                                    | Good                |         |               |
| Guadeloupe                                 | Good                |         |               |
| Guam                                       | Good                |         |               |
| Guatemala                                  | Good                |         |               |
| Guernsey                                   | Good                | ✓       |               |
| Guinea                                     | Major Roads Only    |         |               |
| Guinea-Bissau                              | Major Roads Only    |         |               |
| Guyana                                     | Good                |         |               |
| Haiti                                      | Good                |         |               |
| Heard Island & McDonald Islands            | Major Roads Only    |         |               |
| Honduras                                   | Good                |         |               |
| Hong Kong SAR                              | Good                | ✓       |               |
| Hungary                                    | Good                | ✓       | ✓             |
| Iceland                                    | Good                | ✓       |               |
| India                                      | Good                | ✓       |               |
| Indonesia                                  | Good                | ✓       | ✓             |
| Iran                                       | Major Roads Only    |         |               |
| Iraq                                       | Good                |         |               |
| Ireland                                    | Good                | ✓       | ✓             |
| Isle of Man                                | Good                | ✓       |               |
| Israel                                     | Good                | ✓       | ✓             |
| Italy                                      | Good                | ✓       | ✓             |
| Jamaica                                    | Good                |         |               |
| Jan Mayen                                  | Good                |         |               |
| Japan                                      | Good                | ✓       |               |
| Jersey                                     | Good                | ✓       |               |
| Jordan                                     | Good                |         |               |
| Kazakhstan                                 | Good                |         |               |
| Kenya                                      | Good                | ✓       |               |
| Kiribati                                   | Major Roads Only    |         |               |
| Korea                                      | Good                | ✓       |               |
| Kosovo                                     | Good                |         |               |
| Kuwait                                     | Good                | ✓       |               |
| Kyrgyzstan                                 | Major Roads Only    |         |               |
| Laos                                       | Good                |         |               |
| Latvia                                     | Good                | ✓       | ✓             |
| Lebanon                                    | Good                |         |               |
| Lesotho                                    | Good                | ✓       |               |
| Liberia                                    | Major Roads Only    |         |               |
| Libya                                      | Major Roads Only    |         |               |
| Liechtenstein                              | Good                | ✓       | ✓             |
| Lithuania                                  | Good                | ✓       | ✓             |
| Luxembourg                                 | Good                | ✓       | ✓             |
| Macao SAR                                  | Good                |         |               |
| Madagascar                                 | Major Roads Only    |         |               |
| Malawi                                     | Good                |         |               |
| Malaysia                                   | Good                | ✓       | ✓             |
| Maldives                                   | Major Roads Only    |         |               |
| Mali                                       | Fair                |         |               |
| Malta                                      | Good                | ✓       | ✓             |
| Marshall Islands                           | Major Roads Only    |         |               |
| Martinique                                 | Good                |         |               |
| Mauritania                                 | Fair                |         |               |
| Mauritius                                  | Good                |         |               |
| Mayotte                                    | Good                |         |               |
| Mexico                                     | Good                | ✓       | ✓             |
| Micronesia                                 | Major Roads Only    |         |               |
| Moldova                                    | Good                |         |               |
| Monaco                                     | Good                | ✓       | ✓             |
| Mongolia                                   | Major Roads Only    |         |               |
| Montenegro                                 | Good                |         | ✓             |
| Montserrat                                 | Good                |         |               |
| Morocco                                    | Good                | ✓       |               |
| Mozambique                                 | Good                | ✓       |               |
| Myanmar                                    | Good                |         |               |
| Namibia                                    | Good                |         |               |
| Nauru                                      | Major Roads Only    |         |               |
| Nepal                                      | Major Roads Only    |         |               |
| Netherlands                                | Good                | ✓       | ✓             |
| New Caledonia                              | Major Roads Only    |         |               |
| New Zealand                                | Good                | ✓       | ✓             |
| Nicaragua                                  | Good                |         |               |
| Niger                                      | Fair                |         |               |
| Nigeria                                    | Good                | ✓       |               |
| Niue                                       | Major Roads Only    |         |               |
| Norfolk Island                             | Major Roads Only    |         |               |
| North Korea                                | Major Roads Only    |         |               |
| North Macedonia                            | Good                |         |               |
| Northern Mariana Islands                   | Good                |         |               |
| Norway                                     | Good                | ✓       | ✓             |
| Oman                                       | Good                | ✓       |               |
| Pakistan                                   | Major Roads Only    |         |               |
| Palau                                      | Major Roads Only    |         |               |
| Palestinian Authority                      | Major Roads Only    |         |               |
| Panama                                     | Good                |         |               |
| Papua New Guinea                           | Major Roads Only    |         |               |
| Paraguay                                   | Good                |         |               |
| Peru                                       | Good                | ✓       |               |
| Philippines                                | Good                | ✓       | ✓             |
| Pitcairn Islands                           | Major Roads Only    |         |               |
| Poland                                     | Good                | ✓       | ✓             |
| Portugal                                   | Good                | ✓       | ✓             |
| Puerto Rico                                | Good                | ✓       |               |
| Qatar                                      | Good                | ✓       |               |
| Réunion                                    | Good                |         |               |
| Romania                                    | Good                | ✓       | ✓             |
| Russia                                     | Good                | ✓       | ✓             |
| Rwanda                                     | Good                |         |               |
| Saba                                       | Good                |         |               |
| Saint Barthélemy                           | Good                |         |               |
| Saint Kitts & Nevis                        | Good                |         |               |
| Saint Lucia                                | Good                |         |               |
| Saint Martin                               | Good                |         |               |
| Saint Pierre & Miquelon                    | Good                |         |               |
| Saint Vincent & the Grenadines             | Good                |         |               |
| Samoa                                      | Major Roads Only    |         |               |
| San Marino                                 | Good                | ✓       | ✓             |
| São Tomé & Príncipe                        | Major Roads Only    |         |               |
| Saudi Arabia                               | Good                | ✓       |               |
| Senegal                                    | Good                |         |               |
| Serbia                                     | Good                |         | ✓             |
| Seychelles                                 | Good                |         |               |
| Sierra Leone                               | Major Roads Only    |         |               |
| Singapore                                  | Good                | ✓       | ✓             |
| Sint Eustatius                             | Good                |         |               |
| Sint Maarten                               | Good                |         |               |
| Slovakia                                   | Good                | ✓       | ✓             |
| Slovenia                                   | Good                | ✓       | ✓             |
| Solomon Islands                            | Major Roads Only    |         |               |
| Somalia                                    | Major Roads Only    |         |               |
| South Africa                               | Good                | ✓       | ✓             |
| South Georgia & the South Sandwich Islands | Major Roads Only    |         |               |
| South Sudan                                | Major Roads Only    |         |               |
| Spain                                      | Good                | ✓       | ✓             |
| Sri Lanka                                  | Major Roads Only    |         |               |
| St Helena, Ascension, Tristan da Cunha     | Major Roads Only    |         |               |
| Sudan                                      | Major Roads Only    |         |               |
| Suriname                                   | Good                |         |               |
| Svalbard                                   | Good                |         |               |
| Sweden                                     | Good                | ✓       | ✓             |
| Switzerland                                | Good                | ✓       | ✓             |
| Syria                                      | Major Roads Only    |         |               |
| Taiwan                                     | Good                | ✓       | ✓             |
| Tajikistan                                 | Major Roads Only    |         |               |
| Tanzania                                   | Good                |         |               |
| Thailand                                   | Good                | ✓       | ✓             |
| Timor-Leste                                | Major Roads Only    |         |               |
| Togo                                       | Fair                |         |               |
| Tokelau                                    | Major Roads Only    |         |               |
| Tonga                                      | Major Roads Only    |         |               |
| Trinidad & Tobago                          | Good                |         |               |
| Tunisia                                    | Good                |         |               |
| Turkey                                     | Good                | ✓       | ✓             |
| Turkmenistan                               | Major Roads Only    |         |               |
| Turks & Caicos Islands                     | Good                |         |               |
| Tuvalu                                     | Major Roads Only    |         |               |
| U.S. Outlying Islands                      | Major Roads Only    |         |               |
| U.S. Virgin Islands                        | Good                |         |               |
| Uganda                                     | Good                |         |               |
| Ukraine                                    | Good                | ✓       |               |
| United Arab Emirates                       | Good                | ✓       |               |
| United Kingdom                             | Good                | ✓       | ✓             |
| United States                              | Good                | ✓       | ✓             |
| Uruguay                                    | Good                | ✓       | ✓             |
| Uzbekistan                                 | Major Roads Only    |         |               |
| Vanuatu                                    | Major Roads Only    |         |               |
| Vatican City                               | Good                | ✓       | ✓             |
| Venezuela                                  | Good                |         |               |
| Vietnam                                    | Good                | ✓       | ✓             |
| Wallis & Futuna                            | Major Roads Only    |         |               |
| Yemen                                      | Good                |         |               |
| Zambia                                     | Good                |         |               |
| Zimbabwe                                   | Good                |

::: zone-end

## Next steps

For more information about Azure Maps routing, see the [Routing service] documentation.

For more coverage tables, see:

- Check out coverage for [Geocoding].
- Check out coverage for [Traffic].  
- Check out coverage for [Render].

[Azure Maps routing coverage tables]: #azure-maps-routing-coverage-tables
[Geocoding]: geocoding-coverage.md
[Get Route Directions]: /rest/api/maps/route/get-route-directions
[Get Route Range]: /rest/api/maps/route/get-route-range
[Matrix Routing service]: /rest/api/maps/route/post-route-matrix
[Render]: render-coverage.md
[Routing service]: /rest/api/maps/route
[Traffic service]: /rest/api/maps/traffic
[Traffic]: traffic-coverage.md
