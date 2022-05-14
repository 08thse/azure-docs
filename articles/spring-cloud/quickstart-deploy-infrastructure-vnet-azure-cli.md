---
title: Quickstart - Provision Azure Spring Cloud using Azure CLI
description: This quickstart shows you how to use Azure CLI to deploy a Spring Cloud cluster into an existing virtual network.
services: azure-cli
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-java, mode-api
ms.author: vramasubbu
ms.date: 05/13/2022
---

# Quickstart: Provision Azure Spring Cloud using Azure CLI

**This article applies to:** ✔️ Standard tier ✔️ Enterprise tier

This quickstart describes how to use Azure CLI to deploy an Azure Spring Cloud cluster into an existing virtual network.

Azure Spring Cloud makes it easy to deploy Spring applications to Azure without any code changes. The service manages the infrastructure of Spring Cloud applications so developers can focus on their code. Azure Spring Cloud provides lifecycle management using comprehensive monitoring and diagnostics, configuration management, service discovery, CI/CD integration, blue-green deployments, and more.

## Prerequisites

* An Azure subscription. If you don't have a subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.
* Two dedicated subnets for the Azure Spring Cloud cluster, one for the service runtime and another for the Spring applications. For subnet and virtual network requirements, see the [Virtual network requirements](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements) section of [Deploy Azure Spring Cloud in a virtual network](how-to-deploy-in-azure-virtual-network.md).
* An existing Log Analytics workspace for Azure Spring Cloud diagnostics settings and a workspace-based Application Insights resource. For more information, see [Analyze logs and metrics with diagnostics settings](diagnostic-services.md) and [Application Insights Java In-Process Agent in Azure Spring Cloud](how-to-application-insights.md).
* Three internal Classless Inter-Domain Routing (CIDR) ranges (at least */16* each) that you've identified for use by the Azure Spring Cloud cluster. These CIDR ranges will not be directly routable and will be used only internally by the Azure Spring Cloud cluster. Clusters may not use *169.254.0.0/16*, *172.30.0.0/16*, *172.31.0.0/16*, or *192.0.2.0/24* for the internal Spring Cloud CIDR ranges, or any IP ranges included within the cluster virtual network address range.
* Service permission granted to the virtual network. The Azure Spring Cloud Resource Provider requires Owner permission to your virtual network in order to grant a dedicated and dynamic service principal on the virtual network for further deployment and maintenance. For instructions and more information, see the [Grant service permission to the virtual network](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network) section of [Deploy Azure Spring Cloud in a virtual network](how-to-deploy-in-azure-virtual-network.md).
* If you're using Azure Firewall or a Network Virtual Appliance (NVA), you'll also need to satisfy the following prerequisites:
  * Network and fully qualified domain name (FQDN) rules. For more information, see [Virtual network requirements](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements).
  * A unique User Defined Route (UDR) applied to each of the service runtime and Spring application subnets. For more information about UDRs, see [Virtual network traffic routing](../virtual-network/virtual-networks-udr-overview.md). The UDR should be configured with a route for *0.0.0.0/0* with a destination of your NVA before deploying the Spring Cloud cluster. For more information, see the [Bring your own route table](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table) section of [Deploy Azure Spring Cloud in a virtual network](how-to-deploy-in-azure-virtual-network.md).
* [Azure CLI](/cli/azure/install-azure-cli)
* If deploying Azure Spring Enterprise for the first time in the the target subscription, you are required to register the provider, and accept the legal terms for the Enterprise tier
```azurecli
az provider register --namespace Microsoft.SaaS
az term accept --publisher vmware-inc --product azure-spring-cloud-vmware-tanzu-2 --plan tanzu-asc-ent-mtr
```

## Review the Azure CLI deployment script

The deployment script used in this quickstart is from the [Azure Spring Cloud reference architecture](reference-architecture.md).

# [Azure Spring Standard](#tab/azure-spring-standard)

:::code language="azurecli" source="~/azure-spring-cloud-reference-architecture/CLI/brownfield-deployment/azuredeploySpringStandard.sh":::

