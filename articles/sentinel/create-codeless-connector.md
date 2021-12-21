---
title: Create a codeless connector for Microsoft Sentinel
description: Learn how to create a codeless connector in Microsoft Sentinel using the Codeless Connector Platform (CCP).
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 12/18/2021
---

# Create a codeless connector for Microsoft Sentinel (Public preview)

> [!IMPORTANT]
> The Codeless Connector Platform (CCP) is currently in PREVIEW. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
>

The Codeless Connector Platform (CCP) provides partners, advanced users, and developers with the ability to create custom connectors, connect them, and ingest data to Microsoft Sentinel. Connectors created via the CCP can be deployed via API, an ARM template, or as a solution in the the Microsoft Sentinel [content hub](sentinel-solutions.md).

Connectors created using CCP are fully SaaS, without any requirements for service installations, and also include health monitoring and full support from Microsoft Sentinel.

This article describes the syntax used in the JSON configuration file that defines how your connector works, and procedures for deploying your connector via API, an ARM template, or a Microsoft Sentinel solution.

## Define your connector JSON configuration

A codeless connector JSON configuration file defines both the user interface displayed for the connector in Microsoft Sentinel, and the back-end polling connection between Microsoft Sentinel and your data source.

The following sample shows the basic syntax of the JSON configuration file:

```json
{
    "kind": "<name>",
    "properties": {
        "connectorUiConfig": {...
        },
        "pollingConfig": {...
        }
    }
}
```

