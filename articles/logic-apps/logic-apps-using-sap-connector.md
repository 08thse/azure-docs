---
title: Connect to SAP
description: Connect to an SAP server from a workflow in Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, azla
ms.topic: how-to
ms.date: 05/15/2023
tags: connectors
---

# Connect to SAP from workflows in Azure Logic Apps

[!INCLUDE [logic-apps-sku-consumption-standard](../../includes/logic-apps-sku-consumption-standard.md)]

This guide shows how to access your SAP server from a workflow in Azure Logic Apps using the SAP connector. You can use this connector's operations to create automated workflows that run when triggered by events in your SAP server or in other systems and run actions to manage resources on your SAP server.

> [!IMPORTANT]
> For Standard logic app workflows, the SAP *built-in* connector is in preview and is subject to the 
> [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## SAP compatibility

The SAP connector is compatible with the following types of SAP systems:

* On-premises and cloud-based HANA-based SAP systems, such as S/4 HANA.

* Classic on-premises SAP systems, such as R/3 and ECC.

SAP must support the SAP system version that you want to connect. Otherwise, any issues that you might encounter might not be resolvable. For more information about SAP system versions and maintenance information, review the [SAP Product Availability Matrix (PAM)](http://support.sap.com/pam).

The SAP connector supports the following message and data integration types from SAP NetWeaver-based systems:

* Intermediate Document (IDoc)

* Business Application Programming Interface (BAPI)

* Remote Function Call (RFC) and Transactional RFC (tRFC)

The SAP connector uses the [SAP .NET Connector (NCo) library](https://support.sap.com/en/product/connectors/msnet.html).

* To use the SAP connector operations, you have to first authenticate your connection. For authentication, you have the following options:

  * You can provide a username and password.

  * The SAP connector supports authentication with [SAP Secure Network Communications (SNC)](https://help.sap.com/viewer/e73bba71770e4c0ca5fb2a3c17e8e229/7.31.25/en-US/e656f466e99a11d1a5b00000e835363f.html).

    You can use SNC for SAP NetWeaver single sign-on (SSO) or for security capabilities from external products. If you use SNC, review the [SNC prerequisites](#snc-prerequisites) and the [SNC prerequisites for the ISE connector](#snc-prerequisites-ise).

## Connector technical reference

The SAP connector has different versions, based on [logic app type and host environment](../logic-apps/logic-apps-overview.md#resource-environment-differences).

| Logic app | Environment | Connector version |
|-----------|-------------|-------------------|
| **Consumption** | Multi-tenant Azure Logic Apps | Managed connector, which appears in the designer under the **Enterprise** label. For more information, review the following documentation: <br><br>- [SAP managed connector reference](/connectors/sap/) <br>- [Managed connectors in Azure Logic Apps](../connectors/managed.md) |
| **Consumption** | Integration service environment (ISE) | Managed connector, which appears in the designer under the **Enterprise** label, and the ISE-native version, which appears in the designer with the **ISE** label and has different message limits than the managed connector. <br><br>**Note**: Make sure to use the ISE-native version, not the managed version. <br><br>For more information, review the following documentation: <br><br>- [SAP managed connector reference](/connectors/sap/) <br>- [ISE message limits](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) <br>- [Managed connectors in Azure Logic Apps](../connectors/managed.md) |
| **Standard** | Single-tenant Azure Logic Apps and App Service Environment v3 (Windows plans only) | Managed connector, which appears in the designer under the **Azure** label and built-in connector (preview), which appears in the designer under the **Built-in** label and is [service provider based](../logic-apps/custom-connector-overview.md#service-provider-interface-implementation). The built-in connector can directly access Azure virtual networks with a connection string without an on-premises data gateway. For more information, review the following documentation: <br><br>- [SAP managed connector reference](/connectors/sap/) <br>- [SAP built-in connector reference](/azure/logic-apps/connectors/built-in/reference/sap/) <br><br>- [Managed connectors in Azure Logic Apps](../connectors/managed.md) <br>- [Built-in connectors in Azure Logic Apps](../connectors/built-in.md) |

<a name"connector-parameters"></a>

### Connector parameters

Along with simple string and number inputs, the SAP connector accepts the following table parameters (`Type=ITAB` inputs):

* Table direction parameters, both input and output, for older SAP releases.

* Changing parameters, which replace the table direction parameters for newer SAP releases.

* Hierarchical table parameters.

## Known issues and limitations

For the SAP managed connector, the following known issues and limitations apply:

* The SAP connector currently doesn't support SAP router strings. The on-premises data gateway must exist on the same LAN as the SAP system that you want to connect.

* In general, the SAP trigger doesn't support data gateway clusters. In some failover cases, the data gateway node that communicates with the SAP system might differ from the active node, which results in unexpected behavior.

  * For send message scenarios, data gateway clusters in failover mode are supported.

  * Stateful [SAP actions](/connectors/sap/#actions) don't support data gateway clusters in load-balancing mode. Stateful communications must remain on the same data gateway cluster node. Either use the data gateway in non-cluster mode or in a cluster that's set up for failover only. For example, these actions include the following:

    * All actions that specify a **Session ID** value
    * **\[BAPI] Commit transaction**
    * **\[BAPI] Rollback transaction**
    * **\[BAPI - RFC] Close stateful session**
    * **\[BAPI - RFC] Create stateful session**

* In the action named **\[BAPI] Call method in SAP**, the auto-commit feature won't commit the BAPI changes if at least one warning exists in the **CallBapiResponse** object returned by the action. To commit BAPI changes despite any warnings, follow these steps:

  1. Create a session explicitly using the action named **\[BAPI - RFC] Create stateful session**.
  1. In the action named **\[BAPI] Call method in SAP**, disable the auto-commit feature.
  1. Call the action named **\[BAPI] Commit transaction** instead.

## Prerequisites

* An Azure account and subscription. If you don't have an Azure subscription yet, [sign up for a free Azure account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* The [SAP Application server](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) or [SAP Message server](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) that you want to access from Azure Logic Apps.

  * Set up your SAP server and user account to allow using RFC.

    For more information, which includes the supported user account types and the minimum required authorization for each action type (RFC, BAPI, IDOC), review the following SAP note: [460089 - Minimum authorization profiles for external RFC programs](https://launchpad.support.sap.com/#/notes/460089).

  * Your SAP user account needs access to the `RFC_METADATA` function group and the respective function modules for the following operations:

    | Operations | Access to function modules |
    |------------|----------------------------|
    | RFC actions | `RFC_GROUP_SEARCH` and `DD_LANGU_TO_ISOLA` |
    | BAPI actions | `BAPI_TRANSACTION_COMMIT`, `BAPI_TRANSACTION_ROLLBACK`, `RPY_BOR_TREE_INIT`, `SWO_QUERY_METHODS`, and `SWO_QUERY_API_METHODS` |
    | IDOC actions | `IDOCTYPES_LIST_WITH_MESSAGES`, `IDOCTYPES_FOR_MESTYPE_READ`, `INBOUND_IDOCS_FOR_TID`, `OUTBOUND_IDOCS_FOR_TID`, `GET_STATUS_FROM_IDOCNR`, and `IDOC_RECORD_READ` |
    | [**Read SAP table**](/connectors/sap/#read-sap-table) action | Either `RFC BBP_RFC_READ_TABLE` or `RFC_READ_TABLE` |
    | Grant strict minimum access to SAP server for your SAP connection | `RFC_METADATA_GET` and `RFC_METADATA_GET_TIMESTAMP` |

* The logic app workflow from where you want to access your SAP server.

  * For a Consumption workflow in multi-tenant Azure Logic Apps, see [Multi-tenant prerequisites](#multi-tenant-prerequisites).

  * For a Standard workflow in single-tenant Azure Logic Apps, see [Single-tenant prerequisites](#single-tenant-prerequisites).

  * For a Consumption workflow in a Premium-level [integration service environment (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), see [ISE prerequisites](#ise-prerequisites).

    > [!NOTE]
    >
    > When you use a Premium-level ISE, use the ISE-native SAP connector, not the SAP managed connector, 
    > which doesn't natively run in an ISE. For more information, review the [ISE prerequisites](#ise-prerequisites).

* To use the **When a message is received from SAP** trigger, complete the following tasks:

  * Set up your SAP gateway security permissions or Access Control List (ACL). In the **Gateway Monitor** (T-Code SMGW) dialog box, which shows the **secinfo** and **reginfo** files, open the **Goto** menu, and select **Expert Functions** > **External Security** > **Maintenance of ACL Files**.

    The following permission setting is required:

    `P TP=LOGICAPP HOST=<on-premises-gateway-server-IP-address> ACCESS=*`

    This line has the following format:

    `P TP=<trading-partner-identifier-(program-name)-or-*-for-all-partners> HOST=<comma-separated-list-with-external-host-IP-or-network-names-that-can-register-the-program> ACCESS=<*-for-all-permissions-or-a-comma-separated-list-of-permissions>`

    If you don't configure the SAP gateway security permissions, you might receive the following error:

    **Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <*host-name*> not allowed**

    For more information, review [SAP Note 1850230 - GW: "Registration of tp &lt;program ID&gt; not allowed"](https://userapps.support.sap.com/sap/support/knowledge/en/1850230).

  * Set up your SAP gateway security logging to help find Access Control List (ACL) issues. For more information, review the [SAP help topic for setting up gateway logging](https://help.sap.com/viewer/62b4de4187cb43668d15dac48fc00732/7.31.25/en-US/48b2a710ca1c3079e10000000a42189b.html).

  * In the **Configuration of RFC Connections** (T-Code SM59) dialog box, create an RFC connection with the **TCP/IP** type. Make sure that the **Activation Type** is set to **Registered Server Program**. Set the RFC connection's **Communication Type with Target System** value to **Unicode**.

  * If you use this SAP trigger with the **IDOC Format** parameter set to **FlatFile** along with the [Flat File Decode action](logic-apps-enterprise-integration-flatfile.md), you have to use the `early_terminate_optional_fields` property in your flat file schema by setting the value to `true`.

    This requirement is necessary because the flat file IDoc data record that's sent by SAP on the tRFC call `IDOC_INBOUND_ASYNCHRONOUS` isn't padded to the full SDATA field length. Azure Logic Apps provides the flat file IDoc original data without padding as received from SAP. Also, when you combine this SAP trigger with the **Flat File Decode** action, the schema that's provided to the action must match.

  > [!NOTE]
  >
  > This SAP trigger uses the same URI location to both renew and unsubscribe from a webhook subscription. The renewal 
  > operation uses the HTTP `PATCH` method, while the unsubscribe operation uses the HTTP `DELETE` method. This behavior 
  > might make a renewal operation appear as an unsubscribe operation in your trigger's history, but the operation is 
  > still a renewal because the trigger uses `PATCH` as the HTTP method, not `DELETE`.

* The message content to send to your SAP server, such as a sample IDoc file. This content must be in XML format and include the namespace of the [SAP action](/connectors/sap/#actions) that you want to use. You can [send IDocs with a flat file schema by wrapping them in an XML envelope](#send-flat-file-idocs).

### Network prerequisites

The SAP system requires network connectivity from the host of the SAP .NET Connector (NCo) library. The multi-tenant host of the SAP .NET Connector (NCo) library is the on-premises data gateway. If you use an on-premises data gateway cluster, all nodes of the cluster require network connectivity to the SAP system. The ISE host of the SAP .NET Connector (NCo) library is within the ISE virtual network.

The SAP system-required network connectivity includes the following servers and services:

* SAP Application Server, Dispatcher service (for all Logon types)

  Your SAP system can include multiple SAP Application Servers. The host of the SAP .NET Connector (NCo) library requires access to each server and their services.

* SAP Message Server, Message service (for Logon type Group)

  The Message Server and service will redirect to one or more Application Server's Dispatcher services. The host of the SAP .NET Connector (NCo) library requires access to each server and their services.

* SAP Gateway Server, Gateway service

* SAP Gateway Server, Gateway secured service

  The SAP system-required network connectivity also includes this server and service to use with Secure Network Communications (SNC).

Redirection of requests from Application Server, Dispatcher service to Gateway Server, Gateway service automatically happens within the SAP .NET Connector (NCo) library. This redirection occurs even if only the Application Server, Dispatcher service information is provided in the connection parameters.

If you're using a load balancer in front of your SAP system, you must redirect all the services to their respective servers. 
For more information about SAP services and ports, review the [TCP/IP Ports of All SAP Products](https://help.sap.com/viewer/ports).

> [!NOTE]
>
> Make sure you enabled network connectivity from the host of the SAP .NET Connector (NCo) library and that the required 
> ports are open on firewalls and network security groups. Otherwise, you get errors such as **partner not reached** 
> from the **NI (network interface)** component and error text such as **WSAECONNREFUSED: Connection refused**.

### Azure Logic Apps environment prerequisites

#### [Multi-tenant](#tab/multi-tenant)

<a name="multi-tenant-prerequisites"></a>

For a Consumption workflow in multi-tenant Azure Logic Apps, the SAP managed connector integrates with SAP systems through an [on-premises data gateway](logic-apps-gateway-connection.md). For example, in scenarios where your workflow sends a message to the SAP system, the data gateway acts as an RFC client and forwards the requests received from your workflow to SAP. Likewise, in scenarios where your workflow receives a message from SAP, the data gateway acts as an RFC server that receives requests from SAP and forwards them to your workflow.

> [!NOTE]
>
> If your workflow uses the deprecated SAP connector, **SAP Application Server** or **SAP Message Server**, 
> you must [migrate to the current SAP connector](#migrate-to-current-connector) before you can connect to your SAP server.

1. On a host computer or virtual machine that exists in the same virtual network as the SAP system to which you're connecting, [download and install the on-premises data gateway](logic-apps-gateway-install.md).

   The data gateway helps you securely access on-premises data and resources. Make sure to use a supported version of the gateway. If you experience an issue with your gateway, try [upgrading to the latest version](https://aka.ms/on-premises-data-gateway-installer), which might include updates to resolve your problem.

1. In the Azure portal, [create an Azure gateway resource](logic-apps-gateway-connection.md#create-azure-gateway-resource) for your on-premises data gateway installation.

1. On the same local computer as your on-premises data gateway installation, [download and install the latest SAP client library](#sap-client-library-prerequisites).

1. For the host computer with your on-premises data gateway installation, configure the network host names and service names resolution.

   * To use the host names or service names for connections from Azure Logic Apps, you have to set up name resolution for each SAP Application, Message, and Gateway server along with their services:

     * In the **%windir%\System32\drivers\etc\hosts** file or in the DNS server that's available to the host computer for your on-premises data gateway installation, set up the network host name resolution.

     * In the **%windir%\System32\drivers\etc\services** file, set up the service name resolution.

   * If you don't intend to use network host names or service names for the connection, you can use host IP addresses and service port numbers instead.

   * If you don't have a DNS entry for your SAP system, the following example shows a sample entry for the hosts file:

     ```text
     10.0.1.9           sapserver                   # SAP single-instance system host IP by simple computer name
     10.0.1.9           sapserver.contoso.com       # SAP single-instance system host IP by fully qualified DNS name
     ```

     The following list shows a sample set of entries for the services files:

     ```text
     sapdp00            3200/tcp              # SAP system instance 00 dialog (application) service port
     sapgw00            3300/tcp              # SAP system instance 00 gateway service port
     sapmsDV6           3601/tcp              # SAP system ID DV6 message service port
     ```

#### [Single-tenant](#tab/single-tenant)

<a name="single-tenant-prerequisites"></a>

For a Standard workflow in single-tenant Azure Logic Apps, use the SAP *built-in* connector to directly access resources that are protected by an Azure virtual network. You can also use other built-in connectors that let workflows directly access on-premises resources without having to use the on-premises data gateway.

1. In the [Azure portal](https://portal.azure.com), open your Standard logic app resource.

1. On the logic app menu, under **Workflows**, select **Assemblies**.

1. On the **Assemblies** page toolbar, select **Add**.

1. After the **Add Assembly** pane opens, for **Assembly Type**, select **Client/SDK Assembly (.NET Framework)**.

1. Under **Upload Files**, add all the required SAP private assembly files. When you're ready, select **Upload Files**.

   * **libicudecnumber.dll**
   * **sapcrypto.dll**
   * **sapgenpse.exe**
   * **sapnco.dll**
   * **sapnco_utils.exe**
   * **slcryptokernal.dll**

   If the assembly file is 4 MB or smaller, you can either browse and select or drag and drop the file. For files larger than 4 MB, follow these steps instead:

   1. On the logic app menu, under **Development Tools**, select **Advanced Tools**.

   1. On the **Advanced Tools** page, select **Go**.

   1. On the **Kudu** toolbar, from the **Debug console** menu, select **CMD**.

   1. Open the following folders: **site** > **wwwroot**

   1. On the folder structure toolbar, select the plus (**+**) sign, and then select **New folder**.

   1. Create the following folder and subfolders: **lib** > **builtinOperationSdks** > **net472**

   1. In the **net472** folder, upload the assembly files larger than 4 MB.

#### [ISE](#tab/ise)

<a name="ise-prerequisites"></a>

For a Consumption workflow in an ISE, the ISE provides access to resources that are protected by an Azure virtual network and offers other ISE-native connectors that let workflows directly access on-premises resources without having to use the on-premises data gateway.

1. If you don't already have an Azure Storage account with a blob container, create a container using either the [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) or [Azure Storage Explorer](../storage/blobs/quickstart-storage-explorer.md).

1. On your local computer, [download and install the latest SAP client library](#sap-client-library-prerequisites). You should have the following assembly (.dll) files:

   * **libicudecnumber.dll**
   * **rscp4n.dll**
   * **sapnco.dll**
   * **sapnco_utils.dll**

1. From the root folder, create a .zip file that includes these assembly files. Upload the package to your blob container in Azure Storage.

   > [!NOTE]
   >
   > Don't use a subfolder inside the .zip file. Only assemblies in the archive's root folder 
   > are deployed with the SAP connector in your ISE.
   >
   > If you use SNC, also include the SNC assemblies and binaries in the same .zip file at the root. 
   > For more information, review the [SNC prerequisites for ISE](#snc-prerequisites-ise).

1. In either the Azure portal or Azure Storage Explorer, browse to the container location where you uploaded the .zip file.

1. Copy the URL for the container location. Make sure to include the Shared Access Signature (SAS) token, so the SAS token is authorized. Otherwise, deployment for the SAP ISE connector fails.

1. In your ISE, install and deploy the SAP connector. For more information, review [Add ISE connectors](add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

   1. In the [Azure portal](https://portal.azure.com), find and open your ISE.

   1. On the ISE menu, select **Managed connectors** &gt; **Add**. From the connectors list, find and select **SAP**.

   1. On the **Add a new managed connector** pane, in the **SAP package** box, paste the URL for the .zip file that has the SAP assemblies. Again, make sure to include the SAS token.

   1. Select **Create** to finish creating your ISE connector.

1. If your SAP instance and ISE are in different virtual networks, you also need to [peer those networks](../virtual-network/tutorial-connect-virtual-networks-portal.md) so they're connected. Review the [SNC prerequisites for ISE](#snc-prerequisites-ise).

1. Get the IP addresses for the SAP Application, Message, and Gateway servers that you plan to use for connecting from your workflow. Network name resolution isn't available for SAP connections in an ISE.

1. Get the port numbers for the SAP Application, Message, and Gateway services that you plan you'll use for connecting from your workflow. Service name resolution isn't available for SAP connections in an ISE.

---

### SAP client library prerequisites

The following list describes the prerequisites for the SAP client library that you're using with the SAP connector:

* Make sure that you install the latest 64-bit version, [SAP Connector (NCo 3.0) for Microsoft .NET 3.0.25.0 compiled with .NET Framework 4.0  - Windows 64-bit (x64)](https://support.sap.com/en/product/connectors/msnet.html). The data gateway runs only on 64-bit systems. Installing the unsupported 32-bit version results in a **"bad image"** error.

  Earlier versions of SAP NCo might experience the following issues:

  * When more than one IDoc message is sent at the same time, this condition blocks all later messages that are sent to the SAP destination, causing messages to time out.

  * Session activation might fail due to a leaked session. This condition might block calls sent by SAP to the logic app workflow trigger.

  * The on-premises data gateway (June 2021 release) depends on the `SAP.Middleware.Connector.RfcConfigParameters.Dispose()` method in SAP NCo to free up resources.

  * After you upgrade the SAP server environment, you get the following exception message: **"The only destination &lt;some-GUID&gt; available failed when retrieving metadata from &lt;SAP-system-ID&gt; -- see log for details"**.

* From the client library's default installation folder, copy the assembly (.dll) files to another location, based on your scenario as follows. Or, optionally, when you install the SAP client library, select **Global Assembly Cache registration**.

  * For a Consumption workflow that runs in multi-tenant Azure Logic Apps and uses your on-premises data gateway, copy the assembly (.dll) files to the on-premises data gateway installation folder, for example, **C:\Program Files\On-Premises Data Gateway**.

    Make sure that you copy the assembly files to the data gateway's *installation folder*. Otherwise, your SAP connection might fail with the error message, **Please check your account info and/or permissions and try again**. You can troubleshoot further issues using the [.NET assembly binding log viewer](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer). This tool lets you check that your assembly files are in the correct location.

  * For a Consumption workflow in an ISE, follow the [ISE prerequisites](#ise-prerequisites) instead.

The following relationships exist between the SAP client library, the .NET Framework, the .NET runtime, and the data gateway:

* Both the Microsoft SAP Adapter and the gateway host service use .NET Framework 4.7.2.

* The SAP NCo for .NET Framework 4.0 works with processes that use .NET runtime 4.0 to 4.8.

* The SAP NCo for .NET Framework 2.0 works with processes that use .NET runtime 2.0 to 3.5, but no longer works with the latest gateway.

<a name="snc-prerequisites"></a>

### SNC prerequisites

#### [Multi-tenant](#tab/multi-tenant)

For Consumption workflows in multi-tenant Azure Logic Apps that use the on-premises data gateway with optional SNC , you must also configure the following settings. For workflows that run in an ISE, review the [SNC prerequisites for ISE](#snc-prerequisites-ise).

* Make sure the on-premises data gateway is installed on a computer that's in the same network as your SAP system.

* Make sure that your SNC library version and its dependencies are compatible with your SAP environment. To troubleshoot any library compatibility issues, you can use your on-premises data gateway and data gateway logs.

* For the SAPGENPSE utility, you must specifically use **sapgenpse.exe**.

* If you provide a Personal Security Environment (PSE) with your connection, you don't need to copy and set up the PSE and SECUDIR for your on-premises data gateway.

* If you enable SNC through an external security product, such as [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos, or NTLM, make sure that the SNC library exists on the same computer as your data gateway installation. For this task, copy the SNC library's binary files to the same folder as the data gateway installation on your local computer. For example, **C:\Program Files\On-Premises Data Gateway**.

  > [!NOTE]
  >
  > On the computer with the data gateway installation and SNC library, don't set the 
  > environment variables for **SNC_LIB** and **SNC_LIB_64**. Otherwise, these variables 
  > take precedence over the SNC library value passed through the connector.

* To use SNC with Single Sign-On (SSO), make sure the data gateway service is running as a user who is mapped to an SAP user. To change the default account for the gateway service account, select **Change account**, and enter the user credentials.

  ![Screenshot that shows the on-premises data gateway installer and Service Settings page with button to change gateway service account selected.](./media/logic-apps-using-sap-connector/gateway-account.png)

For more information about enabling SNC, review [Enable Secure Network Communications (SNC)](#enable-secure-network-communications).

#### [Single-tenant](#tab/single-tenant)

For more information about enabling SNC, review [Enable Secure Network Communications (SNC)](#enable-secure-network-communications).

#### [ISE](#tab/ise)

<a name="snc-prerequisites-ise"></a>

The ISE-versioned SAP connector supports SNC X.509. If you previously used the SAP connector without SNC, you can enable SNC for ISE-native SAP connections.

Before you redeploy an SAP connector to use SNC, or if you deployed the SAP connector without the SNC or SAPGENPSE libraries, you must delete all previously existing SAP connections and then the SAP connector. Multiple logic app workflows can use the same SAP connection. So, make sure that you delete any previously existing SAP connections from all logic app workflows in your ISE.

After you delete the SAP connections, you must delete the SAP connector from your ISE. You can still keep the logic app workflows that use this connector. After you redeploy the connector, you can then authenticate the new connection in your workflows' SAP operations.

1. To delete existing SAP connections, follow either path:

   * In the [Azure portal](https://portal.azure.com), open each logic app resource and workflow to delete the SAP connections.

     1. Open your logic app workflow in the designer.

     1. On your logic app menu, under **Development Tools**, select **API connections**.

     1. On the **API connections** page, select your SAP connection.

     1. On the connection's page menu, select **Delete**.

     1. Accept the confirmation prompt to delete the connection.

     1. Wait for the portal notification that the connection has been deleted.

   * In the [Azure portal](https://portal.azure.com), open your ISE resource to delete the SAP connections.

      1. On your ISE menu, under **Settings**, select **API connections**.

      1. On the **API connections** page, select your SAP connection.

      1. On the connection's page menu, select **Delete**.

      1. Accept the confirmation prompt to delete the connection.

      1. Wait for the portal notification that the connection has been deleted.

1. Delete the SAP connector from your ISE. You must delete all connections to this connector in all your logic app workflows before you can delete the connector. If you haven't already deleted all connections, review the previous steps.

   1. In the [Azure portal](https://portal.azure.com), open your ISE resource.

   1. On your ISE menu, under **Settings**, select **Managed connectors**.

   1. On the **Managed connectors** page, select the checkbox for the SAP connector.

   1. On the toolbar, select **Delete**.

   1. Accept the confirmation prompt to delete the connector.

   1. Wait for the portal notification that the connector has been deleted.

1. Deploy or redeploy the SAP connector in your ISE.

   1. Prepare a new zip archive file to use in your SAP connector deployment. You must include the SNC library and the SAPGENPSE utility.

      * You must use the 64-bit SNC library. There's no 32-bit support.

      * Your SNC library and dependencies must be compatible with your SAP environment. For how to check compatibility, the [ISE prerequisites](#ise-prerequisites).

   1. Copy all SNC, SAPGENPSE, and NCo libraries to the root folder of your zip archive. Don't put these binaries in a subfolder.

   1. For your new zip archive, follow the deployment steps in [ISE prerequisites](#ise-prerequisites).

1. For each workflow that uses the ISE-native SAP connector, [create a new SAP connection that enables SNC](#enable-secure-network-communications).

##### Certificate rotation

1. For all connections that use SAP ISE X.509 in your ISE, update the base64-encoded binary PSE.

1. In your copy of the PSE, import the new certificates.

1. Encode the PSE file as a base64-encoded binary.

1. Edit your SAP connection information, and save the new PSE file there.

   The connector detects the PSE change and updates its own copy during the next connection request.

##### Convert a binary PSE file into base64-encoded format

1. Use a PowerShell script, for example:

   ```powershell
   Param ([Parameter(Mandatory=$true)][string]$psePath, [string]$base64OutputPath)
   $base64String = [convert]::ToBase64String((Get-Content -path $psePath -Encoding byte))
   if ($base64OutputPath -eq $null)
   {
       Write-Output $base64String
   }
   else
   {
       Set-Content -Path $base64OutputPath -Value $base64String
       Write-Output "Output written to $base64OutputPath"
   } 
   ```

1. Save the script as a **pseConvert.ps1** file, and then invoke the script, for example:

   ```output
   .\pseConvert.ps1 -psePath "C:\Temp\SECUDIR\request.pse" -base64OutputPath "connectionInput.txt"
   Output written to connectionInput.txt 
   ```

   If you don't provide the output path parameter, the script's output to the console contains line breaks. Remove the line breaks in the base 64-encoded string for the connection input parameter.

---

<a name="enable-secure-network-communications"></a>

### Enable Secure Network Communications (SNC)

#### [Multi-tenant](#tab/multi-tenant)

For a Consumption workflow that runs in multi-tenant Azure Logic Apps, you can enable SNC for authentication, which applies only when you use the data gateway. Before you start, make sure that you met all the necessary [prerequisites](logic-apps-using-sap-connector.md?tabs=multi-tenant#prerequisites) and [SNC prerequisites](logic-apps-using-sap-connector.md?tabs=multi-tenant#snc-prerequisites).

1. In the [Azure portal](https://portal.azure.com), open your Consumption logic app and workflow in the designer.

1. Add or edit an SAP managed connector operation.

1. In the SAP connection information box, provide the following [required information](/connectors/sap/#default-connection). The **Authentication Type** that you select changes the available options.

   ![Screenshot showing SAP connection settings for Consumption.](media\logic-apps-using-sap-connector\sap-connection-consumption.png)

   > [!NOTE]
   >
   > The **SAP Username** and **SAP Password** fields are optional. If you don't provide a username 
   > and password, the connector uses the client certificate provided in a later step for authentication.

1. To enable SNC, in the SAP connection information box, provide the following required information instead:

   ![Screenshot showing SAP connection settings for SNC enabled for Consumption.](./media/logic-apps-using-sap-connector/sap-connection-snc-consumption.png)

   | Parameter | Description |
   |-----------| ------------|
   | **Use SNC** | Select the checkbox. |
   | **SNC Library** | Enter one of the following values: <br><br>- The name for your SNC library, for example, **sapsnc.dll** <br>- The relative path to the NCo installation location, for example, **.\security\sapsnc.dll** <br>- The absolute path to the NCo installation location, for example, **c:\security\sapsnc.dll** |
   | **SNC SSO** | Select either **Logon using the SNC identity** or **Logon with the username/password provided on RFC level**. <br><br>Typically, the SNC identity is used to authenticate the caller. You can choose to authenticate with a username and password instead, but this parameter value is still encrypted. |
   | **SNC My Name** | In most cases, you can omit this value. The installed SNC solution usually knows its own SNC name. In the case where your solution supports multiple identities, you might have to specify the identity to use for this particular destination or server. |
   | **SNC Partner Name** | Enter the name for the backend SNC, for example, **p:CN=DV3, OU=LA, O=MS, C=US**. |
   | **SNC Quality of Protection** | Select the quality of service to use for SNC communication with this particular destination or server. The default value is defined by the backend system. The maximum value is defined by the security product used for SNC. |

1. To finish creating your connection, select **Create**.

   If the parameters are correct, the connection is created. If there's a problem with the parameters, the connection creation dialog displays an error message. To troubleshoot connection parameter issues, you can use the on-premises data gateway installation and the gateway's local logs.

#### [Single-tenant](#tab/single-tenant)

For a Standard workflow that runs in single-tenant Azure Logic Apps, you can enable SNC for authentication. Before you start, make sure that you met all the necessary [prerequisites](logic-apps-using-sap-connector.md?tabs=single-tenant#prerequisites) and [SNC prerequisites for single-tenant](logic-apps-using-sap-connector.md?tabs=single-tenant#snc-prerequisites).

1. In the [Azure portal](https://portal.azure.com), open your Standard logic app and workflow in the designer.

1. Add or edit an SAP *built-in* connector operation.

1. In the SAP connection information box, provide the following [required information](/azure/logic-apps/connectors/built-in/reference/sap/#authentication). The **Authentication Type** that you select changes the available options.

   ![Screenshot showing SAP built-in connection settings for Standard workflow with Basic authentication.](media\logic-apps-using-sap-connector\sap-connection-standard.png)

1. To enable SNC, in the SAP connection information box, provide the [required information instead](/azure/logic-apps/connectors/built-in/reference/sap/#authentication).

   ![Screenshot showing SAP built-in connection settings for Standard workflow with SNC enabled.](media\logic-apps-using-sap-connector\sap-connection-snc-standard.png)

   | Parameter | Description |
   |-----------| ------------|
   | **Authentication Type** | Select **Logon Using SNC**. |
   | **SNC Partner Name** | Enter the name for the backend SNC, for example, **p:CN=DV3, OU=LA, O=MS, C=US**. |
   | **SNC Quality of Protection** | Select the quality of service to use for SNC communication with this particular destination or server. The default value is defined by the backend system. The maximum value is defined by the security product used for SNC. |
   | **SNC Type** | Select the SNC authentication to use. |
   | **SNC Certificate** | Enter your SNC client's public certificate in base64-encoded format. <br><br>**Note**: - Don't include the PEM header or footer. <br><br>- Don't enter the private certificate here because the PSE might contain multiple private certificates. However, this **SNC Certificate** parameter identifies the certificates that this connection must use. For more information, review the next parameter. |
   | **PSE** | Enter your SNC PSE as a base64-encoded binary. <br><br>- The PSE must contain the private client certificate where the thumbprint matches the public client certificate that you provided in the previous step. <br><br>- Although the PSE might contain multiple client certificates. to use different client certificates, create separate workflow apps instead. <br><br>- The PSE must have no PIN. If necessary, set the PIN to empty using the SAPGENPSE utility. <br><br>- If you're using more than one SNC client certificate for your ISE, you must provide the same PSE for all connections. The PSE must contain the client private certificate for each and all the connections. You must set the client public certificate parameter to match the specific private certificate for each connection used in your ISE. |

1. To finish creating your connection, select **Create**.

#### [ISE](#tab/ise)

For a Consumption workflow that runs in an ISE, you can enable SNC for authentication. Before you start, make sure that you met all the necessary [prerequisites](#prerequisites) and [SNC prerequisites for ISE](#snc-prerequisites-ise).

1. In the [Azure portal](https://portal.azure.com), open your ISE resource and logic app workflow in the designer.

1. Add or edit an *ISE-versioned* SAP connector operation. Make sure that the SAP connector operation displays the **ISE** label.

1. In the SAP connection information box, provide the following [required information](/connectors/sap/#default-connection). The **Authentication Type** that you select changes the available options.

   ![Screenshot showing SAP connection settings for ISE.](media\logic-apps-using-sap-connector\sap-connection-ise.png)

   > [!NOTE]
   >
   > The **SAP Username** and **SAP Password** fields are optional. If you don't provide a username 
   > and password, the connector uses the client certificate provided in a later step for authentication.

1. To enable SNC, in the SAP connection information box, provide the following required information instead:

   ![Screenshot showing SAP connection settings with SNC enabled for ISE.](./media\logic-apps-using-sap-connector\sap-connection-snc-ise.png)

   | Parameter | Description |
   |-----------|-------------|
   | **Use SNC** | Select the checkbox. |
   | **SNC Library** | Enter the name for your SNC library, for example, **sapcrypto.dll**. |
   | **SNC Partner Name** | Enter the name for the backend SNC, for example, **p:CN=DV3, OU=LA, O=MS, C=US**. |
   | **SNC My Name** and **SNC Quality of Protection** | Optional: Enter these values, as necessary. |
   | **SNC Certificate** | Enter your SNC client's public certificate in base64-encoded format with the following guidance: <br><br>- Don't include the PEM header or footer. <br><br>- Don't enter the private certificate here because the PSE might contain multiple private certificates. However, this **SNC Certificate** parameter identifies the certificates that this connection must use. For more information, review the next parameter. |
   | **PSE** | Enter your SNC PSE as a base64-encoded binary with the following guidance: <br><br>- The PSE must contain the private client certificate where the thumbprint matches the public client certificate that you provided in the previous step. <br><br>- Although the PSE might contain multiple client certificates. to use different client certificates, create separate workflow apps instead. <br><br>- The PSE must have no PIN. If necessary, set the PIN to empty using the SAPGENPSE utility. <br><br>- If you're using more than one SNC client certificate for your ISE, you must provide the same PSE for all connections. The PSE must contain the client private certificate for each and all the connections. You must set the client public certificate parameter to match the specific private certificate for each connection used in your ISE. |

1. To finish creating your connection, select **Create**.

   If the parameters are correct, the connection is created. If there's a problem with the parameters, the connection creation dialog displays an error message. To troubleshoot connection parameter issues, you can use an on-premises data gateway and the gateway's local logs.

---

### Migrate to current connector

In Consumption workflows, the **SAP Application Server** and **SAP Message Server** connectors were deprecated February 29, 2020. To migrate to the current SAP connector, follow these steps:

1. Update your [on-premises data gateway](https://www.microsoft.com/download/details.aspx?id=53127) to the current version. For more information, review [Install an on-premises data gateway for Azure Logic Apps](logic-apps-gateway-install.md).

1. In your logic app workflow that uses the deprecated SAP connector, delete the **Send to SAP** action.

1. Find the current SAP connector, and add the action named **Send message to SAP**.

1. Reconnect to your SAP system using the new action.

1. Save your workflow. On the designer toolbar, select **Save**.

## Safe typing

By default, when you create your SAP connection, strong typing is used to check for invalid values by performing XML validation against the schema. This behavior can help you detect issues earlier. The **Safe Typing** option is available for backward compatibility and only checks the string length. If you choose **Safe Typing**, the DATS type and TIMS type in SAP are treated as strings rather than as their XML equivalents, `xs:date` and `xs:time`, where `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. Safe typing affects the behavior for all schema generation, the send message for both the "been sent" payload and the "been received" response, and the trigger.

When strong typing is used (**Safe Typing** isn't enabled), the schema maps the DATS and TIMS types to more straightforward XML types:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

When you send messages using strong typing, the DATS and TIMS response complies with the matching XML type format:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

When **Safe Typing** is enabled, the schema maps the DATS and TIMS types to XML string fields with length restrictions only, for example:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

When messages are sent with **Safe Typing** enabled, the DATS and TIMS response looks like this example:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## Send SAP telemetry for on-premises data gateway to Azure Application Insights

With the August 2021 update for the on-premises data gateway, SAP connector operations can send telemetry data from the SAP client library and traces from the Microsoft SAP Adapter to [Application Insights](../azure-monitor/app/app-insights-overview.md), which is a capability in Azure Monitor. This telemetry primarily includes the following data:

* Metrics and traces based on SAP NCo metrics and monitors.

* Traces from Microsoft SAP Adapter.

### Metrics and traces from SAP client library

*Metrics* are numeric values that might or might not vary over a time period, based on the usage and availability of resources on the on-premises data gateway. You can use these metrics to better understand system health and to create alerts about the following activities:

* Whether system health is declining.

* Unusual events.

* Heavy load on your system.

This information is sent to the Application Insights table, `customMetrics`. By default, metrics are sent at 30-second intervals.

SAP NCo metrics and traces are based on SAP NCo metrics, specifically the following NCo classes:

* RfcDestinationMonitor.

* RfcConnectionMonitor.

* RfcServerMonitor.

* RfcRepositoryMonitor.

For more information about the metrics that each class provides, review the [SAP NCo documentation (sign-in required)](https://support.sap.com/en/product/connectors/msnet.html#section_512604546).

*Traces* include text information that is used with metrics. This information is sent to the Application Insights table named `traces`. By default, traces are sent at 10-minute intervals.

### Set up SAP telemetry for Application Insights

Before you can send SAP telemetry for your gateway installation to Application Insights, you need to have created and set up your Application Insights resource. For more information, review the following documentation:

* [Create an Application Insights resource (classic)](/previous-versions/azure/azure-monitor/app/create-new-resource)

* [Workspace-based Application Insights resources](../azure-monitor/app/create-workspace-resource.md)

To enable sending SAP telemetry to Application insights, follow these steps:

1. Download the NuGet package for **Microsoft.ApplicationInsights.EventSourceListener.dll** from this location: [https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0).

1. Add the downloaded file to your on-premises data gateway installation directory, for example, "C:\Program Files\On-Premises Data Gateway".

1. In your on-premises data gateway installation directory, check that the **Microsoft.ApplicationInsights.dll** file has the same version number as the **Microsoft.ApplicationInsights.EventSourceListener.dll** file that you added. The gateway currently uses version 2.14.0.

1. In the **ApplicationInsights.config** file, add your [Application Insights instrumentation key](../azure-monitor/app/sdk-connection-string.md) by uncommenting the line with the `<InstrumentationKey></Instrumentation>` element. Replace the placeholder, *your-Application-Insights-instrumentation-key*, with your key, for example:

      ```xml
      <?xml version="1.0" encoding="utf-8"?>
      <ApplicationInsights schemaVersion="2014-05-30" xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
         <!-- Uncomment this element and insert your Application Insights key to receive ETW telemetry about your gateway <InstrumentationKey>*your-instrumentation-key-placeholder*</InstrumentationKey> -->
         <TelemetryModules>
            <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
               <IsHeartbeatEnabled>false</IsHeartbeatEnabled>
            </Add>
            <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
               <Sources>
                  <Add Name="Microsoft-LobAdapter" Level="Verbose" />
               </Sources>
            </Add>
         </TelemetryModules>
      </ApplicationInsights>
      ```

1. In the **ApplicationInsights.config** file, you can change the required traces `Level` value for your SAP connector operations, per your requirements, for example:

   ```xml
   <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
         <Add Name="Microsoft-LobAdapter" Level="Verbose" />
      </Sources>
   </Add>
   ```

   For more information, review the following documentation:

   * `Level` values: [EventLevel Enum](/dotnet/api/system.diagnostics.tracing.eventlevel)

   * [EventSource tracking](../azure-monitor/app/configuration-with-applicationinsights-config.md#eventsource-tracking)

   * [EventSource events](../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events)

1. After you apply your changes, restart the on-premises data gateway service.

### Review metrics in Application Insights

After your SAP operations run in your logic app workflow, you can review the telemetry that was sent to Application Insights.

1. In the Azure portal, open your Application Insights resource.

1. On the resource menu, under **Monitoring**, select **Logs**.

   The following screenshot shows the Azure portal with Application Insights, which is open to the **Logs** pane:

   [![Screenshot showing the Azure portal with Application Insights open to the "Logs" pane for creating queries.](./media/logic-apps-using-sap-connector/application-insights-query-panel.png)](./media/logic-apps-using-sap-connector/application-insights-query-panel.png#lightbox)

1. On the **Logs** pane, you can create a [query](/azure/data-explorer/kusto/query/) using the [Kusto Query Language (KQL)](/azure/data-explorer/kusto/concepts/) that's based on your specific requirements.

   You can use a query pattern similar to the following example query:

   ```Kusto
   customMetrics
   | extend DestinationName = tostring(customDimensions["DestinationName"])
   | extend MetricType = tostring(customDimensions["MetricType"])
   | where customDimensions contains "RfcDestinationMonitor"
   | where name contains "MaxUsedCount"
   ```

1. After you run your query, review the results.

   The following screenshot shows the example query's metrics results table:

   [![Screenshot showing Application Insights with the metrics results table.](./media/logic-apps-using-sap-connector/application-insights-metrics.png)](./media/logic-apps-using-sap-connector/application-insights-metrics.png#lightbox)

   * **MaxUsedCount** is "The maximal number of client connections that were simultaneously used by the monitored destination." as described in the [SAP NCo documentation (sign-in required)](https://support.sap.com/en/product/connectors/msnet.html#section_512604546). You can use this value to understand the number of simultaneously open connections.

   * The **valueCount** column shows **2** for each reading because metrics are generated at 30-second intervals, and Application Insights aggregates these metrics by the minute.

   * The **DestinationName** column contains a character string that is a Microsoft SAP Adapter internal name.

     To better understand this Remote Function Call (RFC) destination, use this value with `traces`, for example:

     ```Kusto
     customMetrics
     | extend DestinationName = tostring(customDimensions["DestinationName"])
     | join kind=inner (traces
        | extend DestinationName = tostring(customDimensions["DestinationName"]),
        AppServerHost = tostring(customDimensions["AppServerHost"]),
        SncMode = tostring(customDimensions["SncMode"]),
        SapClient = tostring(customDimensions["Client"])
        | where customDimensions contains "RfcDestinationMonitor"
        )
        on DestinationName , $left.DestinationName == $right.DestinationName
     | where customDimensions contains "RfcDestinationMonitor"
     | where name contains "MaxUsedCount"
     | project AppServerHost, SncMode, SapClient, name, valueCount, valueSum, valueMin, valueMax
     ```

You can also create metric charts or alerts using those capabilities in Application Insights, for example:

[![Screenshot showing Application Insights with the results in chart format.](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png)](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png#lightbox)

### Traces from Microsoft SAP Adapter

You can use traces sent from Microsoft SAP Adapter for issue post-analysis and to find any existing internal system errors that might or might not surface from SAP connector operations. These traces have `message` set to `"n\a"` because they come from an earlier event source framework that predates Application Insights, for example:

```Kusto
traces
| where message == "n/a"
| where severityLevel > 0
| extend ActivityId = tostring(customDimensions["ActivityId"])
| extend fullMessage = tostring(customDimensions["fullMessage"])
| extend shortMessage = tostring(customDimensions["shortMessage"])
| where ActivityId contains "8ad5952b-371e-4d80-b355-34e28df9b5d1"
```

The following screenshot shows the example query's traces results table:

[![Screenshot showing Application Insights with the traces results table.](./media/logic-apps-using-sap-connector/application-insights-traces.png)](./media/logic-apps-using-sap-connector/application-insights-traces.png#lightbox)

## Advanced scenarios

### Change language headers

When you connect to SAP from Logic Apps, the default language for the connection is English. You can set the language for your connection by using the [standard HTTP header `Accept-Language`](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) with your inbound requests.

> [!TIP]
> Most web browsers add an `Accept-Language` header based on the user's settings. The web browser applies this header when you create a new SAP connection in the workflow designer, either update your web browser's settings to use your preferred language, or create your SAP connection using Azure Resource Manager instead of the workflow designer.

For example, you can send a request with the `Accept-Language` header to your logic app workflow by using the **Request** trigger. All the actions in your logic app workflow receive the header. Then, SAP uses the specified languages in its system messages, such as BAPI error messages.

The SAP connection parameters for a logic app workflow don't have a language property. So, if you use the `Accept-Language` header, you might get the following error: **Please check your account info and/or permissions and try again.** In this case, check the SAP component's error logs instead. The error actually happens in the SAP component that uses the header, so you might get one of these error messages:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`

* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### Confirm transaction explicitly

When you send transactions to SAP from Azure Logic Apps, this exchange happens in two steps as described in the SAP document, [Transactional RFC Server Programs](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). By default, the **Send to SAP** action handles both the steps for the function transfer and for the transaction confirmation in a single call. The SAP connector gives you the option to decouple these steps. You can send an IDoc and rather than automatically confirm the transaction, you can use the explicit **\[IDOC] Confirm transaction ID** action.

This capability to decouple the transaction ID confirmation is useful when you don't want to duplicate transactions in SAP, for example, in scenarios where failures might happen due to causes such as network issues. When the **Send to SAP** action separately confirms the transaction ID, the SAP system completes the transaction only once.

Here's an example that shows this pattern:

1. Create a blank logic app workflow, and add the Request trigger.

1. From the SAP connector, add the **\[IDOC] Send document to SAP** action. Provide the details for the IDoc that you send to your SAP system.

1. To explicitly confirm the transaction ID in a separate step, in the **Confirm TID** field, select **No**. For the optional **Transaction ID GUID** field, you can either manually specify the value or have the connector automatically generate and return this GUID in the response from the **\[IDOC] Send document to SAP** action.

   ![Screenshot that shows the "[IDOC] Send document to SAP" action properties](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. To explicitly confirm the transaction ID, add the **\[IDOC] Confirm transaction ID** action, making sure to [avoid sending duplicate IDocs to SAP](#avoid-sending-duplicate-idocs). Click inside the **Transaction ID** box so that the dynamic content list appears. From that list, select the **Transaction ID** value that's returned from the **\[IDOC] Send document to SAP** action.

   ![Screenshot that shows the "Confirm transaction ID" action](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   After this step runs, the current transaction is marked complete at both ends, on the SAP connector side and on SAP system side.

#### Avoid sending duplicate IDocs

If you experience an issue with duplicate IDocs being sent to SAP from your logic app workflow, follow these steps to create a string variable to serve as your IDoc transaction identifier. Creating this transaction identifier helps prevent duplicate network transmissions when there are issues such as temporary outages, network issues, or lost acknowledgments.

> [!NOTE]
> SAP systems forget a transaction identifier after a specified time, or 24 hours by default. 
> As a result, SAP never fails to confirm a transaction identifier if the ID or GUID is unknown.
> If confirmation for a transaction identifier fails, this failure indicates that communcation 
> with the SAP system failed before SAP was able to acknowledge the confirmation.

1. In the workflow designer, add the action **Initialize variable** to your logic app workflow.

1. In the editor for the action **Initialize variable**, configure the following settings. Then, save your changes.

   1. For **Name**, enter a name for your variable. For example, `IDOCtransferID`.

   1. For **Type**, select **String** as the variable type.

   1. For **Value**, select the text box **Enter initial value** to open the dynamic content menu.

   1. Select the **Expressions** tab. In the list of functions, enter the function `guid()`.

   1. Select **OK** to save your changes. The **Value** field is now set to the `guid()` function, which generates a GUID.

1. After the **Initialize variable** action, add the action **\[IDOC] Send document to SAP**.

1. In the editor for the action **\[IDOC] Send document to SAP**, configure the following settings. Then, save your changes.

   1. For **IDOC type** select your message type, and for **Input IDOC message**, specify your message.

   1. For **SAP release version**, select your SAP configuration's values.

   1. For **Record types version**, select your SAP configuration's values.

   1. For **Confirm TID**, select **No**.

   1. Select **Add new parameter list** > **Transaction ID GUID**.

   1. Select the text box to open the dynamic content menu. Under the **Variables** tab, select the name of the variable that you created, for example, `IDOCtransferID`.

1. On the title bar of the action **\[IDOC] Send document to SAP**, select **...** > **Settings**.

   For **Retry Policy**, it's recommended to select **Default** &gt; **Done**. However, you can instead configure a custom policy for your specific needs. For custom policies, it's recommended to configure at least one retry to overcome temporary network outages.

1. After the action **\[IDOC] Send document to SAP**, add the action **\[IDOC] Confirm transaction ID**.

1. In the editor for the action **\[IDOC] Confirm transaction ID**, configure the following settings. Then, save your changes.

1. For **Transaction ID**, enter the name of your variable again. For example, `IDOCtransferID`.

1. Optionally, validate the deduplication in your test environment.

    1. Repeat the **\[IDOC] Send document to SAP** action with the same **Transaction ID** GUID that you used in the previous step.
    
    1. To validate which IDoc number got assigned after each call to the **\[IDOC] Send document to SAP** action, use the **\[IDOC] Get IDOC list for transaction** action with the same **Transaction ID** and the **Receive** direction.

       If the same, single IDoc number is returned for both calls, the IDoc was deduplicated.

   When you send the same IDoc twice, you can validate that SAP is able to identify the duplication of the tRFC call and resolve the two calls to a single inbound IDoc message.

## Next steps

* [Connect to on-premises systems](logic-apps-gateway-connection.md) from Azure Logic Apps