# [Azure Spring Enterprise](#tab/azure-spring-enterprise)

:::code language="azurecli" source="~/azure-spring-cloud-reference-architecture/CLI/brownfield-deployment/azuredeploySpringEnterprise.sh":::

---

## Deploy the cluster

To deploy the Azure Spring Cloud cluster using the Azure CLI script, follow these steps:

1. Sign in to Azure by using the following command:

   ```azurecli
   az login
   ```

   After you sign in, this command will output information about all the subscriptions you have access to. Take note of the name and ID of the subscription you want to use.

1. Set the target subscription.

   ```azurecli
   az account set --subscription "<your subscription name>"
   ```

1. Register the Azure Spring Cloud Resource Provider.

   ```azurecli
   az provider register --namespace 'Microsoft.AppPlatform'
   ```

1. Add the required extensions to Azure CLI.

   ```azurecli
   az extension add --name spring-cloud
   ```

1. Choose a deployment location from the regions where Azure Spring Cloud is available, as shown in [Products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all).

1. Use the following command to generate a list of Azure locations. Take note of the short **Name** value for the region you selected in the previous step.

   ```azurecli
   az account list-locations --output table
   ```

1. Create a resource group to deploy the resource to.

   ```azurecli
   az group create --name <your-resource-group-name> --location <location-name>
   ```

1. Save the script for Azure Spring [Standard](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/CLI/brownfield-deployment/azuredeploySpringStandard.sh) or [Enterprise](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/CLI/brownfield-deployment/azuredeploySpringEnterprise.sh) locally, then execute it from the Bash prompt.

# [Azure Spring Standard](#tab/azure-spring-standard-script)

   ```azurecli
   ./azuredeploySpringStandard.sh
   ```

# [Azure Spring Enterprise(#tab/azure-spring-enterprise-script)

   ```azurecli
   ./azuredeploySpringEnterprise.sh
   ```
---

1. Enter the following values when prompted by the script:

   * The Azure subscription ID that you saved earlier.
   * The Azure location name that you saved earlier.
   * The name of the resource group that you created earlier.
   * The name of the virtual network resource group where you'll deploy your resources.
   * The name of the spoke virtual network (for example, *vnet-spoke*).
   * The name of the subnet to be used by the Spring Cloud App Service (for example, *snet-app*).
   * The name of the subnet to be used by the Spring Cloud runtime service (for example, *snet-runtime*).
   * The name of the resource group for the Azure Log Analytics workspace to be used for storing diagnostic logs.
   * The name of the Azure Log Analytics workspace (for example, *la-cb5sqq6574o2a*).
   * The CIDR ranges from your virtual network to be used by Azure Spring Cloud (for example, *XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16*).
   * The key/value pairs to be applied as tags on all resources that support tags. For more information, see [Use tags to organize your Azure resources and management hierarchy](../azure-resource-manager/management/tag-resources.md). Use a space-separated list to apply multiple tags (for example, *environment=Dev BusinessUnit=finance*).

After you provide this information, the script will create and deploy the Azure resources.

## Review deployed resources

You can either use the Azure portal to check the deployed resources, or use Azure CLI to list the deployed resources.

## Clean up resources

If you plan to continue working with subsequent quickstarts and tutorials, you might want to leave these resources in place. When no longer needed, delete the resource group, which deletes the resources in the resource group. To delete the resource group by using Azure CLI, use the following commands:

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## Next steps

In this quickstart, you deployed an Azure Spring Cloud instance into an existing virtual network using Azure CLI, and then validated the deployment. To learn more about Azure Spring Cloud, continue on to the resources below.

* Deploy one of the following sample applications from the locations below:
  * [Pet Clinic App with MySQL Integration](https://github.com/azure-samples/spring-petclinic-microservices)
  * [Simple Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI).
* Use [custom domains](tutorial-custom-domain.md) with Azure Spring Cloud.
* Expose applications in Azure Spring Cloud to the internet using [Azure Application Gateway](expose-apps-gateway-azure-firewall.md).
* View the secure end-to-end [Azure Spring Cloud reference architecture](reference-architecture.md), which is based on the [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).
