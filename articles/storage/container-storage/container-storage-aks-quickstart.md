---
title: Quickstart for configuring and using Azure Container Storage with Azure Kubernetes Service (AKS)
description: Learn how to configure and use Azure Container Storage with Azure Kubernetes Service. You'll end up with two new storage classes that you can use for your Kubernetes workloads.
author: khdownie
ms.service: storage
ms.topic: quickstart
ms.date: 04/03/2023
ms.author: kendownie
ms.subservice: container-storage
---

# Quickstart: Use Azure Container Storage with Azure Kubernetes Service
Azure Container Storage is a service built natively for containers that enables customers to create and manage volumes for running stateful container applications. This Quickstart shows you how to configure and use Azure Container Storage with Azure Kubernetes Service (AKS), using Azure Disks as back-end storage. At the end, you'll have new storage classes and a pod that you can use for your Kubernetes workloads.

[!INCLUDE [azure-cli-prepare-your-environment.md](~/articles/reusable-content/azure-cli/azure-cli-prepare-your-environment.md)]

## Getting started

- If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

- Sign up for the public preview by completing the [onboarding survey](https://aka.ms/AzureContainerStoragePreviewSignUp).

- This article requires version 2.0.64 or later of the Azure CLI. If you're using Azure Cloud Shell, the latest version is already installed. If you plan to run the commands in this quickstart locally instead of in Azure Cloud Shell, be sure to run them with administrative privileges.

- If you're using Azure Cloud Shell, you might be prompted to mount storage. Select the Azure subscription in which you want to create the storage account and select **Create**.

## Create a resource group

An Azure resource group is a logical group in which Azure resources are deployed and managed. When you create a resource group, you are prompted to specify a location. This location is:

* The storage location of your resource group metadata.
* Where your resources will run in Azure if you don't specify another region during resource creation.

> [!IMPORTANT]
> Azure Container Storage Preview is only available in *eastus* and *westeurope* regions.

The following example creates a resource group named *myContainerStorageRG* in the *eastus* region.

1. Set your subscription context using the `az account set` command. You can view the subscription IDs for all the subscriptions you have access to by running the `az account list --output table` command.

   ```azurecli-interactive
   az account set --subscription <your-subscription-id>
   ```

2. Create a resource group using the `az group create` command.

   ```azurecli-interactive
   az group create --name myContainerStorageRG --location eastus
   ```

   If the resource group was created successfully, you'll see output similar to this:
   
   ```json
   {
     "id": "/subscriptions/<guid>/resourceGroups/myContainerStorageRG",
     "location": "eastus",
     "managedBy": null,
     "name": "myContainerStorageRG",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```

## Create AKS cluster

First, make sure the identity you're using to create your cluster has the appropriate minimum permissions. For more details, see [Access and identity options for Azure Kubernetes Service](../../aks/concepts-identity.md).

Create a Linux-based AKS cluster with a single master node using the `az aks create` command. The following example creates a cluster named *myAKSCluster* with one node and enables a system-assigned managed identity:

```azurecli-interactive
az aks create -g myContainerStorageRG -n myAKSCluster --node-count 1 --generate-ssh-keys
```

The deployment will take a few minutes to complete.

## Connect to the cluster

To connect to the cluster, use the Kubernetes command-line client, `kubectl`. It's already installed if you're using Azure Cloud Shell, or you can install it locally by running the `az aks install-cli` command.

1. Configure `kubectl` to connect to your cluster using the `az aks get-credentials` command. The following command:

    * Downloads credentials and configures the Kubernetes CLI to use them.
    * Uses `~/.kube/config`, the default location for the Kubernetes configuration file. You can specify a different location for your Kubernetes configuration file using the *--file* argument.

    ```azurecli-interactive
    az aks get-credentials --resource-group myContainerStorageRG --name myAKSCluster
    ```

2. Verify the connection to your cluster using the `kubectl get` command. This command returns a list of the cluster nodes.

    ```azurecli-interactive
    kubectl get nodes
    ```

3. The following output example shows the single node created in the previous steps. Make sure the status shows *Ready*:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.24.9
    ```

## Create and label a node pool

You'll need a node pool of at least three virtual machines (VMs). Each VM must have a minimum of four virtual CPUs (vCPUs), so we'll use the standard DS4 VM size.

To create a new node pool, run the following command:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name storagepool --resource-group myContainerStorageRG --node-vm-size standard_ds4_v2 --node-count 3 --labels openebs.io/engine=io.engine
```

If you already have an existing node pool you'd like to use, run the following command instead to label the node pool:

```azurecli-interactive
az aks nodepool update --resource-group <resource-group> --cluster-name <cluster-name> --name <nodepool_name> --labels openebs.io/engine=io.engine
```

> [!TIP]
> You can verify that the node pool has been correctly labeled by signing into the [Azure portal](https://portal.azure.com?azure-portal=true) and navigating to your AKS cluster. Go to **Settings > Node pools**, select your node pool, and under **Taints and labels** you should see `Labels: openebs.io/engine : io.engine`.

## Assign Contributor role to AKS managed identity

When you create an AKS cluster, AKS automatically creates a second resource group to store the AKS resources. For more information, see [Why are two resource groups created with AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks). This second resource group follows the naming convention `MC_YourResourceGroup_YourAKSClusterName_Region`. In order to allow Azure Container Storage to provision storage, follow these steps to grant Contributor-level access to the AKS managed identity within this resource group.

> [!IMPORTANT]
> You'll need an Owner role in Azure RBAC for your subscription in order to grant the Contributor role to the managed identity. If you don't have sufficient permissions, you might need to ask your admin to perform these steps.

1. Sign into the [Azure portal](https://portal.azure.com?azure-portal=true) and select **Resource groups**.
1. Locate the resource group that AKS created. For this Quickstart, it would be `MC_myContainerStorageRG_myAKSCluster_eastus`. Select the resource group.
1. Locate the managed identity with your cluster name and `-agentpool` appended. For this Quickstart, it would be `myAKSCluster-agentpool`. Copy or take note of the name of this managed identity.

   :::image type="content" source="media/container-storage-aks-quickstart/locate-aks-managed-identity.png" alt-text="Screenshot showing how to locate the AKS managed identity in the Azure portal." lightbox="media/container-storage-aks-quickstart/locate-aks-managed-identity.png":::

1. Select **Access control (IAM)** from the left pane.
1. Select **Add > Add role assignment**.
1. Under **Assignment type**, select **Privileged administrator roles** and then **Contributor**.
1. Under **Assign access to**, select **Managed identity**.
1. Under **Members**, click **+ Select members**. The **Select managed identities** menu will appear.
1. Under **Managed identity**, select **User-assigned managed identity**.
1. Under **Select**, search for and select the managed identity that has *agentpool* in it that you identified in step 3.
1. Select **Review + assign**.

## Install Azure Container Storage

The initial install uses Azure Arc CLI commands to download a new extension. The `--name` value can be whatever you want. During installation, you might be asked to install the `k8s-extension`. Select **Y**.

```azurecli-interactive
az k8s-extension create --cluster-type managedClusters --cluster-name myAKSCluster --resource-group myContainerStorageRG --name azurecontainerstorage --extension-type microsoft.azstor --scope cluster --release-train staging --release-namespace azstor
```

Installation takes 10-15 minutes to complete. You can check if the installation completed correctly by running the following command and ensuring that `provisioningState` says Succeeded:

```azurecli-interactive
az k8s-extension list --cluster-name myAKSCluster --resource-group myContainerStorageRG --cluster-type managedClusters
```

## Create a storage pool utilizing Azure Disks

Now you can create a storage pool, which is a logical grouping of storage for your Kubernetes cluster, by defining it in a yaml file. Follow these steps to create a storage pool for Azure managed disks. 

1. Run `code acstor-storagepool.yaml` to create a yaml file.

2. Paste in the following code. The `name` value can be whatever you want. 

   ```azurecli-interactive
   apiVersion: containerstorage.azure.com/v1alpha1
   kind: StoragePool
   metadata:
      name: azuredisk
      namespace: azstor
   spec:
      poolType:
          csi: {}
      resources:
          limits: {"storage": 10Ti}
          requests: {"storage": 5Ti}
   ```

3. Apply the yaml file to create the storage pool.

   ```azurecli-interactive
   kubectl apply -f acstor-storagepool.yaml 
   ```

When storage pool creation is complete, you'll see a message like:

```output
storagepool.containerstorage.azure.com/azuredisk created
```

You can also run this command to check the status of the storage pool:

```azurecli-interactive
kubectl describe sp azuredisk -n azstor
```

## Display the available storage classes

When the storage pool is ready to use, you must select a storage class to define how storage is dynamically created when creating persistent volume claims and deploying persistent volumes.

Run `kubectl get sc` to display the available storage classes. You should see output that includes the following.

```output
azure-disk-sc-for-mayastor
azurecontainerstorage-single-replica
```

## Create a persistent volume claim

A persistent volume claim is used to automatically provision storage based on a storage class. Follow these steps to create a persistent volume claim. 

1. Run `code acstor-pvc.yaml` to create a yaml file.

2. Paste in the following code. The `name` value can be whatever you want. 

   ```azurecli-interactive
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
      name: azurediskpvc
   spec:
      accessModes:
         - ReadWriteOnce
      storageClassName: azuredisk
      resources:
         requests:
            storage: 100Gi
   ```

3. Apply the yaml file to create the persistent volume claim.

   ```azurecli-interactive
   kubectl apply -f acstor-pvc.yaml
   ```

You should see output similar to:

```output
persistentvolumeclaim/azurediskpvc created
```

You can verify the status of the persistent volume claim by running the following command:

```azurecli-interactive
kubectl describe pvc azurediskpvc
```

Once the persistent volume claim is created, it's ready to mount onto a pod.

## Deploy a pod and attach a persistent volume

Create a pod using Fio (flexible I/O) for benchmarking and workload simulation, and specify a mount path for the persistent volume.

1. Run `code acstor-pod.yaml` to create a yaml file.

2. Paste in the following code.

   ```azurecli-interactive
   kind: Pod
   apiVersion: v1
   metadata:
     name: fiopod
   spec:
     nodeSelector:
       openebs.io/engine: io.engine
     volumes:
       - name: 
         persistentVolumeClaim:
           claimName: azurediskpvc
     containers:
       - name: fio
         image: nixery.dev/shell/fio
         args:
           - sleep
           - "1000000"
         volumeMounts:
           - mountPath: "/volume"
             name: azurediskpv
   ```

3. Apply the yaml file to deploy the pod.

   ```azurecli-interactive
   kubectl apply -f acstor-pod.yaml
   ```

You should see output similar to the following:

   ```output
   pod/fiopod created
   ```

4. Check that the pod is running and that the persistent volume claim has been bound successfully to the pod:

   ```azurecli-interactive
   kubectl describe pod fiopod
   kubectl describe pvc azurediskpvc
   ```

5. Check fio testing to see its current status:

   ```azurecli-interactive
   kubectl exec -it fiopod -- fio --name=benchtest --size=800m --filename=/volume/test --direct=1 --rw=randrw --ioengine=libaio --bs=4k --iodepth=16 --numjobs=8 --time_based --runtime=60
   ```

You now have a pod with storage that you can use for your Kubernetes workloads.

## Clean up resources

To uninstall Azure Container Storage, you can delete the `k8s-extension` by running the following command:

```azurecli-interactive
az k8s-extension delete --cluster-type managedClusters --cluster-name myAKSCluster --resource-group myContainerStorageRG --name azurecontainerstorage
```

You can also use the [`az group delete`](/cli/azure/group) command to delete the resource group and all resources contained in the resource group:

```azurecli-interactive
az group delete --name myContainerStorageRG
```

## Next steps

> [!div class="nextstepaction"]
> [What is Azure Container Storage?](container-storage-introduction.md)
