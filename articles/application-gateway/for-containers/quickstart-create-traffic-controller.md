---

title: 'Quickstart: Create Application Gateway for Containers'
titlesuffix: Azure Application Load Balancer
description: In this quickstart, you learn how to provision Application Gateway for Containers.
services: application-gateway
author: greglin
ms.service: application-gateway
ms.subservice: traffic-controller
ms.topic: quickstart
ms.date: 5/1/2023
ms.author: greglin
---

# Quickstart: Create a Application Gateway for Containers

This document provides instructions on how to deploy the 3 types of resources (Application Gateway for Containers, Association, and Frontend) needed for Application Gateway for Containers to work with your AKS workload, and how to install ALB Controller on your AKS cluster to control the behavior of the Application Gateway for Containers.

## Prerequisites

You need to complete the following tasks prior to deploying Application Gateway for Containers on Azure and installing ALB Controller on your cluster:

1. Prepare your Azure subscription and your `az-cli` client.

	```bash
	# Login to your Azure subscription.
	SUBSCRIPTION_ID='<your subscription id>'
	az login
	az account set --subscription $SUBSCRIPTION_ID

	# Register required resource providers on Azure.
	az provider register --namespace Microsoft.ContainerService
	az provider register --namespace Microsoft.Network
	az provider register --namespace Microsoft.NetworkFunction
	az provider register --namespace Microsoft.ServiceNetworking

	# Register features to enable Workload Identity on AKS.
	az feature register --name EnableWorkloadIdentityPreview --namespace Microsoft.ContainerService
	az extension add --name aks-preview
	az extension update --name aks-preview
	```

1. **(Optional)** Create an AKS cluster for your workload.

	If you have an existing AKS cluster for running your workload in one of the regions where Application Gateway for Containers is available for preview, you may skip ahead to the next step.

	> **Note**
	>
	> The AKS cluster needs to be in the following regions where Application Gateway for Containers is available for Private Preview.
	>
	> - North Central US
	> - North Europe
	>
	> Additionally, your AKS cluster must use the [Azure CNI](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) instead of Kubenet.

	```bash
	AKS_NAME='<your cluster name>'
	RESOURCE_GROUP='<the resource group of your AKS cluster>'
	LOCATION='northeurope' # The list of available regions may grow as we roll out to more preview regions
	VM_SIZE='<the size of the vm in AKS>' # The size needs to be available in your location

	az group create --name $RESOURCE_GROUP --location $LOCATION
	az aks create \
		--resource-group $RESOURCE_GROUP \
		--name $AKS_NAME \
		--location $LOCATION \
		--node-vm-size $VM_SIZE \
		--network-plugin azure \
		--generate-ssh-key
	```

2. Delegate a Subnet in the AKS Virtual Network to the Application Gateway for Containers Service.

	Once you have an AKS cluster, identify the Virtual Network to which the agent pool is connected using the following commands:

	```bash
	AKS_NAME='<your cluster name>'
	RESOURCE_GROUP='<your resource group>'

	mcResourceGroup=$(az aks show --name $AKS_NAME --resource-group $RESOURCE_GROUP --query "nodeResourceGroup" -o tsv)
	clusterSubnetId=$(az vmss list --resource-group $mcResourceGroup --query '[0].virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].subnet.id' -o tsv)
	read -d '' vnetName vnetResourceGroup vnetId <<< $(az network vnet show --ids $clusterSubnetId --query '[name, resourceGroup, id]' -o tsv)
	echo $vnetId
	```

	Once the Virtual Network has been identified, create a new Subnet with at least 120 available addresses and delegate it to the Application Gateway for Containers service with the following command:

	```bash
	subnetAddressPrefix='<an address space under the vnet that has at least 120 available addresses (/25 or smaller cidr prefix)>'
	tcSubnetName='tc-subnet' # subnet name can be any non-reserved subnet name (i.e. GatewaySubnet, AzureFirewallSubnet, AzureBastionSubnet would all be invalid)
	az network vnet subnet create \
		--resource-group $vnetResourceGroup \
		--vnet-name $vnetName \
		--name $tcSubnetName \
		--delegations 'Microsoft.ServiceNetworking/trafficControllers' \
		--address-prefixes $subnetAddressPrefix
	```

3. Enable Workload Identity on your AKS cluster.

    ```bash
    az aks update -g $RESOURCE_GROUP -n $AKS_NAME --enable-oidc-issuer --enable-workload-identity --no-wait
    ```

