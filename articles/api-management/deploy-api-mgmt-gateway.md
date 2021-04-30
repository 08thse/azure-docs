---
title: Deploy an Azure API Management gateway on Azure Arc
description: Enable Azure Arc to deploy your self-hosted Azure API Management gateway. 
author: v-hhunter
ms.author: v-hhunter
ms.service: api-management
ms.topic: article 
ms.date: 04/30/2021
---

# Deploy an Azure API Management gateway on Azure Arc

With the integration between Azure API Management and Azure Arc on Kubernetes, you can deploy the API Management gateway component as an extension in an Azure Arc enabled Kubernetes cluster. 

## Prerequisites

* [Connect your Kubernetes cluster](../azure-arc/kubernetes/quickstart-connect-cluster.md). 
* [Create an Azure API Management instance](./get-started-create-service-instance.md).
* [Provision a gateway resource in your Azure API Management instance](./api-management-howto-provision-self-hosted-gateway.md).

> [!NOTE]
> To deploy the API management gateway extension while taking advantage of Lima features, [create a custom location](../azure-arc/kubernetes/custom-locations.md) on your connected cluster before deploying.

## Deploy the API management gateway extension
1. In the Azure portal, navigate to your API Management instance.
1. Select **Gateways** from the side navigation menu.
1. Select and open your provisioned gateway resource from the list.
1. In your provisioned gateway resource, click **Deployment** from the side navigation menu.
1. Make note of the **Token** and **Configuration URL** values for the next step.
1. In Azure CLI, deploy the gateway extension using the following command. Fill in the `token` and `configuration URL` values.
    ```azurecli
    az k8s-extension create --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <rg-name> --name <extension-name> --extension-type Microsoft.ApiManagement.Gateway --scope cluster --release-namespace {namespace} --configuration-settings gateway.endpoint='{Configuration URL}' --configuration-settings gateway.authKey='{token}' --configuration-settings service.type='NodePort' --release-train preview
    ```
1. Verify deployment status using the following CLI command:
    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <rg-name> --name <extension-name>
    ```
1. Navigate back to the **Gateways** list to verify the gateway status shows a green check mark with a node count. This status means the deployed self-hosted gateway pods:
    * Are successfully communicating with the API Management service.
    * Have a regular "heartbeat".

## Next Steps
* To learn more about the self-hosted gateway, see [Azure API Management self-hosted gateway overview](self-hosted-gateway-overview.md)
* Learn more about [Azure Arc enabled Kubernetes](../azure-arc/kubernetes/overview.md)