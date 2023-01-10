---
title: Complete the prerequisites for enabling Azure Active Directory (Azure AD) for local monitoring tools
titleSuffix: Azure Private 5G Core Preview
description: Complete the prerequisite tasks for enabling Azure Active Directory to access Azure Private 5G Core's local monitoring tools. 
author: b-branco
ms.author: biancabranco
ms.service: private-5g-core
ms.topic: how-to 
ms.date: 12/29/2022
ms.custom: template-how-to
---

# Complete the prerequisites for enabling Azure Active Directory (Azure AD) for local monitoring tools

Azure Private 5G Core provides the [distributed tracing](distributed-tracing.md) and [packet core dashboards](packet-core-dashboards.md) tools for monitoring your deployment at the edge. You can access these tools using [Azure Active Directory (Azure AD)](/azure/active-directory/authentication/overview-authentication) or a local username and password. We recommend setting up Azure AD authentication to improve security in your deployment.

In this how-to guide, you'll carry out the steps you need to complete before you can deploy or configure a site to use Azure AD to authenticate access to your local monitoring tools. You don't need to follow this if you decided to use local usernames and passwords to access the distributed tracing and packet core dashboards.

## Prerequisites

- You must have completed the steps in [Complete the prerequisite tasks for deploying a private mobile network](complete-private-mobile-network-prerequisites.md).
- Identify the IP address for accessing the local monitoring tools that you set up in [Management network](complete-private-mobile-network-prerequisites.md#management-network).
- Ensure you can sign in to the Azure portal using an account with access to the active subscription you used to create your private mobile network. This account must have permission to manage applications in Azure AD. [Azure AD built-in roles](/azure/active-directory/roles/permissions-reference.md#application-developer) that include the required permissions include, for example, Application administrator, Application developer, and Cloud application administrator.
- Ensure your local machine has admin kubectl access to the Azure Arc-enabled Kubernetes cluster. This requires an admin kubeconfig file. Contact your trials engineer for instructions on how to obtain this. <!-- TODO: update this to remove need for support -->

## Configure domain system name (DNS) for local monitoring IP

When registering your application and configuring redirect URIs, you'll need your redirect URIs to contain a domain name rather than an IP address for accessing the local monitoring tools.

In your local DNS server, configure the IP address for accessing local monitoring tools, which you set up in [Management network](complete-private-mobile-network-prerequisites.md#management-network), to resolve to a domain name of your choice.

## Register application

You'll now register a new local monitoring application with Azure AD to establish a trust relationship with the Microsoft identity platform.

1. Follow [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) to register a new application for your local monitoring tools with the Microsoft identity platform.
    1. In [Add a redirect URI](/azure/active-directory/develop/quickstart-register-app#add-a-redirect-uri), select the **Web** platform and add the following two redirect URIs, where *\<local monitoring domain\>* is the domain name for your local monitoring tools that you set up in [Configure domain system name (DNS) for local monitoring IP](#configure-domain-system-name-dns-for-local-monitoring-ip):
    
        - https://*\<local monitoring domain\>*/sas/auth/aad/callback
        - https://*\<local monitoring domain\>*/grafana/login/azuread

    1. In [Add credentials](/azure/active-directory/develop/quickstart-register-app#add-credentials), follow the steps to add a client secret.

        Make sure to record the secret under the **Value** column, as this field is only available immediately after secret creation. This is the **Client secret** value that you'll need later in this procedure.

1. Follow [App roles UI](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) to create three roles for your application (Admin, Viewer and Editor) with the following configuration:

    - In **Allowed member types**, select **Users/Groups**.
    - In **Value**, enter one of **Admin**, **Viewer** and **Editor** for each role you're creating.
    - In **Do you want to enable this app role?**, ensure the checkbox is selected.

1. Follow [Assign users and groups to roles](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#assign-users-and-groups-to-roles) to assign users and groups to the roles you created.

## Collect the information for Kubernetes Secret Objects

1. Collect the values in the following table.

    |Value  | How to collect  |  Kubernetes secret parameter name
    |---------|---------|---------|
    | **Tenant ID** | In the Azure portal, search for Azure Active Directory. You can find the **Tenant ID** field in the Overview page. | `tenant_id` |
    | **Application (client) ID** | Navigate to the new local monitoring app registration you just created. You can find the **Application (client) ID** field in the Overview page, under the **Essentials** heading. | `client_id` |
    | **Authorization URL** | In the local monitoring app registration Overview page, select **Endpoints**. Copy the contents of the **OAuth 2.0 authorization endpoint (v2)** field. | `auth_url` |
    | **Token URL** |  In the local monitoring app registration Overview page, select **Endpoints**. Copy the contents of the **OAuth 2.0 token endpoint (v2)** field. | `token_url` |
    | **Client secret** | You collected this when creating the client secret in the previous step. | `client_secret` |
    | **Distributed tracing redirect URI root** | Make a note of the following part of the redirect URI: **https://*\<local monitoring domain\>*/**. | `redirect_uri_root` |
    | **Packet core dashboards redirect URI root** | Make a note of the following part of the packet core dashboards redirect URI: **https://*\<local monitoring domain\>*/grafana**. | `root_url` |

## Create Kubernetes Secret Objects

To support Azure AD on Azure Private 5G Core applications, you'll need two files containing Kubernetes secrets.

1. Convert each of the values you collected in [Collect the information for Kubernetes Secret Objects](#collect-the-information-for-kubernetes-secret-objects) into Base64 format. For example, you can run the following command in a Linux shell:
    
    `$ echo -n  <Value> | base64`

1. Create a Kubernetes secret for distributed tracing by creating a *secret-azure-ad-sas.yaml* file containing the Base64-encoded values. The secret must be named **sas-auth-secrets**.

    ```yml
    apiVersion: v1
    kind: Secret
    metadata:
        name: sas-auth-secrets
        namespace: <deployment namespace>
    type: Opaque
    data:
        client_id: <Base64-encoded client ID>
        client_secret: <Base64-encoded client secret>
        redirect_uri_root: <Base64-encoded distributed tracing redirect URI root>
        tenant_id: <Base64-encoded tenant ID>
    ```
1. Create a Kubernetes secret for the packet core dashboards by creating a *secret-azure-ad-grafana.yaml* file containing the Base64-encoded values. The secret must be named **grafana-auth-secrets**.

    ```yml
    apiVersion: v1
    kind: Secret
    metadata:
        name: grafana-auth-secrets
        namespace: <deployment namespace>
    type: Opaque
    data:
        client_id: <Base64-encoded client ID>
        client_secret: <Base64-encoded client secret>
        auth_url: <Base64-encoded authorization URL>
        token_url: <Base64-encoded token URL>
        root_url: <Base64-encoded packet core dashboards redirect URI root>
    ```

1. In a command line with kubectl access to the Azure Arc-enabled Kubernetes cluster, apply the Secret Object for both distributed tracing and the packet core dashboards, specifying the admin kubeconfig filename.
    
    `kubectl apply -f  /home/centos/secret-azure-ad-sas.yaml --kubeconfig=<admin_kubeconfig>`

    `kubectl apply -f  /home/centos/secret-azure-ad-grafana.yaml --kubeconfig=<admin_kubeconfig>`

1. Use the following commands to verify if the Secret Objects were applied correctly, specifying your deployment namespace and the admin kubeconfig filename. You should see the correct **Name**, **Namespace** and **Type** values, along with the size of the encoded values.

    `kubectl describe secrets -n <deployment namespace> sas-auth-secrets --kubeconfig=<admin_kubeconfig>`

    `kubectl describe secrets -n <deployment namespace> grafana-auth-secrets --kubeconfig=<admin_kubeconfig>`

## Next steps

Ensure you have completed all the steps in [Collect the required information for a site](collect-required-information-for-a-site.md) and deploy the site:

   - [Create a site - Azure portal](create-a-site.md)
   - [Create a site - ARM template](create-site-arm-template.md)
