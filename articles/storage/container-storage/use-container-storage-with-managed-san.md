---
title: Use Azure Container Storage Preview with Azure Elastic SAN Preview.
description: Configure Azure Container Storage Preview for use with Azure Elastic SAN Preview. Create a storage pool, select a storage class, create a persistent volume claim, and attach the persistent volume to a pod.
author: khdownie
ms.service: storage
ms.topic: how-to
ms.date: 05/01/2023
ms.author: kendownie
ms.subservice: container-storage
---

# Use Azure Container Storage Preview with Azure Elastic SAN Preview
Azure Container Storage is a volume management service built natively for containers that enables customers to create and manage volumes for running stateful container applications. This article shows you how to configure Azure Container Storage to use Azure Elastic SAN Preview as back-end storage for your Kubernetes workloads.

## Prerequisites

- This article requires version 2.0.64 or later of the Azure CLI. If you're using Azure Cloud Shell, the latest version is already installed. If you plan to run the commands locally instead of in Azure Cloud Shell, be sure to run them with administrative privileges.
- You'll need an Azure Kubernetes Service (AKS) cluster with a node pool of at least three virtual machines (VMs), each with at least four virtual CPUs.
- Follow the instructions in [Use Azure Container Storage with AKS](container-storage-aks-quickstart.md) to assign Contributor role to the AKS managed identity and install Azure Container Storage Preview.

## Create a storage pool

First, create a storage pool, which is a logical grouping of storage for your Kubernetes cluster, by defining it in a YAML file. Follow these steps to create a storage pool with managed SAN. 

1. Use your favorite text editor to create a YAML file such as `code acstor-storagepool.yaml`.

2. Paste in the following code. The storage pool `name` value can be whatever you want.

   ```yml
   apiVersion: containerstorage.azure.com/v1alpha1
   kind: StoragePool
   metadata:
     name: managed
     namespace: acstor
   spec:
     poolType:
       san:
         managed: true
     resources:
       limits: {"storage": 5Ti}
       requests: {"storage": 1Ti}
   ```

3. Apply the YAML file to create the storage pool.
   
   ```azurecli-interactive
   kubectl apply -f acstor-storagepool.yaml 
   ```
   
   When storage pool creation is complete, you'll see a message like:
   
   ```output
   storagepool.containerstorage.azure.com/managed created
   ```
   
   You can also run this command to check the status of the storage pool:
   
   ```azurecli-interactive
   kubectl describe sp managed -n acstor
   ```

## Display the available storage classes

When the storage pool is ready to use, you must select a storage class to define how storage is dynamically created when creating persistent volume claims and deploying persistent volumes.

Run `kubectl get sc` to display the available storage classes. You should see a storage class with the same name as the storage pool you just created.

## Create a persistent volume claim

A persistent volume claim (PVC) is used to automatically provision storage based on a storage class. Follow these steps to create a PVC using the new storage class. 

1. Use your favorite text editor to create a YAML file such as `code acstor-pvc.yaml`.

2. Paste in the following code. The PVC `name` value can be whatever you want.

   ```yml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: managedpvc
   spec:
     accessModes:
       - ReadWriteOnce
     storageClassName: managed # or whatever your storage class is named
     resources:
       requests:
         storage: 100Gi
   ```

3. Apply the YAML file to create the PVC.
   
   ```azurecli-interactive
   kubectl apply -f acstor-pvc.yaml
   ```
   
   You should see output similar to:
   
   ```output
   persistentvolumeclaim/managedpvc created
   ```
   
   You can verify the status of the PVC by running the following command:
   
   ```azurecli-interactive
   kubectl describe pvc managedpvc
   ```

Once the PVC is created, it's ready for use by a pod.

## Deploy a pod and attach a persistent volume

Create a pod using Fio (flexible I/O) for benchmarking and workload simulation, and specify a mount path for the persistent volume.

1. Use your favorite text editor to create a YAML file such as `code acstor-pod.yaml`.

2. Paste in the following code.

   ```yml
   kind: Pod
   apiVersion: v1
   metadata:
     name: fiopod
   spec:
     nodeSelector:
       openebs.io/engine: io.engine
     volumes:
       - name: managedpv
         persistentVolumeClaim:
           claimName: managedpvc
     containers:
       - name: fio
         image: nixery.dev/shell/fio
         args:
           - sleep
           - "1000000"
         volumeMounts:
           - mountPath: "/volume"
             name: managedpv
   ```

3. Apply the YAML file to deploy the pod.
   
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
   kubectl describe pvc managedpvc
   ```

5. Check fio testing to see its current status:

   ```azurecli-interactive
   kubectl exec -it fiopod -- fio --name=benchtest --size=800m --filename=/volume/test --direct=1 --rw=randrw --ioengine=libaio --bs=4k --iodepth=16 --numjobs=8 --time_based --runtime=60
   ```

You now have a pod with storage that you can use for your Kubernetes workloads.

## See also

- [What is Azure Container Storage?](container-storage-introduction.md)