4. Install Helm.

	[Helm](https://github.com/helm/helm) is an open-source packaging tool that is used to install ALB controller. Ensure that you have the latest version of helm installed. Instructions on installation can be found [here](https://github.com/helm/helm#install).

## Deploy Application Gateway for Containers

1. The following commands deploy Application Gateway for Containers (along with the Association and Frontend resources) using an [ARM template](./templates/traffic-controller.template.json).

	```bash
	TRAFFIC_CONTROLLER_NAME='traffic-controller'
	FRONTEND_NAME='frontend'

	tcSubnetId=$(az network vnet subnet show --resource-group $vnetResourceGroup --vnet-name $vnetName --name $tcSubnetName --query id -o tsv)
	az deployment group create \
		--resource-group $RESOURCE_GROUP \
		--name 'sample-traffic-controller-deployment' \
		--template-uri 'https://trafficcontrollerdocs.blob.core.windows.net/templates/traffic-controller.template.json' \
		--parameters "trafficControllerName=$TRAFFIC_CONTROLLER_NAME" \
		--parameters "frontendName=$FRONTEND_NAME" \
		--parameters "subnetId=$tcSubnetId" \
		--parameters "mcResourceGroup=$mcResourceGroup"
	```

2. Once the deployment is successful, you may verify the creation of the Application Gateway for Containers resources with the following commands:

	```bash
	# Verify the Application Gateway for Containers
	az resource show --ids $(az resource list --resource-type 'Microsoft.ServiceNetworking/trafficControllers' --resource-group $RESOURCE_GROUP --query '[].id' -o tsv)

	# Verify the Application Gateway for Containers Association
	az resource show --ids $(az resource list --resource-type 'Microsoft.ServiceNetworking/trafficControllers/associations' --resource-group $RESOURCE_GROUP --query '[].id' -o tsv)

	# Verify the Application Gateway for Containers Frontend
	az resource show --ids $(az resource list --resource-type 'Microsoft.ServiceNetworking/trafficControllers/frontends' --resource-group $RESOURCE_GROUP --query '[].id' -o tsv)
	```

## Install ALB Controller

0. Prerequisites - Federate user assigned identity as Pod Identity to use in AKS cluster.

    ```bash
	AKS_OIDC_ISSUER="$(az aks show -n "$AKS_NAME" -g "$RESOURCE_GROUP" --query "oidcIssuerProfile.issuerUrl" -o tsv)"
    az identity federated-credential create --name "azure-application-lb-identity" \
	    --identity-name "azure-application-lb-identity" \
		--resource-group $RESOURCE_GROUP \
		--issuer "$AKS_OIDC_ISSUER" \
		--subject "system:serviceaccount:azure-application-lb-system:gateway-controller-sa"
    ```

1. Install ALB Controller

	LB Controller can be installed by running the following commands:

	```bash
	az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_NAME
	helm upgrade \
		--install alb-controller oci://mcr.microsoft.com/application-lb/charts/gateway-controller \
		--create-namespace --namespace azure-application-lb-system \
		--version '0.1.022981' \
		--set gatewayController.podIdentity.clientID=$(az identity show -g $RESOURCE_GROUP -n azure-application-lb-identity --query clientId -o tsv)
	```

### Verify the ALB Controller installation

1. Verify the ALB Controller pods are ready:

    ```bash
    kubectl get pods -n azure-application-lb-system
    ```
    You should see the following:
    - 1 alb-controller pod with status **Running** and 1/1 **Ready**
    - 1 alb-controller-bootstrap pod with status **Running** and 1/1 **Ready**

2. Verify GatewayClass `azure-application-lb` is installed on your cluster:

    ```bash
    kubectl get gatewayclass azure-application-lb -o yaml
    ```
    You should see that the GatewayClass has a condition that reads **Valid GatewayClass** . This indicates that a default GatewayClass has been set up and that any gateway objects that reference this GatewayClass is managed by ALB Controller automatically.

    You should also see that the gateway class has a parameterRef section that links this gateway class to an ApplicationLbParam of name `default`

3. Verify an ApplicationLbParam called `default` is installed on your cluster:
    ```bash
    kubectl get applicationlbparam
    ```

## Link your ALB Controller to Application Gateway for Containers

Now that you have successfully installed an ALB Controller on your cluster, you can link it to an existing Application Gateway for Containers by leveraging the GatewayClass and ApplicationLbParam on the cluster.

Update the ApplicationLbParam to contain the resource ID of the Application Gateway for Containers you wish to associate with your ALB Controller.

```bash
kubectl apply -f - <<EOF
apiVersion: networking.azure.io/v1alpha1
kind: ApplicationLbParam
metadata:
  name: default
spec:
  ipAddress:
  subnetPrefix:
  loadBalancerId: $(az resource show --resource-type 'Microsoft.ServiceNetworking/trafficControllers' -g $RESOURCE_GROUP -n $TRAFFIC_CONTROLLER_NAME --query id -o tsv)
EOF
```

> **Note**
>
> You may see the following warning, which may safely be ignored:
> 
> _Warning: resource applicationlbparams/default is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply._

## Test it out!

Congratulations, you have installed ALB Controller on your cluster!

## Uninstall Application Gateway for Containers and ALB Controller

1. To delete the Application Gateway for Containers, you may simply delete the Resource Group containing the Application Gateway for Containers resources:

	```bash
	az group delete --resource-group $RESOURCE_GROUP
	```

2. To uninstall ALB Controller and its resources from your cluster run the following commands:

	```bash
	helm uninstall alb-controller -n azure-application-lb-system
	kubectl delete ns azure-application-lb-system
	kubectl delete gatewayclass azure-application-lb
	kubectl delete applicationlbparam default
	```