Your connector uses the `connectorUiConfig` section to define the [visual elements and text](#configure-your-connectors-user-interface) displayed on the data connector page in Microsoft Sentinel, and the `pollingConfig` section to define [how Microsoft Sentinel collects data](#configure-your-connectors-polling-settings) from your data source.

> [!TIP]
> Before building a connector, we recommend that you learn and understand how the source behaves and exactly what it expects to be connected to. For example, the types of authentication, pagination, and API endpoints are required for successful connections.
>

## Configure your connector's user interface

This section describes the configuration for how the user interface on the data connector page appears in Microsoft Sentinel.

Each data connector page in Microsoft Sentinel has the following areas, configured using the `connectorUiConfig` section of the [data connector configuration file](#define-your-connector-json-configuration).

|UI area  |Description  |
|---------|---------|
|**Title**     |   The title displayed for your data connector.      |
|**Icon**     |   The icon displayed for your data connector.      |
|**Status**     |  Describes whether or not your data connector is connected to Microsoft Sentinel.       |
|**Data charts**     |    Displays relevant queries and the amount of ingested data in the last two weeks.   |
|**Instructions tab**     | Includes a **Prerequisites** section, with a list of minimal validations before the user can enable the connector, and an **Instructions**, with a list of instructions to guide the user in enabling the connector. <br><br>This section can include text, buttons, forms, tables, and other common widgets to simplify the process.        |
|**Next steps tab**     |   Includes useful information for understanding how to find data in the event logs, such as sample queries.     |
|     |         |

<!--For example, see: TBD-->

The `connectorUiConfig` section of the configuration file includes the following properties:


|Name  |Type  |Description  |
|---------|---------|---------|
|**id**     |  GUID       |  A distinct ID for the connector.       |
|**title**     |  String       |Title displayed in the data connector page.         |
|**publisher**     |    String     |  Your company name.       |
|**descriptionMarkdown**     |  String, in markdown       |   A description for the connector.      |
|**additionalRequirementBanner**     |   String, in markdown      |    Text for the **Prerequisites** section of the **Instructions** tab.     |
| **graphQueriesTableName** | String | Defines the name of the Log Analytics table from which data for your queries is pulled. <br><br>The table name can be any string, but must end in `_CL`. For example: `TableName_CL`
|**graphQueries**     |   [GraphQuery[]](#graphquery)      |   Queries that present data ingestion over the last two weeks in the **Data charts** pane.<br><br>Provide either one query for all of the data connector's data types, or a different query for each data type.     |
|**sampleQueries**     | [SampleQuery[]](#samplequery)       | Sample queries for the customer to understand how to find the data in the event log, to be displayed in the **Next steps** tab.        |
|**dataTypes**     | [DataTypes[]](#datatypes)        | A list of all data types for your connector, and a query to fetch the time of the last event for each data type.        |
|**connectivityCriteria**     |   [ConnectivityCriteria[]](#connectivitycriteria)      |An object that defines how to verify if the connector is correctly defined.          |
|**availability**     | `{`<br>`  status: Number,`<br>`  isPreview: Boolean`<br>`}`        |  One of the following values: <br><br>- **1**: Connector is generally available to customers. <br>- **isPreview**: Indicates that the connector is not yet generally available.       |
|**permissions**     | [RequiredConnectorPermissions[]](#requiredconnectorpermissions)        | Lists the permissions required to enable or disable the connector.        |
|**instructionsSteps**     | [InstructionStep[]](#instructionstep)        |     An array of widget parts that explain how to install the connector, displayed on the **Instructions** tab.    |
|**metadata**     |   [Metadata](#metadata)      |  ARM template metadata, for deploying the connector as an ARM template.       |
|     |         |         |

### GraphQuery

Defines a query that presents data ingestion over the last two weeks in the **Data charts** pane.

Provide either one query for all of the data connector's data types, or a different query for each data type.

|Name  |Type  |Description  |
|---------|---------|---------|
|**metricName**     |   String      |  A meaningful name for your graph. <br><br>Example: `Total data received`       |
|**legend**     |     String    |   The string that appears in the legend to the right of the chart, including a variable reference.<br><br>Example: `{{graphQueriesTableName}}`      |
|**baseQuery**     | String        |    The query that filters for relevant events, including a variable reference. <br><br>Example: `TableName | where ProviderName == “myprovider”` or `{{graphQueriesTableName}}`     |
|     |         |         |


### SampleQuery

|Name  |Type  |Description  |
|---------|---------|---------|
| **Description** | String | A meaningful description for the sample query.<br><br>Example: `Top 10 vulnerabilities detected` |
| **Query** | String | Sample query used to fetch the data type's data. <br><br>Example: `{{graphQueriesTableName}}\n | sort by TimeGenerated\n | take 10` |
| | | |

### DataTypes

|Name  |Type  |Description  |
|---------|---------|---------|
| **dataTypeName** | String | A meaningful description for the`lastDataReceivedQuery` query, including support for a variable. <br><br>Example: `{{graphQueriesTableName}}` |
| **lastDataReceivedQuery** | String | A query that returns one row, and indicates the last time data was received, or no data if there is no relevant data. <br><br>Example: `{{graphQueriesTableName}}\n | summarize Time = max(TimeGenerated)\n | where isnotempty(Time)`
| | | |


### ConnectivityCriteria

|Name  |Type  |Description  |
|---------|---------|---------|
| **type** | ENUM | Always define this value as `SentinelKindsV2`. |
| **value** | deprecated |N/A |
| | | |

### Availability

|Name  |Type  |Description  |
|---------|---------|---------|
| **status** | Boolean | Determines whether or not the data connector is available in your workspace. <br><br>Example: `1`|
| **isPreview** | Boolean |Determines whether the data connector is supported as Preview or not. <br><br>Example: `false` |
| | | |

### RequiredConnectorPermissions

|Name  |Type  |Description  |
|---------|---------|---------|
| **tenant** | ENUM | Defines the required permissions, as one or more of the following values: `GlobalAdmin`, `SecurityAdmin`,  `SecurityReader`, `InformationProtection` <br><br>Example: The **tenant** value displays displays in Microsoft Sentinel as: **Tenant Permissions: Requires `Global Administrator` or `Security Administrator` on the workspace's tenant**|
| **licenses** | ENUM | Defines the required licenses, as one of the following values: `OfficeIRM`,`OfficeATP`, `Office365`, `AadP1P2`, `Mcas`, `Aatp`, `Mdatp`, `Mtp`, `IoT` <br><br>Example: The **licenses** value displays in Microsoft Sentinel as: **License: Required Azure AD Premium P2**|
| **customs** | String | Describes any custom permissions required for your data connection, in the following syntax: <br>`{`<br>`  name:string,`<br>` description:string`<br>`}` <br><br>Example: The **customs** value displays in Microsoft Sentinel as: **Subscription: Contributor permissions to the subscription of your IoT Hub.** |
| **resourceProvider**	| [ResourceProviderPermissions](#resourceproviderpermissions) | Describes any prerequisites for your Azure resource. <br><br>Example: The **resourceProvider** value displays in Microsoft Sentinel as: <br>**Workspace: write permission is required.**<br>**Keys: read permissions to shared keys for the workspace are required.**|
| | | |

#### ResourceProviderPermissions

|Name  |Type  |Description  |
|---------|---------|---------|
| **provider** | 	ENUM	| Describes the resource provider, with one of the following values: <br>- `Microsoft.OperationalInsights/workspaces` <br>- `Microsoft.OperationalInsights/solutions`<br>- `Microsoft.OperationalInsights/workspaces/datasources`<br>- `microsoft.aadiam/diagnosticSettings`<br>- `Microsoft.OperationalInsights/workspaces/sharedKeys`<br>- `Microsoft.Authorization/policyAssignments` |
| **providerDisplayName** | 	String	| A query that should return one row, indicating the last time that data was received, or no data if there is no relevant data. |
| **permissionsDisplayText** | 	String	| Display text for *Read*, *Write*, or *Read and Write* permissions. |
| **requiredPermissions** | 	[RequiredPermissionSet](#requiredpermissionset) | Describes the minimum permissions required for the connector as one of the following values: `read`, `write`, `delete`, `action` |
| **Scope** | 	ENUM	 | Describes the scope of the data connector, as one of the following values: `Subscription`, `ResourceGroup`, `Workspace` |
| | | |

### RequiredPermissionSet

|Name  |Type  |Description  |
|---------|---------|---------|
|**read**	| boolean | Determines whether *read* permissions are required. |
| **write** | boolean | Determines whether *write* permissions are required. |
| **delete** | boolean | Determines whether *delete* permissions are required. |
| **action** | 	boolean	| Determines whether *action* permissions are required. |
| | | |

### Metadata

This section provides metadata used when you're [deploying your data connector as an ARM template](#deploy-your-connector-in-microsoft-sentinel-and-start-ingesting-data).

|Name  |Type  |Description  |
|---------|---------|---------|
| **id** | 	String | Defines a GUID for your ARM template.  |
| **kind** 	| String | Defines the kind of ARM template you're creating. Always use `dataConnector`. |
| **source** | 	String |Describes your data source, using the following syntax: <br>`{`<br>`  kind:string`<br>`  name:string`<br>`}`|
| **author** |	String | Describes the data connector author, using the following syntax: <br>`{`<br>`  name:string`<br>`}`|
| **support** |	String | Describe the support provided for the data connector using the following syntax: <br>	`{`<br>`      "tier": string,`<br>`      "name": string,`<br>`"email": string,`<br>      `"link": string`<br>`    }`|
| | | |

### Instructions

This section provides parameters that define the set of instructions that appear on your data connector page in Microsoft Sentinel.


|Name  |Type  |Description  |
|---------|---------|---------|
| **title**	| String | Optional. Defines a title for your instructions. |
| **description** | 	String	| Optional. Defines a meaningful description for your instructions. |
| **innerSteps**	| [InstructionStep](#instructionstep) | Optional. Defines an array of inner instruction steps. |
| **bottomBorder** | 	Boolean	| When `true`, adds a bottom border to the instructions area on the connector page in Microsoft Sentinel |
| **isComingSoon** |	Boolean	| When `true`, adds a **Coming soon** title on the connector page in Microsoft Sentinel |
| | | |


#### CopyableLabel

Shows a field with a button on the right to copy the field value. For example:

:::image type="content" source="media/create-codeless-connector/copy-field-value.png" alt-text="Screenshot of a copy value button in a field.":::

**Sample code**:

```json
Implementation:
instructions: [
                new CopyableLabelInstructionModel({
                    fillWith: [“MicrosoftAwsAccount”],
                    label: “Microsoft Account ID”,
                }),
                new CopyableLabelInstructionModel({
                    fillWith: [“workspaceId”],
                    label: “External ID (WorkspaceId)”,
                }),
            ]
```

**Parameters**: `CopyableLabelInstructionParameters`

|Name  |Type  |Description  |
|---------|---------|---------|
|**fillWith**     |  ENUM       | Optional. Array of environment variables used to populate a placeholder. Separate multiple placeholders with commas. For example: `{0},{1}`  <br><br>Supported values: `workspaceId`, `workspaceName`, `primaryKey`, `MicrosoftAwsAccount`, `subscriptionId`      |
|**label**     |  String       |  Defines the text for the label above a text box.      |
|**value**     |  String       |  Defines the value to present in the text box, supports placeholders.       |
|**rows**     |   Rows      |  Optional. Defines the rows in the user interface area. By default, set to **1**.       |
|**wideLabel**     |Boolean         | Optional. Determines a wide label for long strings. By default, set to `false`.        |
|    |         |         |


#### InfoMessage

Defines an inline information message. For example:

:::image type="content" source="media/create-codeless-connector/inline-information-message.png" alt-text="Screenshot of an inline information message.":::

In contrast, the following image shows a *non*-inline information message:

:::image type="content" source="media/create-codeless-connector/non-inline-information-message.png" alt-text="Screenshot of a non-inline information message.":::

**Sample code**:

```json
instructions: [
                new InfoMessageInstructionModel({
                    text:”Microsoft Defender for Endpoint… “,
                    visible: true,
                    inline: true,
                }),
                new InfoMessageInstructionModel({
                    text:”In order to export… “,
                    visible: true,
                    inline: false,
                }),

            ]
```
**Parameters**: `InfoMessageInstructionModelParameters`


|Name  |Type  |Description  |
|---------|---------|---------|
|**text**     |    String     |   Define the text to display in the message.      |
|**visible**     |   Boolean      |    Determines whether the message is displayed.     |
|**inline**     |   Boolean      |   Determines how the information message is displayed. <br><br>- `true`: (Recommended) Shows the information message embedded in the instructions. <br>- `false`: Adds a blue background.     |
|     |         |         |



#### LinkInstructionModel

Displays a link to other pages in the Azure portal, as a button or a link. For example:

:::image type="content" source="media/create-codeless-connector/link-by-button.png" alt-text="Screenshot of a link added as a button.":::

:::image type="content" source="media/create-codeless-connector/link-by-text.png" alt-text="Screenshot of a link added as inline text.":::

**Sample code**:

```json
new LinkInstructionModel({linkType: “OpenPolicyAssignment”, policyDefinitionGuid: <GUID>, assignMode = “Policy”})

new LinkInstructionModel({ linkType: LinkType.OpenAzureActivityLog } )
```

**Parameters**: `InfoMessageInstructionModelParameters`

|Name  |Type  |Description  |
|---------|---------|---------|
|**linkType**     |   ENUM      |  Determines the link type, as one of the following values: <br><br>`InstallAgentOnWindowsVirtualMachine`<br>`InstallAgentOnWindowsNonAzure`<br> `InstallAgentOnLinuxVirtualMachine`<br> `InstallAgentOnLinuxNonAzure`<br>`OpenSyslogSettings`<br>`OpenCustomLogsSettings`<br>`OpenWaf`<br> `OpenAzureFirewall` `OpenMicrosoftAzureMonitoring` <br> `OpenFrontDoors` <br>`OpenCdnProfile` <br>`AutomaticDeploymentCEF` <br> `OpenAzureInformationProtection` <br> `OpenAzureActivityLog` <br> `OpenIotPricingModel` <br> `OpenPolicyAssignment` <br> `OpenAllAssignmentsBlade` <br> `OpenCreateDataCollectionRule`       |
|**policyDefinitionGuid**     | String        |  Optional. For policy-based connectors, defines the GUID of the built-in policy definition.        |
|**assignMode**     |   ENUM      |   Optional. For policy-based connectors, defines the assign mode, as one of the following values: `Initiative`, `Policy`      |
|**dataCollectionRuleType**     |  ENUM       |   Optional. For DCR-based connectors, defines the type of data collection rule type as one of the following: `SecurityEvent`,  `ForwardEvent`       |
|     |         |         |

To define an inline link using markdown, use the following example as a guide:

```markdown
<value>Follow the instructions found on article [Connect Microsoft Sentinel to your threat intelligence platform]({0}). Once the application is created you will need to record the Tenant ID, Client ID and Client Secret.</value>
```

The code sample listed above shows an inline link that looks like the following image:

:::image type="content" source="media/create-codeless-connector/sample-markdown-link-text.png" alt-text="Screenshot of the link text created by the earlier sample markdown.":::

To define a link as an ARM template, use the following example as a guide:

```markdown
    <value>1. Click the **Deploy to Azure** button below.
[![Deploy To Azure]({0})]({1})
```

The code sample listed above shows a link button that looks like the following image:

 :::image type="content" source="media/create-codeless-connector/sample-markdown-link-button.png" alt-text="Screenshot of the link button created by the earlier sample markdown.":::

#### InstructionStep

Displays a group of instructions, as an expandable accordion or non-expandable, separate from the main instructions section.

For example:

:::image type="content" source="media/create-codeless-connector/accordion-instruction-area.png" alt-text="Screenshot of an expandable, extra instruction group.":::

**Parameters**: `InstructionStepsGroupModelParameters`

|Name  |Type  |Description  |
|---------|---------|---------|
|**title**     |    String     |  Defines the title for the instruction step.       |
|**instructionSteps**     |  [InstructionStep[]](#instructionstep)       | Optional. Defines an array of inner instruction steps.       |
|**canCollapseAllSections**     |  Boolean       |  Optional. Determines whether the section is a collapsible accordion or not.       |
|**noFxPadding**     |   Boolean      |  Optional. If `true`, reduces the height padding to save space.       |
|**expanded**     |   Boolean      |   Optional. If `true`, shows as expanded by default.      |
|     |         |         |




## Configure your connector's polling settings

This section describes the configuration for how data is polled from your data source for a codeless data connector.

The following code shows the syntax of the `pollingConfig` section of the [CCP configuration](#define-your-connector-json-configuration) file.

```rest
"pollingConfig": {
    auth": {
        "authType": <string>,
    },
    "request": {…
    },
    "response": {…
    },
    "paging": {…
    }
 }
```

The `pollingConfig` section includes the following properties:

|Name  |Type  |Description  |
|---------|---------|---------|
|**id**     |  String | Mandatory. Defines a unique identifier for a rule or configuration entry, using one of the following values: <br><br>- A GUID (recommended) <br>- A document ID, if the data source resides in a Cosmos DB       |
|**auth**     | String | Describes the authentication properties for polling the data.  For more information, see  [auth configuration](#auth-configuration).   |
|<a name="authtype"></a>**auth.authType**     |   String | Mandatory. Defines the type of authentication, nested inside the `auth` object, as  one of the following values: `Basic`, `APIKey`, `OAuth2`, `Session`        |
|**request**     |  Nested JSON | Mandatory. Describes the request payload for polling the data, such as the API endpoint.     For more information, see [request configuration](#request-configuration).         |
|**response**     | Nested JSON |  Mandatory. Describes the response object and nested message returned from the API when polling the data. For more information, see [response configuration](#response-configuration).     |
|**paging**     | Nested JSON. |  Optional. Describes the pagination payload when polling the data.  For more information, see [paging configuration](#paging-configuration).         |
|     |         |         |

For more information, see [Sample pollingConfig code](#sample-pollingconfig-code).

### auth configuration

The `auth` section of the `[pollingConfig](#configure-your-connectors-polling-settings)` configuration includes the following parameters, depending on the type defined in the [authType](#authtype) element:

#### APIKey authType parameters

|Name  |Type   |Description  |
|---------|---------|---------|
|**APIKeyName**     |String | Optional. Defines the name of your API key, as one of the following values: <br><br>- `XAuthToken` <br>- `Authorization`        |
|**IsAPIKeyInPostPayload**     |Boolean | Determines where your API key is defined. <br><br>True: API key is defined in the POST request payload <br>False: API key is defined in the header     |
|**APIKeyIdentifier**     |  String | Optional. Defines the name of the identifier for the API key. <br><br>For example, where the authorization is defined as  `"Authorization": "token <secret>"`, this parameter is defined as: `{APIKeyIdentifier: “token”})`     |
| | | |

#### Session authType parameters

|Name  |Type  |Description  |
|---------|---------|---------|
|**QueryParameters**     |    String     | Optional. A list of query parameters, in the serialized `dictionary<string, string>` format: <br><br>`{'<attr_name>': '<val>', '<attr_name>': '<val>'... }`        |
|**IsPostPayloadJson**     |   Boolean | Optional. Determines whether the query parameters are in JSON format.  |
|**Headers**     |    String. | Optional. Defines the header used when calling the endpoint to get the session ID, and when calling the endpoint API.  <br><br> Define the string in the serialized `dictionary<string, string>` format: `{'<attr_name>': '<val>', '<attr_name>': '<val>'... }`        |
|**SessionTimeoutInMinutes**     |   String | Optional. Defines a session timeout, in minutes.       |
|**SessionIdName**     |    String | Optional. Defines an ID name for the session.  |
|**SessionLoginRequestUri**     |  String | Optional. Defines a session login request URI. |
| | | |


#### OAuth2 authType parameters

|Name  |Type  |Description  |
|---------|---------|---------|
|**AccessToken** |String | Optional. <!--description--> |
|**RefreshToken** |String | Optional. <!--description--> |
|**TokenEndpoint** |String | Optional. <!--description--> |
|**AuthorizationEndpoint** |String | Optional. <!--description--> |
|**RedirectionEndpoint** |String | Optional. <!--description--> |
| **AccessTokenExpirationDateTimeInUtc**|String | Optional. <!--description--> |
| **RefreshTokenExpirationDateTimeInUtc**|String | Optional. <!--description--> |
|**TokenEndpointHeaders** | String. | Optional. <!--description--> <br><br> Define a string in the serialized `dictionary<string, string>` format: `{'<attr_name>': '<val>', '<attr_name>': '<val>'... }`  |
|**AuthorizationEndpointHeaders** |String | Optional. <!--description--> |
|**AuthorizationEndpointQueryParameters** | String | Optional. <!--description--> <br><br> Define a string in the serialized `dictionary<string, string>` format: `{'<attr_name>': '<val>', '<attr_name>': '<val>'... }`  |
|**TokenEndpointQueryParameters** | String. | Optional. <!--description--> <br><br> Define a string in the serialized `dictionary<string, string>` format: `{'<attr_name>': '<val>', '<attr_name>': '<val>'... }`  |
|**IsTokenEndpointPostPayloadJson** | Boolean. | Optional. Determines whether query parameters are in JSON format. |
| **IsClientSecretInHeader**| Boolean | Determines whether the **client_id** and **client_secret** values are defined in the header, as is done in the Basic authentication schema, instead of in the POST payload.|
|**RefreshTokenLifetimeinSecAttributeName** |String. | Optional. Defines the attribute name from the token endpoint response, specifying the lifetime of the refresh token, in seconds. <br><br>Define a string in the `{<attr_name>: <val>}`|
|**IsJwtBearFlow** | Boolean | Optional. Determines whether you are using JWT. |
|**JwtHeaderInJson** |String | Optional. <!--description--> <br><br>Define a string in the following syntax: `{<attr_name>: <val>}`|
|**JwtClaimsInJson** |String | Optional. <!--description--> <br><br>Define a string in the following syntax: `{<attr_name>: <val>}`|
|**JwtPem** |String | Optional. Defines a secret key, in PEM Pkcs8 format.|
| | | |


### request configuration

The `request` section of the `[pollingConfig](#configure-your-connectors-polling-settings)` configuration includes the following parameters:

<!--where do the asterisks go to?-->

|Name  |Type  |Description  |
|---------|---------|---------|
|**apiEndpoint**     |   String | Mandatory. Defines the endpoint to pull data from.      |
|**httpMethod**     |String | Mandatory. Defines the API method: `GET` or `POST`       |
|**queryTimeFormat**     |  String, or *UnixTimestamp* or *UnixTimestampInMills* | Mandatory.  Defines the format used to define the query time.    <br><br>This value can be a string, or in *UnixTimestamp* or *UnixTimestampInMills* format to indicate the query start and end time in the UnixTimestamp.  |
|**startTimeAttributeName**     |  String | Mandatory<sup*</sup>Defines the name of the attribute that defines the query start time.        |
|**endTimeAttributeName**     |  String | Mandatory<sup>*</sup>. Defines the name of the attribute that defines the query end time.      |
|**queryTimeIntervalAttributeName**     | String. | Optional. Defines the name of the attribute that defines the query time interval. |
|**queryTimeIntervalDelimiter**     |   String | Optional. Defines the query time interval delimiter. |
|**queryWindowInMin**     |  String | Optional. Defines the available query window, in minutes. <br><br>Minimum value: `5` |
|**queryParameters**     |  String | Optional. Defines the parameters passed in the query in the [`eventsJsonPaths`](#eventsjsonpaths) path.  <br><br>Define the string in the serialized `dictionary<string, string>` format: `{'<attr_name>': '<val>', '<attr_name>': '<val>'... }`       |
|**queryParametersTemplate**     | String object | Optional. Defines the query parameters template to use when passing query parameters in advanced scenarios. <br><br>For example: `"queryParametersTemplate": "{'cid': 1234567, 'cmd': 'reporting', 'format': 'siem', 'data': { 'from': '{_QueryWindowStartTime}', 'to': '{_QueryWindowEndTime}'}, '{_APIKeyName}': '{_APIKey}'}"`      |
|**isPostPayloadJson**     | Boolean | Optional. Determines whether the POST payload is in JSON format.  |
|**rateLimitQPS**     |   <!--type tbd-->      |    Optional.  <!--description tbd--> |
|**timeoutInSeconds**     |  Integer | Optional. Defines the request timeout, in seconds. |
|**retryCount**     |   Integer | Optional. Defines the number of request retries to try if needed. |
|**headers**     |  String | Optional. Defines the request header value, in the serialized `dictionary<string, string>` format: `{'<attr_name>': '<val>', '<attr_name>': '<val>'... }`         |
|     |         |         |


### response configuration

The `response` section of the `[pollingConfig](#configure-your-connectors-polling-settings)` configuration includes the following parameters:

|Name  |Type  |Description  |
|---------|---------|---------|
|  <a name="eventsjsonpaths"></a> **eventsJsonPaths**  |   List of strings | Mandatory.  Defines the path to the message in the response JSON. <br><br>A JSON path expression specifies a path to an element, or a set of elements, in a JSON structure |
| **successStatusJsonPath**    |  String | Optional. Defines the path to the success message in the response JSON. |
|  **successStatusValue**   | String | Optional. Defines the path to the success message value in the response JSON    |
|  **isGzipCompressed**   |   Boolean | Optional. Determines whether the response is compressed in a gzip file.      |
|     |         |         |

The following code shows an example of the [eventsJsonPaths](#eventsjsonpaths) value for a top-level message:

```json
"eventsJsonPaths": [
              "$"
            ]
```


### paging configuration

The `paging` section of the `[pollingConfig](#configure-your-connectors-polling-settings)` configuration includes the following parameters:

|Name  |Type  |Description  |
|---------|---------|---------|
|  **pagingType**   | String | Mandatory. Determines the paging type to use in results, as one of the following values: `None`, `LinkHeader`, `NextPageToken`, `NextPageUrl`, `Offset`     |
| **linkHeaderTokenJsonPath**    |  String | Optional. Defines the JSON path to link header in the response JSON, if the `LinkHeader` isn't defined in the response header. |
| **nextPageTokenJsonPath**    |  String | Optional. Defines the path to a next page token JSON. |
| **hasNextFlagJsonPath**    |String | Optional. Defines the path to the `HasNextPage` flag attribute. |
|  **nextPageTokenResponseHeader**   | String | Optional. Defines the *next page* token header name in the response. |
| **nextPageParaName**    |  String | Optional.  Determines the *next page* name in the request. |
| **nextPageRequestHeader**    |   String | Optional. Determines the *next page* header name in the request.   |
| **nextPageUrl**    |   String | Optional. Determines the *next page* URL, if it's different from the initial request URL. |
| **nextPageUrlQueryParameters**    |  String | Optional.  Determines the *next page* URL's query parameters if it's different from the initial request's URL. <br><br>Define the string in the serialized `dictionary<string, object>` format: `{'<attr_name>': <val>, '<attr_name>': <val>... }`        |
|  **offsetParaName**   |    String | Optional. Defines the name of the offset parameter. |
|  **pageSizeParaName**   |   String | Optional. Defines the name of the page size parameter. |
| **PageSize**    |     Integer | Defines the paging size. |
|     |         |         |


### Sample pollingConfig code

The following code shows an example of the `pollingConfig` section of the [CCP configuration](#define-your-connector-json-configuration) file:

```rest
"pollingConfig": {
    "auth": {
        "authType": "APIKey",
        "APIKeyIdentifier": "token",
        "APIKeyName": "Authorization"
     },
     "request": {
        "apiEndpoint": "https://api.github.com/../{{placeHolder1}}/audit-log",
        "rateLimitQPS": 50,
        "queryWindowInMin": 15,
        "httpMethod": "Get",
        "queryTimeFormat": "yyyy-MM-ddTHH:mm:ssZ",
        "retryCount": 2,
        "timeoutInSeconds": 60,
        "headers": {
           "Accept": "application/json",
           "User-Agent": "Scuba"
        },
        "queryParameters": {
           "phrase": "created:{_QueryWindowStartTime}..{_QueryWindowEndTime}"
        }
     },
     "paging": {
        "pagingType": "LinkHeader",
        "pageSizeParaName": "per_page"
     },
     "response": {
        "eventsJsonPaths": [
          "$"
        ]
     }
}
```

## Create a connector configuration template with placeholders

You may want to create a JSON configuration file template, with placeholders parameters, to reuse across multiple connectors, or even to create a connector with data that you don't currently have.

To create placeholder parameters, define an additional array named `userRequestPlaceHoldersInput` in the [Instructions](#instructions) section of your [CCP JSON configuration](#define-your-connector-json-configuration) file, using the following syntax:

```json
"instructions": [
                {
                  "parameters": {
                    "enable": "true",
                    "userRequestPlaceHoldersInput": [
                      {
                        "displayText": "Organization Name",
                        "requestObjectKey": "apiEndpoint",
                        "placeHolderName": "{{placeHolder1}}"
                      }
                    ]
                  },
                  "type": "APIKey"
                }
              ]
```

The `userRequestPlaceHoldersInput` parameter includes the following attributes:

|Name  |Type  |Description  |
|---------|---------|---------|
|**DisplayText**     |  String       | Defines the text box display value, which is displayed to the user when connecting.       |
|**RequestObjectKey** |String | Defines the ID used to identify where in the request section of the API call to replace the placeholder value with a user value. <br><br>If you don't use this attribute, use the `PollingKeyPaths` attribute instead. |
|**PollingKeyPaths** |String |Defines an array of [JsonPath](https://www.npmjs.com/package/JSONPath) objects that directs the API call to anywhere in the template, to replace a placeholder value with a user value.<br><br>**Example**: `"pollingKeyPaths":["$.request.queryParameters.test1"]` <br><br>If you don't use this attribute, use the `RequestObjectKey` attribute instead.  |
|**PlaceHolderName** |String |Defines the name of the placeholder parameter in the JSON template file. This can be any unique value, such as `{{placeHolder}}`. |
| | |


## Deploy your connector in Microsoft Sentinel and start ingesting data

After creating your [JSON configuration file](#define-your-connector-json-configuration), including both the [user interface](#configure-your-connectors-user-interface) and [polling](#configure-your-connectors-polling-settings) configuration, deploy your connector in your Microsoft Sentinel workspace.

1. Use one of the following options to deploy your data connector:

    # [Deploy via ARM template](#tab/deploy-via-arm-template)

    Use your JSON configuration file to create an Azure Resource Manager (ARM) template to use when deploying your connector.

    The advantage of deploying via an ARM template is that several values are built-in to the template, and you don't need to define them manually in an API call.

    1. Prepare your ARM templates using the following examples: <!--links TBD-->

        > [!TIP]
        > Make sure to either define the workspace for the ARM template to deploy, or select the workspace when you deploy the ARM template, to make sure that your data connector is deployed in the correct workspace.
        >

    1. In the Azure portal, search for **Deploy a custom template**.

    1. On the **Custom deployment** page, select **Build your own template in the editor** > **Load file**. Browse to and select your local ARM template, and then save your changes.

    1. Select your subscription and resource group, and then enter the Log Analytics workspace where you want to deploy your custom connector.

    1. Select **Review + create** to deploy your custom connector to Microsoft Sentinel.

    1. In Microsoft Sentinel, go to the **Data connectors** page, search for your new connector. Configure it to start ingesting data.

    For more information, see [Deploy a local template](/azure/azure-resource-manager/templates/deployment-tutorial-local-template?tabs=azure-powershell) in the Azure Resource Manager documentation.

    # [Deploy via API](#tab/deploy-via-api)

    1. Authenticate to the Azure API. For more information, see [Getting started with REST](/rest/api/azure/).

    1. Invoke an [UPSERT](#upsert) API call to Microsoft Sentinel to deploy your new connector. Your data connector is deployed to your Microsoft Sentinel workspace, and is available on the **Data connectors** page.

    ---

1. Configure your data connector to connect your data source and start ingesting data into Microsoft Sentinel. You can connect to your data source either via the portal, as with out-of-the-box data connectors, or via API.

    When you use the Azure portal to connect, user data is sent automatically. When you connect via API, you'll need to send the relevant authentication parameters in the API call.

    # [Connect via the Azure portal](#tab/connect-via-the-azure-portal)

    In your Microsoft Sentinel data connector page, follow the instructions you've provided to connect to your data connector.

    The data connector page in Microsoft Sentinel is controlled by the [InstructionStep](#instructionstep) configuration in the `connectorUiConfig` element of the [CCP JSON configuration](#define-your-connector-json-configuration) file.  If you have issues with the user interface connection, make sure that you have the correct configuration for your authentication type.

    # [Connect via API](#tab/connect-via-api)

    Use the [CONNECT](#connect) endpoint to send a PUT method and pass the JSON configuration directly in the body of the message. For more information, see [auth configuration](#auth-configuration).

    Use the following API attributes, depending on the [authType](#authtype) defined. For each `authType` parameter, all listed attributes are mandatory and are string values.

    |authType  |Attributes  |
    |---------|---------|
    |**Basic**     |  Define: <br>- `kind` as `Basic` <br>- `userName` as your username, in quotes <br>- `password` as your password, in quotes     |
    |**APIKey**     |Define: <br>- `kind` as `APIKey` <br>- `APIKey` as your full API key string, in quotes|
    |**OAuth2**     |   Define: <br>- `kind` as `OAuth2`<br>- `authorizationCode` as your full authorization code, in quotes <br>- `clientSecret` and `clientId` as your client secret and ID values, each in quotes      |
    |     |         |

    If you're using a [template configuration file with placeholder data](#create-a-connector-configuration-template-with-placeholders), send the data together with the `placeHolderValue` attributes that hold the user data. For example:

    ```rest
    "requestConfigUserInputValues": [
        {
           "displayText": "<A display name>",
           "placeHolderName": "<A placeholder name>",
           "placeHolderValue": "<A value for the placeholder>",
           "pollingKeyPaths": "<Array of items to use in place of the placeHolderName>"
         }
    ]
    ```

    ---

1. In Microsoft Sentinel, go to the **Logs** page and verify that you see the logs from your data source flowing in to your workspace.

If you don't see data flowing into Microsoft Sentinel, check your data source documentation and troubleshooting resources, check the configuration details, and check the connectivity.

### Disconnect your connector

If you no longer need your connector's data, disconnect the connector to stop the data flow.

Use one of the following methods:

- **Azure portal**: In your Microsoft Sentinel data connector page, select **Disconnect**.

- **API**: Use the [DISCONNECT](#disconnect) API to send a PUT call with an empty body to the following URL:

    ```rest
    https://management.azure.com /subscriptions/{{SUB}}/resourceGroups/{{RG}}/providers/Microsoft.OperationalInsights/workspaces/{{WS-NAME}}/providers/Microsoft.SecurityInsights/dataConnectors/{{Connector_Id}}/disconnect?api-version=2021-03-01-preview
    ```

## Supported REST API actions

This section describes the API actions supported by the CCP, and the REST API endpoints provided to manage the connector:

### GET

Retrieves the connector configuration file for the specified connector ID.

**Method**: GET

**Endpoint URL**:

```rest
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/providers/Microsoft.SecurityInsights/dataConnectors/{connector-name-GUID}/?api-version={apiVersion}`
```

### UPSERT

Creates a new connector, or updates an existing connector, for the specified connector ID. New connectors are created by pushing the connector configuration in the body of the UPSERT action.

**Method**: PUT

**Endpoint URL**:

```rest
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/providers/Microsoft.SecurityInsights/dataConnectors/{connector-name-GUID}/?api-version={apiVersion}
```

### CONNECT

Initiates the connector data pulling mechanism for the specified connector ID.

Codeless data connectors pull data from publicly accessible APIs. To pull data, Microsoft Sentinel must authenticate to the data source service using an authentication method supported by both the data source's API and the CCP.

Authentication data isn't included in the codeless connector's configuration, but provided when you [connect your data connector from Microsoft Sentinel](#deploy-your-connector-in-microsoft-sentinel-and-start-ingesting-data). This connection is performed using the **CONNECT** action, with connection data stored in an encrypted keystore residing in the customer region.

Connect using one of the following authentication methods:

- **Basic authentication**. Passes a username and password to authenticate to the REST API.

- **API key**. Provides an API key to the API.

- **OAuth2**. Uses an open standard for access delegation intended to grant access from applications to an API without supporting the actual authentication data.

    Using OAuth2 requires app registration, authentication, and then getting a token and access by the application using the registration and authentication data. When connecting a codeless data connector, register Microsoft Sentinel as your application, and then interact directly with your data source's API authentication process for the required keys. Provide the keys to the connector when you need to connect.


**Method**: POST

**Endpoint URL**:

```rest
subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/providers/Microsoft.SecurityInsights/dataConnectors/{connector-name-GUID}/connect?api-version={apiVersion}
```


### DISCONNECT

[Disconnects the connector](#disconnect-your-connector) specified by the connector ID from Microsoft Sentinel.

**Method**: POST

**Endpoint URL**:

```rest
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/providers/Microsoft.SecurityInsights/dataConnectors/{connector-name-GUID}/disconnect?api-version={apiVersion}
```

### DELETE

**Method**: DELETE

Deletes a connector specified by the connector ID.

```rest
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/providers/Microsoft.SecurityInsights/dataConnectors/{connector-name-GUID}/?api-version={apiVersion}
```

For more information, see the [DataConnectors.json](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/securityinsights/resource-manager/Microsoft.SecurityInsights/stable/2020-01-01/DataConnectors.json) file in the Azure REST API specs GitHub repository.





## Next steps

If you haven't yet, share your new codeless data connector with the Microsoft Sentinel community! Create a solution for your data connector and share it in the Microsoft Sentinel Marketplace.

For more information, see [About Microsoft Sentinel solutions](sentinel-solutions.md).
