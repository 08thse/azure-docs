---
title: Azure Sentinel Partner data connectors reference | Microsoft Docs
description: See specific configuration parameters for Azure Sentinel Partner data connectors.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: yelevin

---

# Azure Sentinel partner data connectors reference

> [!IMPORTANT]
> Many of the following Azure Sentinel Partner data connectors are currently in **Preview**. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

## Agari Phishing Defense and Brand Protection (Preview)

| Connector | Agari Phishing Defense and Brand Protection |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-agari-functionapp |
| **API credentials** | <li>Client ID<li>Client Secret<li>(Optional: Graph Tenant ID, Graph Client ID, Graph Client Secret) |
| **Vendor documentation/<br>installation instructions** | <li>[Quick Start](https://developers.agari.com/agari-platform/docs/quick-start)<li>[Agari Developers Site](https://developers.agari.com/agari-platform) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPS) |
| **Application settings** | <li>clientID<li>clientSecret<li>workspaceID<li>workspaceKey<li>enableBrandProtectionAPI (true/false)<li>enablePhishingResponseAPI (true/false)<li>enablePhishingDefenseAPI (true/false)<li>resGroup (enter Resource group)<li>functionName<li>subId (enter Subscription ID)<li>enableSecurityGraphSharing (true/false; see below)<br>Required if enableSecurityGraphSharing is set to true (see below):<li>GraphTenantId<li>GraphClientId<li>GraphClientSecret<li>logAnalyticsUri (optional) |
| **Supported by:** | [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support) |
|

### Extra steps for deployment of this connector

#### Before deployment

**(Optional) Enable the Security Graph API**

The Agari Function App allows you to share threat intelligence with Azure Sentinel via the Security Graph API. To use this feature, you'll need to enable the [Sentinel Threat Intelligence Platforms connector](connect-threat-intelligence.md) and also [register an application](/graph/auth-register-app-v2) in Azure Active Directory.

This process will give you three pieces of information for use when [deploying the Function App](connect-azure-functions-template.md): the **Graph tenant ID**, the **Graph client ID**, and the **Graph client secret** (see the *Application settings* in the table above).

#### After deployment

**Assign the necessary permissions to your Function App**

The Agari connector uses an environment variable to store log access timestamps. In order for the application to write to this variable, permissions must be assigned to the system assigned identity.

1. In the Azure portal, navigate to **Function App**.

1. In the **Function App** blade, select your Function App from the list, then select **Identity** under **Settings** in the Function App's navigation menu.

1. In the **System assigned** tab, set the **Status** to **On**. 

1. Select **Save**, and an **Azure role assignments** button will appear. Click it.

1. In the **Azure role assignments** screen, select **Add role assignment**. Set **Scope** to **Subscription**, select your subscription from the **Subscription** drop-down, and set **Role** to **App Configuration Data Owner**. 

1. Select **Save**.

## Atlassian Confluence Audit (Preview)

| Connector | [Atlassian Confluence Audit](https://www.atlassian.com/software/confluence) |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-confluenceauditapi-functionapp |
| **API credentials** | <li>ConfluenceAccessToken<li>ConfluenceUsername<li>ConfluenceHomeSiteName |
| **Vendor documentation/<br>installation instructions** | <li>[API Documentation](https://developer.atlassian.com/cloud/confluence/rest/api-group-audit/)<li>[Requirements and instructions for obtaining credentials](https://developer.atlassian.com/cloud/confluence/rest/intro/#auth)<li>[View the audit log](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto function alias** | ConfluenceAudit |
| **Kusto function URL/<br>Parser config instructions** | https://aka.ms/sentinel-confluenceauditapi-parser |
| **Application settings** | <li>ConfluenceUsername<li>ConfluenceAccessToken<li>ConfluenceHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

## Atlassian Jira Audit (Preview)

| Connector | Atlassian Jira Audit |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-jiraauditapi-functionapp |
| **API credentials** | <li>JiraAccessToken<li>JiraUsername<li>JiraHomeSiteName |
| **Vendor documentation/<br>installation instructions** | <li>[API Documentation - Audit records](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/)<li>[Requirements and instructions for obtaining credentials](https://developer.atlassian.com/cloud/jira/platform/rest/v3/intro/#authentication) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto function alias** | JiraAudit |
| **Kusto function URL/<br>Parser config instructions** | https://aka.ms/sentinel-jiraauditapi-parser |
| **Application settings** | <li>JiraUsername<li>JiraAccessToken<li>JiraHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

## Cisco Umbrella (Preview)

| Connector | Cisco Umbrella |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-CiscoUmbrellaConn-functionapp |
| **API credentials** | <li>AWS Access Key Id<li>AWS Secret Access Key<li>AWS S3 Bucket Name |
| **Vendor documentation/<br>installation instructions** | <li>[Logging to Amazon S3](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto function alias** | Cisco_Umbrella |
| **Kusto function URL/<br>Parser config instructions** | https://aka.ms/sentinel-ciscoumbrella-function |
| **Application settings** | <li>WorkspaceID<li>WorkspaceKey<li>S3Bucket<li>AWSAccessKeyId<li>AWSSecretAccessKey<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

## ESET Enterprise Inspector (Preview)

| Connector | ESET Enterprise Inspector |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** |  |
| **API credentials** | <li>EEI Username<li>EEI Password<li>Base URL |
| **Vendor documentation/<br>installation instructions** | <li>[ESET Enterprise Inspector REST API documentation](https://help.eset.com/eei/1.5/en-US/api.html) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) |
| **Application settings** |  |
| **Supported by:** | [ESET](https://support.eset.com/en) |
|

### Custom steps for deployment of this connector



## Google Workspace (G-Suite) (Preview)

| Connector | Google Workspace (G-Suite) |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-GWorkspaceReportsAPI-functionapp |
| **API credentials** | <li>GooglePickleString |
| **Vendor documentation/<br>installation instructions** | <li>[API Documentation](https://developers.google.com/admin-sdk/reports/v1/reference/activities)<li>Get credentials at [Perform Google Workspace Domain-Wide Delegation of Authority](https://developers.google.com/admin-sdk/reports/v1/guides/delegation)<li>[Convert token.pickle file to pickle string](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto function alias** | GWorkspaceActivityReports |
| **Kusto function URL/<br>Parser config instructions** | https://aka.ms/sentinel-GWorkspaceReportsAPI-parser |
| **Application settings** | <li>GooglePickleString<li>WorkspaceID<li>workspaceKey<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

## Netskope (Preview)

| Connector | Netskope |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-netskope-functioncode |
| **API credentials** | <li>Netskope API Token |
| **Vendor documentation/<br>installation instructions** | <li>[Netskope Cloud Security Platform](https://www.netskope.com/platform)<li>[Netskope API Documentation](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v1-overview.html)<li>[Obtain an API Token](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v2-overview.html) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPS) |
| **Kusto function alias** | Netskope |
| **Kusto function URL/<br>Parser config instructions** | https://aka.ms/sentinel-netskope-parser |
| **Application settings** | <li>apikey<li>workspaceID<li>workspaceKey<li>uri (depends on region, follows schema: `https://<Tenant Name>.goskope.com`) <li>timeInterval (set to 5)<li>logTypes<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

## Okta Single Sign-On (Preview)

| Connector | Okta Single Sign-On |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentineloktaazurefunctioncodev2 |
| **API credentials** | <li>API Token |
| **Vendor documentation/<br>installation instructions** | <li>[Okta System Log API Documentation](https://developer.okta.com/docs/reference/api/system-log/)<li>[Create an API token](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/)<li>[Connect Okta SSO to Azure Sentinel](connect-okta-single-sign-on.md) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPS) |
| **Application settings** | <li>apiToken<li>workspaceID<li>workspaceKey<li>uri (follows schema `https://<OktaDomain>/api/v1/logs?since=`. [Identify your domain namespace](https://developer.okta.com/docs/reference/api-overview/#url-namespace).) <li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

## Proofpoint On Demand (POD) Email Security (Preview)

| Connector | Proofpoint On Demand (POD) Email Security |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-proofpointpod-functionapp |
| **API credentials** | <li>ProofpointClusterID<li>ProofpointToken |
| **Vendor documentation/<br>installation instructions** | <li>[Sign in to the Proofpoint Community](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public)<li>[Proofpoint API documentation and instructions](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto function alias** | ProofpointPOD |
| **Kusto function URL/<br>Parser config instructions** | https://aka.ms/sentinel-proofpointpod-parser |
| **Application settings** | <li>ProofpointClusterID<li>ProofpointToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

## Proofpoint Targeted Attack Protection (TAP) (Preview)

| Connector | Proofpoint Targeted Attack Protection (TAP) |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinelproofpointtapazurefunctioncode |
| **API credentials** | <li>API Username<li>API Password |
| **Vendor documentation/<br>installation instructions** | <li>[Proofpoint SIEM API Documentation](https://help.proofpoint.com/Threat_Insight_Dashboard/API_Documentation/SIEM_API) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPS) |
| **Application settings** | <li>apiUsername<li>apiUsername<li>uri (set to `https://tap-api-v2.proofpoint.com/v2/siem/all?format=json&sinceSeconds=300`)<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

## Qualys VM KnowledgeBase (KB) (Preview)

| Connector | Qualys VM KnowledgeBase (KB) |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-qualyskb-functioncode |
| **API credentials** | <li>API Username<li>API Password |
| **Vendor documentation/<br>installation instructions** | <li>[QualysVM API User Guide](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPS) |
| **Kusto function alias** | QualysKB |
| **Kusto function URL/<br>Parser config instructions** | https://aka.ms/sentinel-qualyskb-parser |
| **Application settings** | <li>apiUsername<li>apiUsername<li>uri (by region; see [API Server list](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348). Follows schema `https://<API Server>/api/2.0`.<li>WorkspaceID<li>WorkspaceKey<li>filterParameters (add to end of URI, delimited by `&`. No spaces.)<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

### Custom instructions to deploy this connector

#### Step 1 - Configuration steps for the Qualys API

1. Log into the Qualys Vulnerability Management console with an administrator account, select the **Users** tab and the **Users** subtab.
1. Click on the **New** drop-down menu and select **Users**.
1. Create a username and password for the API account.
1. In the **User Roles** tab, ensure the account role is set to **Manager** and access is allowed to **GUI** and **API**
1. Log out of the administrator account and log into the console with the new API credentials for validation, then log out of the API account.
1. Log back into the console using an administrator account and modify the API accounts User Roles, removing access to **GUI**.
1. Save all changes.

## Qualys Vulnerability Management (VM) (Preview)

| Connector | Qualys Vulnerability Management (VM) |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinelqualysvmazurefunctioncode |
| **API credentials** | <li>API Username<li>API Password |
| **Vendor documentation/<br>installation instructions** | <li>[QualysVM API User Guide](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPS) |
| **Application settings** | <li>apiUsername<li>apiUsername<li>uri (by region; see [API Server list](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348). Follows schema `https://<API Server>/api/2.0/fo/asset/host/vm/detection/?action=list&vm_processed_after=`.<li>WorkspaceID<li>WorkspaceKey<li>filterParameters (add to end of URI, delimited by `&`. No spaces.)<li>tineInterval (set to 5)<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

### Custom instructions to deploy this connector

#### Step 1 - Configuration steps for the Qualys API

1. Log into the Qualys Vulnerability Management console with an administrator account, select the **Users** tab and the **Users** subtab.
1. Click on the **New** drop-down menu and select **Users**.
1. Create a username and password for the API account.
1. In the **User Roles** tab, ensure the account role is set to **Manager** and access is allowed to **GUI** and **API**
1. Log out of the administrator account and log into the console with the new API credentials for validation, then log out of the API account.
1. Log back into the console using an administrator account and modify the API accounts User Roles, removing access to **GUI**.
1. Save all changes.

## Salesforce Service Cloud (Preview)

| Connector | Salesforce Service Cloud |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-SalesforceServiceCloud-functionapp |
| **API credentials** | <li>Salesforce API Username<li>Salesforce API Password<li>Salesforce Security Token<li>Salesforce Consumer Key<li>Salesforce Consumer Secret |
| **Vendor documentation/<br>installation instructions** | <li>[Salesforce REST API Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto function alias** | SalesforceServiceCloud |
| **Kusto function URL/<br>Parser config instructions** | https://aka.ms/sentinel-SalesforceServiceCloud-parser |
| **Application settings** | <li>SalesforceUser<li>SalesforcePass<li>SalesforceSecurityToken<li>SalesforceConsumerKey<li>SalesforceConsumerSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

## SentinelOne (Preview)

| Connector | SentinelOne |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-SentinelOneAPI-functionapp |
| **API credentials** | <li>SentinelOneAPIToken<li>SentinelOneUrl (`https://<SOneInstanceDomain>.sentinelone.net`) |
| **Vendor documentation/<br>installation instructions** | <li>https://`<SOneInstanceDomain>`.sentinelone.net/api-doc/overview<li>See instructions below |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto function alias** | SentinelOne |
| **Kusto function URL/<br>Parser config instructions** | https://aka.ms/sentinel-SentinelOneAPI-parser |
| **Application settings** | <li>SentinelOneAPIToken<li>SentinelOneUrl<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

### Custom instructions for deploying this connector

#### STEP 1 - Configuration steps for the SentinelOne API

Follow the instructions to obtain the credentials.

1. Log in to the SentinelOne Management Console with Admin user credentials.
1. In the Management Console, click **Settings**.
1. In the **SETTINGS** view, click **USERS**
1. Click **New User**.
1. Enter the information for the new console user.
1. In Role, select **Admin**.
1. Click **SAVE**
1. Save credentials of the new user for using in the data connector.

## Trend Micro XDR (Preview)

| Connector | Trend Micro XDR |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** |  |
| **API credentials** | <li>API Token |
| **Vendor documentation/<br>installation instructions** | <li>https://automation.trendmicro.com/xdr/home<li>[Obtaining API Keys for Third-Party Access](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) |
| **Application settings** |  |
| **Supported by:** | [Trend Micro](https://success.trendmicro.com/technical-support) |
|

## VMware Carbon Black Endpoint Standard (Preview)

| Connector | VMware Carbon Black Endpoint Standard |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinelcarbonblackazurefunctioncode |
| **API credentials** | **API access level** (for *Audit* and *Event* logs):<li>API ID<li>API Key<br>**SIEM access level** (for *Notification* events):<li>SIEM API ID<li>SIEM API Key |
| **Vendor documentation/<br>installation instructions** | <li>[Carbon Black API Documentation](https://developer.carbonblack.com/reference/carbon-black-cloud/cb-defense/latest/rest-api/)<li>[Creating an API Key](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPS) |
| **Application settings** | <li>apiId<li>apiKey<li>WorkspaceID<li>WorkspaceKey<li>uri (by region; [see list of options](https://community.carbonblack.com/t5/Knowledge-Base/PSC-What-URLs-are-used-to-access-the-APIs/ta-p/67346). Follows schema: `https://<API URL>.conferdeploy.net`.)<li>timeInterval (Set to 5)<li>SIEMapiId (if ingesting *Notification* events)<li>SIEMapiKey (if ingesting *Notification* events)<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

## Workplace from Facebook (Preview)

| Connector | Workplace from Facebook |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-WorkplaceFacebook-functionapp |
| **API credentials** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken |
| **Vendor documentation/<br>installation instructions** | <li>[Configure Webhooks](https://developers.facebook.com/docs/workplace/reference/webhooks)<li>[Configure permissions](https://developers.facebook.com/docs/workplace/reference/permissions) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto function alias** | Workplace_Facebook |
| **Kusto function URL/<br>Parser config instructions** | https://aka.ms/sentinel-WorkplaceFacebook-parser |
| **Application settings** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|

### Extra configuration steps for this connector

#### Before deployment

**Configure Webhooks:**

1. Log in to the Workplace with Admin user credentials.
1. In the Admin panel, click **Integrations**.
1. In the **All integrations** view, click **Create custom integration**.
1. Enter the name and description and click **Create**.
1. In the **Integration details** panel, show the **App secret** and copy it.
1. In the **Integration permissions** panel, set all read permissions. Refer to [permission page](https://developers.facebook.com/docs/workplace/reference/permissions) for details.

#### After deployment

**Add Callback URL to Webhook configuration:**

1. Open your Function App's page, go to the **Functions** list, click **Get Function URL** and copy it.
1. Go back to **Workplace from Facebook**. In the **Configure webhooks** panel, on each Tab set the **Callback URL** as the Function URL you copied in the last step, and the **Verify token** as the same value you received during automatic deployment, or entered during manual deployment.
1. Click Save.

## Zoom Reports (Preview)

| Connector | Zoom Reports |
| --- | --- |
| **Data ingestion method:** | [Azure Functions and the REST API](connect-azure-functions-template.md) |
| **Azure Function App code** | https://aka.ms/sentinel-ZoomAPI-functionapp |
| **API credentials** | <li>ZoomApiKey<li>ZoomApiSecret |
| **Vendor documentation/<br>installation instructions** | <li>[Get credentials using JWT With Zoom](https://marketplace.zoom.us/docs/guides/auth/jwt) |
| **Connector deployment instructions** | [One-click](connect-azure-functions-template.md?tabs=ARM) \| [Manual](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto function alias** | Zoom |
| **Kusto function URL/<br>Parser config instructions** | https://aka.ms/sentinel-ZoomAPI-parser |
| **Application settings** | <li>ZoomApiKey<li>ZoomApiSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (optional) |
| **Supported by:** | Microsoft |
|
