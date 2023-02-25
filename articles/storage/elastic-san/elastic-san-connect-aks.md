---
title: Connect an Azure Elastic SAN (preview) volume to an AKS cluster.
description: Learn how to connect to an Azure Elastic SAN (preview) volume an AKS cluster.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 02/24/2023
ms.author: rogarana
ms.subservice: elastic-san
---

# Connect Elastic SAN volumes to AKS

For AKS clusters connecting to Elastic SAN, use the [Kubernetes iSCSI CSI driver](https://github.com/kubernetes-csi/csi-driver-iscsi) enabled on your cluster to connect Elastic SAN volumes. With this driver, you can access volumes on your Elastic SAN by creating persistent volumes on your AKS cluster, and then attaching the Elastic SAN volumes to the persistent volumes. 

## About the driver

The iSCSI CSI driver is an open source project that allows you to connect to a Kubernetes cluster over iSCSI. Since the driver is an open source project, Microsoft won't provide support from any issues stemming from the driver, itself.

The Kubernetes iSCSI CSI driver is available on GitHub:

- [Kubernetes iSCSI CSI driver repository](https://github.com/kubernetes-csi/csi-driver-iscsi)
- [Readme](https://github.com/kubernetes-csi/csi-driver-iscsi/blob/master/README.md)
- [Report iSCSI driver issues](https://github.com/kubernetes-csi/csi-driver-iscsi/issues)

### Licensing

The iSCSI CSI driver for Kubernetes is [licensed under the Apache 2.0 license](https://github.com/kubernetes-csi/csi-driver-iscsi/blob/master/LICENSE).

## Get started

### Driver installation

First, install the Kubernetes iSCSI CSI driver on your cluster. You have to perform a local install since a few code changes are required for the driver to connect to Elastic SAN volumes.

First, clone the driver repo:

```
git clone https://github.com/kubernetes-csi/csi-driver-iscsi.git 
```

Since Elastic SAN doesn't currently support dynamic discovery used in this driver. The following code changes in the driver are required to add volumes statically.

Make the following modifications to pkg/lib/iscsi/iscsi/iscsi.go:

First, add a new function for static discovery: 

```
// Add a new function to make static discovery
func (c *Connector) discoverTargetStatically(targetIqn string, iFace string, portal string) error {
  err := CreateDBEntry(targetIqn, portal, iFace, c.DiscoverySecrets, c.SessionSecrets)
  if err != nil {
    debug.Printf("Error creating db entry: %s\n", err.Error())
    return err
  }
  return nil
}
```

Then, make the following change so that you can use the function you created: 

Replace: 
```
if err := c.discoverTarget(targetIqn, iFace, portal); err != nil
```
with:
```
if err := c.discoverTargetStatically
```

After the modifications, run the remaining install scripts:

```
cd csi-driver-iscsi 

./deploy/install-driver.sh master local
```

After deployment, check the pods status to verify that the driver installed.

```bash
kubectl -n kube-system get pod -o wide -l app=csi-iscsi-node
```

### Volume information

To connect an Elastic SAN volume to an AKS cluster, you need the volume's StorageTargetIQN, StorageTargetPortalHostName, and StorageTargetPortalPort.

You may get them with the following Azure PowerShell command:

```azurepowershell
Get-AzElasticSanVolume -ResourceGroupName $resourceGroupName -ElasticSanName $sanName -VolumeGroupName $searchedVolumeGroup -Name $searchedVolume 
```

You may also get them with the following Azure CLI command:

```azurecli
az elastic-san volume show --elastic-san-name --name --resource-group --volume-group-name
```

### Cluster configuration

Once you've retrieved your volume's information, you need to create a few yml files for your new resources on your AKS cluster.

First, create a storageclass.yml file. This file defines your persistent volume's storageclass.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: manual
provisioner: manual
```

Then, create a pv.yml file. This file defines your [persistent volume](../../aks/concepts-storage.md#persistent-volumes). In the following example, replace `yourTargetPortal`, `yourTargetPortalPort`, and `yourIQN` with the values you collected earlier, then use the example to create a pv.yml file. If you need more than 1 gibibyte of storage and have it available, replace `1Gi` with the amount of storage you require.

```yml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: iscsiplugin-pv
  labels:
    name: data-iscsiplugin
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 1Gi
  csi:
    driver: iscsi.csi.k8s.io
    volumeHandle: iscsi-data-id
    volumeAttributes:
      targetPortal: "yourTargetPortal:yourTargetPortalPort"
      portals: "[]"
      iqn: "yourIQN"
      lun: "0"
      iscsiInterface: "default"
      discoveryCHAPAuth: "false"
      sessionCHAPAuth: "false"
```

Next, create a [persistent volume claim](../../aks/concepts-storage.md#persistent-volume-claims). Use the storage class we defined earlier with the persistent volume we defined. The following is an example of what your pvc.yml file might look like:

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: iscsiplugin-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: manual
  selector:
    matchExpressions:
      - key: name
        operator: In
        values: ["data-iscsiplugin"]
```

Finally, create a [pod](../../aks/concepts-clusters-workloads.md#pods). The following is an example of what your pod.yml file might look like:

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - image: maersk/nginx
      imagePullPolicy: Always
      name: nginx
      ports:
        - containerPort: 80
          protocol: TCP
      volumeMounts:
        - mountPath: /var/www
          name: iscsi-volume
  volumes:
    - name: iscsi-volume
      persistentVolumeClaim:
        claimName: iscsiplugin-pvc
```

### Deployment

Now that you've created all the necessary files, create the persistent volume with the following command:

```bash
kubectl apply -f pathtoyourfile/pv.yaml
```

Then, create a persistent volume claim.

```bash
kubectl apply -f pathtoyourfile/pvc.yaml
```

To verify your PersistentVolumeClaim is created and bound to the PersistentVolume, run the following command: 

```bash
kubectl get pvc pathtoyourfile 
```

When you've confirmed your persistent volume claim has been created, create the pod.

```bash
kubectl apply -f pathtoyourfile/pod.yaml
```

To verify your Pod was created run the following command: 

```bash
kubectl get pods  
```

You've now successfully connected an Elastic SAN volume to your AKS cluster.
