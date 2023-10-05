---
title: Upgrade an Azure Operator Nexus Kubernetes cluster
description: Learn how to upgrade an Azure Operator Nexus Kubernetes cluster to get the latest features and security updates.
author: dramasamy
ms.author: dramasamy
ms.service: azure-operator-nexus
ms.topic: how-to
ms.date: 10/05/2023 
ms.custom: template-how-to-pattern
---

# Upgrade an Azure Operator Nexus Kubernetes cluster

This article provides instructions on how to upgrade an Kubernetes cluster to get the latest features and security updates. Part of the Kubernetes cluster lifecycle involves performing periodic upgrades to the latest Kubernetes version. It's important you apply the latest security releases, or upgrade to get the latest features. This article shows you how to check for, configure, and apply upgrades to your Kubernetes cluster.

> [!NOTE]
> Individual node pool upgrades are not supported. Instead, Nexus offers cluster-wide upgrades, ensuring consistency across all node pools. Also, the node image is upgraded as part of the cluster upgrade when a new version is available.

> [!IMPORTANT]
> No automatic cluster upgrades are performed, users must manually trigger the upgrade operation. For more information, see [Upgrade the cluster](#upgrade-the-cluster).

When you upgrade the Nexus Kubernetes cluster, Kubernetes minor versions can't be skipped. You must perform all upgrades sequentially by major version number. For example, upgrades between *1.14.x* -> *1.15.x* or *1.15.x* -> *1.16.x* are allowed, however *1.14.x* -> *1.16.x* isn't allowed. If your version is significantly out of date, we recommend you recreate your cluster.

## Before you begin

* An Azure Operator Nexus Kubernetes cluster deployed in a resource group in your Azure subscription.
* If you're using Azure CLI, this article requires that you're running the latest Azure CLI version. If you need to install or upgrade, see [Install Azure CLI](./howto-install-cli-extensions.md)
* Understand the version bundles concept. For more information, see [Nexus Kubernetes version bundles](./reference-nexus-kubernetes-cluster-supported-versions.md#version-bundles).

> [!WARNING]
> The cluster upgrade involves a scale-out approach, meaning that at least one additional node is added (or as many nodes as configured in [max surge](#customize-node-surge-upgrade)). If there isn't sufficient capacity available, the upgrade might not succeed.

## Check for available upgrades

The availability of Kubernetes versions for tenant cluster upgrades is relies on the management bundle of the on-prem Nexus instance. Check which Kubernetes releases are available for your cluster using the following steps:

### Azure CLI

The following Azure CLI command returns the available upgrades for your cluster:

```azurecli
az networkcloud kubernetescluster show --name <NexusK8sClusterName> --resource-group <ResourceGroup> --output json --query availableUpgrades
```

Sample output:

```json
[
  {
    "availabilityLifecycle": "GenerallyAvailable",
    "version": "v1.25.4-4"
  },
  {
    "availabilityLifecycle": "GenerallyAvailable",
    "version": "v1.25.6-1"
  },
  {
    "availabilityLifecycle": "GenerallyAvailable",
    "version": "v1.26.3-1"
  }
]
```

### Use the Azure portal

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Navigate to your Nexus Kubernetes cluster.
3. Under **Overview**, select **Available upgrades** tab.
:::image type="content" source="media/nexus-kubernetes/search-kubernetes-service.png" lightbox="media/nexus-kubernetes/search-kubernetes-service.png" alt-text="Screenshot of browsing Nexus Kubernetes service.":::

### Choose a version to upgrade to

The available Nexus Kubernetes upgrade output indicate that there are multiple versions to choose from for upgrading. In this specific scenario, the current cluster is operating on version "v1.25.4-3" Consequently, the available upgrade options include "v1.25.4-4" as well as the latest patch release "v1.25.6-1." Furthermore, a new minor version is also available.

You have the flexibility to upgrade to any of the available versions. However, the recommended course of action is to perform the upgrade to the most recent available `major-minor-patch-versionbundle` version.

## Upgrade the cluster

During the cluster upgrade process, Nexus performs the following operations:

* Add a new buffer control plane node with the specified Kubernetes version to the cluster.
* After the new node has been added, cordon and drain one of the old control plane nodes, ensuring that the workloads running on it are gracefully moved to other healthy control plane nodes.
* After the old control plane node has been completely drained, it is removed, and a new buffer control plane node is added to the cluster.
* This process repeats until all control plane nodes in the cluster have been upgraded.
* After control plane upgrage, add a new buffer worker node (or as many nodes as configured in [max surge](#customize-node-surge-upgrade)) to the cluster that runs the specified Kubernetes version. In cases where there are multiple agent pools, each agent pool undergoes a scale-out upgrade independently.
* [Cordon and drain][kubernetes-drain] one of the old worker nodes to minimize disruption to running applications. If you're using max surge, it [cordons and drains][kubernetes-drain] as many worker nodes at the same time as the number of buffer nodes specified.
* After the old worker node has been completely drained, it is removed, and a new buffer worker node is added to the cluster (or as many nodes as configured in [max surge](#customize-node-surge-upgrade))
* This process repeats until all worker nodes in the cluster have been upgraded.

> [!NOTE]
> The input format for the version is `major.minor.patch` or `major.minor.patch-versionbundle`. The version input must be one of the available upgrade versions. For example, if the current version of the cluster is `1.1.1-1`, valid version inputs are `1.1.1-2` or `1.1.1-x`. While `1.1.1` is a valid format, it won't trigger any update because the current version is already `1.1.1`. To initiate an update, you can specify the complete version with the version bundle, such as `1.1.1-2`. However, `1.1.2` and `1.2.x` are a valid input and will use the latest version bundle available for `1.1.2` or `1.2.x`.

> [!IMPORTANT]
> Ensure that any `PodDisruptionBudgets` (PDBs) allow for at least *one* pod replica to be moved at a time otherwise the drain/evict operation will fail.
> If the drain operation fails, the upgrade operation will fail by design to ensure that the applications are not disrupted. Please correct what caused the operation to stop (incorrect PDBs, lack of quota, and so on) and re-try the operation.

> [!IMPORTANT]
> An upgrade operation might remove customizations if you made customizations to agent nodes.

1. Upgrade your cluster using the `networkcloud kubernetescluster update` command.

```azurecli
az networkcloud kubernetescluster update --name myNexusK8sCluster --resource-group myResourceGroup --kubernetes-version v1.26.3
```

2. Confirm the upgrade was successful using the `show` command.

```azurecli
az networkcloud kubernetescluster show --name myNexusK8sCluster --resource-group myResourceGroup --output json --query kubernetesVersion
```

The following example output shows that the cluster now runs *v1.26.3*:

```output
"v1.26.3"
```

3. Ensure that the cluster is healthy.

```azurecli
az networkcloud kubernetescluster show --name myNexusK8sCluster --resource-group myResourceGroup --output table
```

The following example output shows that the cluster is healthy:

```output
Name                 ResourceGroup          ProvisioningState    DetailedStatus    DetailedStatusMessage             Location
------------------   ---------------------  -------------------  ----------------  --------------------------------  --------------
myNexusK8sCluster    myResourceGroup        Succeeded            Available         Cluster is operational and ready  southcentralus
```

## Customize node surge upgrade

By default, Nexus configures upgrades to surge with one extra worker node. A default value of one for the max surge settings enables Nexus to minimize workload disruption by creating an extra node before the cordon/drain of existing applications to replace an older versioned node. The max surge value can be customized per node pool to enable a trade-off between upgrade speed and upgrade disruption. When you increase the max surge value, the upgrade process completes faster. If you set a large value for max surge, you might experience disruptions during the upgrade process.

For example, a max surge value of 100% provides the fastest possible upgrade process (doubling the node count) but also causes all nodes in the node pool to be drained simultaneously. You might want to use a higher value such as this for testing environments. For production node pools, we recommend a max_surge setting of 33%.

API accepts both integer values and a percentage value for max surge. An integer such as 5 indicates five extra nodes to surge. A value of 50% indicates a surge value of half the current node count in the pool. Max surge percent values can be a minimum of 1% and a maximum of 100%. A percent value is rounded up to the nearest node count. If the max surge value is higher than the required number of nodes to be upgraded, the number of nodes to be upgraded is used for the max surge value.

During an upgrade, the max surge value can be a minimum of 1 and a maximum value equal to the number of nodes in your node pool. You can set larger values, but the maximum number of nodes used for max surge isn't higher than the number of nodes in the pool at the time of upgrade.

> [!IMPORTANT]
> It's important to note that standard Kubernetes workloads natively cycle to the new nodes when they are drained from the nodes being torn down. Please keep in mind that Nexus Kubernetes service cannot make workload promises for nonstandard Kubernetes behaviors.

### Set max surge values

The max surge values must be set during the cluster creation. You can't change the max surge values after the cluster is created. For more information, see `upgradeSettings` in [Create an Azure Operator Nexus Kubernetes cluster](./quickstarts-kubernetes-cluster-deployment-bicep.md).

## Next steps

* Learn more about [Nexus Kubernetes version bundles](./reference-nexus-kubernetes-cluster-supported-versions.md#version-bundles).