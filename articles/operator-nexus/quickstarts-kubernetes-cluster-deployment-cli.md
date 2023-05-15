---
title: Create an Azure Nexus Kubernetes cluster by using Azure CLI
description: Learn how to create an Azure Nexus Kubernetes cluster by using Azure CLI.
ms.service: azure-operator-nexus
author: dramasamy
ms.author: dramasamy
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/13/2023
---

# Quickstart: Create an Azure Nexus Kubernetes cluster by using Azure CLI

* Deploy an Azure Nexus Kubernetes cluster using Azure CLI.

## Before you begin

[!INCLUDE [kubernetes-cluster-prereq](./includes/kubernetes-cluster/quickstart-prereq.md)]

## Create an Azure Nexus Kubernetes cluster

The following example creates a cluster named *myNexusAKSCluster* in resource group *myResourceGroup* in the *eastus* location.

Before you run the commands, you need to set several variables to define the configuration for your cluster. Here are the variables you need to set, along with some default values you can use for certain variables:

* **CUSTOM_LOCATION**: This argument specifies a custom location of the Nexus instance. Azure Extended Locations allow you to use Azure services directly in your datacenter. Define this variable according to your own datacenter location.
* **CSN_ID**: CSN ID is the unique identifier for the cloud services network you want to use. You should replace it with your actual Cloud Services Network ID.
* **CNI_ID**: CNI ID the unique identifier for the network interface to be used by the container runtime. You should replace it with your actual CNI Network ID.
* **CLUSTER_NAME**: The name you want to give to your Kubernetes cluster. In this example, we're using ```myNexusAKSCluster```.
* **RESOURCE_GROUP**: The name of the Azure resource group where you want to create the cluster. In this example, we're using ```myResourceGroup```.
* **SUBSCRIPTION_ID**: The ID of your Azure subscription. This ID is obtained automatically using the command ```az account show -o tsv --query id```.
* **LOCATION**: The Azure region where you want to create your cluster. In this example, we're using ```eastus```.
* **K8S_VERSION**: The version of Kubernetes you want to use. In this example, we're using ```v1.24.9```.
* **AAD_ADMIN_GROUP_OBJECT_ID**: The object ID of the Azure Active Directory group that should have admin privileges on the cluster. You should replace it with your actual Azure AD Group Object ID.
* **ADMIN_USERNAME**: The username for the cluster administrator. In this example, we're using ```clouduser```.
* **SSH_PUBLIC_KEY**: The SSH public key that is used for secure communication with the cluster. This key is obtained automatically from the file ```~/.ssh/id_rsa.pub```.
* **CONTROL_PLANE_COUNT**: The number of control plane nodes for the cluster. In this example, we're using ```1```.
* **CONTROL_PLANE_VM_SIZE**: The size of the virtual machine for the control plane nodes. In this example, we're using ```NC_G2_v1```.
* **INITIAL_AGENT_POOL_NAME**: The name of the initial agent pool. In this example, we're using ```agentpool1```.
* **INITIAL_AGENT_POOL_COUNT**: The number of nodes in the initial agent pool. In this example, we're using ```1```.
* **INITIAL_AGENT_POOL_VM_SIZE**: The size of the virtual machine for the agent pool nodes. In this example, we're using ```NC_G2_v1```.
* **POD_CIDR**: The network range for the Kubernetes pods in the cluster, in CIDR notation. In this example, we're using ```10.244.0.0/16```.
* **SERVICE_CIDR**: The network range for the Kubernetes services in the cluster, in CIDR notation. In this example, we're using ```10.96.0.0/16```.
* **DNS_SERVICE_IP**: The IP address for the Kubernetes DNS service. In this example, we're using ```10.96.0.10```.

Once you've defined these variables, you can run the Azure CLI command to create the cluster. The ```--debug``` flag at the end is used to provide more detailed output for troubleshooting purposes.

Here are the set commands for defining these variables:

```bash
CUSTOM_LOCATION="/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/microsoft.extendedlocation/customlocations/<custom-location-name>"
CSN_ID="/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.NetworkCloud/cloudServicesNetworks/<csn-name>"
CNI_ID="/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.NetworkCloud/l3Networks/<l3Network-name>"
AAD_ADMIN_GROUP_OBJECT_ID="00000000-0000-0000-0000-000000000000"
CLUSTER_NAME="myNexusAKSCluster"
RESOURCE_GROUP="myResourceGroup"
SUBSCRIPTION_ID="$(az account show -o tsv --query id)"
LOCATION="eastus"
K8S_VERSION="v1.24.9"
ADMIN_USERNAME="clouduser"
SSH_PUBLIC_KEY="$(cat ~/.ssh/id_rsa.pub)"
CONTROL_PLANE_COUNT="1"
CONTROL_PLANE_VM_SIZE="NC_G2_v1"
INITIAL_AGENT_POOL_NAME="agentpool1"
INITIAL_AGENT_POOL_COUNT="1"
INITIAL_AGENT_POOL_VM_SIZE="NC_G2_v1"
POD_CIDR="10.244.0.0/16"
SERVICE_CIDR="10.96.0.0/16"
DNS_SERVICE_IP="10.96.0.10"
```
> [!NOTE]
> Please replace the placeholders for CUSTOM_LOCATION, CSN_ID, CNI_ID and AAD_ADMIN_GROUP_OBJECT_ID with your actual values before running these commands.

* After defining these variables, you can create the Kubernetes cluster by executing the following Azure CLI command:

```azurecli-interactive
az networkcloud kubernetescluster create \
--name "${CLUSTER_NAME}" \
--resource-group "${RESOURCE_GROUP}" \
--subscription "${SUBSCRIPTION_ID}" \
--extended-location name="${CUSTOM_LOCATION}" type=CustomLocation \
--location "${LOCATION}" \
--kubernetes-version "${K8S_VERSION}" \
--aad-configuration admin-group-object-ids="[${AAD_ADMIN_GROUP_OBJECT_ID}]" \
--administrator-configuration \
    admin-username="${ADMIN_USERNAME}" \
    ssh-public-keys="[{keyData:'${SSH_PUBLIC_KEY}'}]" \
--control-plane-node-configuration \
    count="${CONTROL_PLANE_COUNT}" \
    vm-sku-name="${CONTROL_PLANE_VM_SIZE}" \
--initial-agent-pool-configurations "[{count:${INITIAL_AGENT_POOL_COUNT},mode:System,name:${INITIAL_AGENT_POOL_NAME},vm-sku-name:${INITIAL_AGENT_POOL_VM_SIZE}}]" \
--network-configuration \
    cloud-services-network-id="${CSN_ID}" \
    cni-network-id="${CNI_ID}" \
    pod-cidrs="[${POD_CIDR}]" \
    service-cidrs="[${SERVICE_CIDR}]" \
    dns-service-ip="${DNS_SERVICE_IP}" \
--debug
```

After a few minutes, the command completes and returns JSON-formatted information about the cluster.

> [!NOTE]
> When you create a new cluster, Nexus automatically creates a second resource group to store the Kuberenetes cluster resources.

## Review deployed resources

[!INCLUDE [quickstart-review-deployment-cli](./includes/kubernetes-cluster/quickstart-review-deployment-cli.md)]

## Connect to the cluster

[!INCLUDE [quickstart-cluster-connect](./includes/kubernetes-cluster/quickstart-cluster-connect.md)]

## Clean up resources

[!INCLUDE [quickstart-cleanup](./includes/kubernetes-cluster/quickstart-cleanup.md)]

## Next steps

[!INCLUDE [quickstart-nextsteps](./includes/kubernetes-cluster/quickstart-nextsteps.md)]