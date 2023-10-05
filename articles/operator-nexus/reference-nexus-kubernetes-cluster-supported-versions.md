---
title: Supported Kubernetes versions in Azure Operator Nexus Kubernetes service
description: Learn the Kubernetes version support policy and lifecycle of clusters in Azure Operator Nexus Kubernetes service
ms.topic: article
ms.date: 10/04/2023
author: dramasamy
ms.author: dramasamy
ms.service: azure-operator-nexus
---

# Supported Kubernetes versions in Azure Operator Nexus Kubernetes service

This document provides an overview of the versioning schema used for the Nexus Kubernetes service, including the supported Kubernetes versions. It explains the differences between major, minor, and patch versions, and provides guidance on upgrading Kubernetes versions, and what the upgrade experience is like. The document also covers the version support lifecycle and end of life (EOL) for each minor version of Kubernetes.

The Kubernetes community releases minor versions roughly every three months. Recently, the Kubernetes community has [increased the support window for each version from nine months to one year](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/), starting with version 1.19.

Minor version releases include new features and improvements. Patch releases are more frequent (sometimes weekly) and are intended for critical bug fixes within a minor version. Patch releases include fixes for security vulnerabilities or major bugs.

## Kubernetes versions

Kubernetes uses the standard [Semantic Versioning](https://semver.org/) versioning scheme for each version:

```bash
[major].[minor].[patch]

Examples:
  1.24.7
  1.25.4
```

Each number in the version indicates general compatibility with the previous version:

* **Major versions** change when incompatible API updates or backwards compatibility may be broken.
* **Minor versions** change when functionality updates are made that are backwards compatible to the other minor releases.
* **Patch versions** change when backwards-compatible bug fixes are made.

It's considered best practice and recommended staying up to date with the latest available patches. For example, if your production cluster is on **`1.24.4`**, and **`1.24.7`** is the latest available patch version available for the *1.24* series. You should upgrade to **`1.24.7`** as soon as possible to ensure your cluster is fully patched and supported. Further details on upgrading your cluster can be found in the [Upgrading Kubernetes versions](#upgrading-kubernetes-versions) section.

## Nexus Kubernetes release calendar

View the upcoming version releases on the Nexus Kubernetes release calendar.

> [!NOTE]
> To read more about our support policy for Kubernetes versioning, please read section [Kubernetes version support policy](#kubernetes-version-support-policy).

For the past release history, see [Kubernetes history](https://github.com/kubernetes/kubernetes/releases).

|  K8s version | Nexus GA  | End of life | Platform support      |
|--------------|-----------|-------------|-----------------------|
| 1.24         | Jul 2022  | Jul 2023    | Jun 2024              |
| 1.25         | Dec 2022  | Dec 2023    | Nov 2024              |
| 1.26         | Apr 2023  | Mar 2024    | Feb 2025              |
| 1.27*        | Sep 2023  | Jul 2025*   | Jul 2025              |
| 1.28         | Nov 2023  |             |                       |

*\* Indicates the version is designated for Long Term Support*

## Nexus Kubernetes service version components

A Nexus Kubernetes service version is made of two discrete components that are combined into a single representation:

* The Kubernetes version. For example, 1.25.4, is the version of Kubernetes that you deploy in Azure Operator Nexus. These packages are supplied by Azure AKS, including all patch versions that Azure Operator Nexus supports. For more information on Azure AKS versions, see [AKS Supported Kubernetes Versions](../aks/supported-kubernetes-versions.md)
* The [Version Bundle](#version-bundles), which encapsulates the features (add-ons) and the operating system image used by nodes in the Azure Operator Nexus Kubernetes cluster, as a single number. For example, 4.
The combination of these values is represented in the API as the single kubernetesVersion. For example, 1.25.4-2 or the alternatively supported “v” notation: v1.25.4-2.

### Version bundles

By extending the version of Kubernetes to include a secondary value for the patch version, the version bundle, Nexus Kubernetes service can account for cases where the deployment is modified to include extra Operating System related updates. Such updates may include but aren't limited to: updated operating system images, patch releases for features (add-ons) and so on. Version bundles are always backward compatible with prior version bundles within the same patch version, for example, 1.25.4-2 is backwards compatible with 1.25.4-1.

Changes to the configuration of a deployed Azure Operator Nexus Kubernetes cluster should only be applied within a Kubernetes minor version upgrade, not during a patch version upgrade. Examples of configuration changes that could be applied during the minor version upgrade include:

* Changing the configuration of the kube-proxy from using the iptables to ipvs
* Changing the CNI from one product to another

When we follow these principles, it becomes easier to predict and manage the process of moving between different versions of Kubernetes clusters offered by the Nexus Kubernetes service.

We can easily upgrade from any small update in one Nexus Kubernetes version to any small update in the next version, giving you flexibility. For example, an upgrade from 1.24.1-x to 1.25.5-x would be allowed, regardless of the presence of an intermediate 1.24.2-x version.

## Upgrading Kubernetes versions

New versions of the Nexus Kubernetes Service are made available during management bundle upgrades and the available versions are dependent on the cluster's current management bundle. To trigger an update, the `kubernetesVersion` property on the `Microsoft.NetworkCloud/kubernetesClusters` resource is modified.
Input can be in the form of:

* 1.1.1, meaning the latest 1.1.1 version that is, 1.1.1-2.
* 1.1.1-1, which is fully qualified.

> [!NOTE]
> If the current version of the `KubernetesCluster` resource matches the input version’s pattern, no update occurs, meaning that an input of 1.1.1 doesn't trigger the change from 1.1.1-1 to 1.1.1-2, because 1.1.1-1 is already congruent with 1.1.1. To upgrade to 1.1.1-2, it can be fully specified as input.

### Upgrade experience

During a version upgrade, there are two stages: Control plane and Agent pools. These stages attempt to minimize the effect to running workloads by following safe Kubernetes practices.

#### Control plane

The control plane nodes are cycled to the new version one node at a time in a scale-out upgrade process. This means that no interruption to the Kubernetes API or cluster operation is expected during the upgrade.

#### Agent pools

The agent pool nodes undergo a scale-out upgrade process, which involves cordoning, draining, and tearing down one node at a time by default. However, the process can be accelerated using the `maxSurge` option to allow for more rapid replacement of the agent pool. In cases where there are multiple agent pools, each agent pool undergoes a scale-out upgrade independently.

> [!IMPORTANT]
> It's important to note that standard Kubernetes workloads natively cycle to the new nodes when they are drained from the nodes being torn down. Please keep in mind that Nexus Kubernetes service cannot make workload promises for nonstandard Kubernetes behaviors.

## Kubernetes version support policy

Nexus Kubernetes service provides a standardized duration of support for each minor version of Kubernetes that is released. Versions adhere to two different timelines, reflecting:

* Duration of support – How long is a version actively maintained. At the end of the supported period, the version is “End of life.”
* Duration of extended availability / Platfrom support – How long can a version be selected for deployment after End of life.

> [NOTE]
> Platform support policy is a reduced support plan for EOL kubernetes versions. During platform support, customers only receive support from Microsoft for Nexus platform related issues. Any issues related to Kubernetes functionality and components aren't supported. Customers are responsible for upgrading to a supported version of Kubernetes to receive full support.

### Support timeline

Nexus Kubernetes service provides support for 12 months from the initial AKS GA release of a minor version typically. This timeline follows the timing of Azure AKS, which includes a declared Long-Term Support version 1.27.

Supported versions:

* Can be deployed as new Nexus Kubernetes clusters.
* Can be the target of upgrades from prior versions. Limited by normal upgrade paths.
* Are eligible for any provided SLA or agreement for support.
* May have extra patches or Version Bundles within the minor version.

:::image type="content" source="./media/nexus-kubernetes/nexus-kubernetes-service-version-lifecycle.png" alt-text="Diagram of the Nexus Kubernetes service and lifecycle." lightbox="./media/nexus-kubernetes/nexus-kubernetes-service-version-lifecycle.png":::

### End of life (EOL)

End of life (EOL) means no more patch or version bundles are produced.
It's possible the cluster you've set up can't be upgraded anymore because the latest supported versions are no longer available. In this event, the only way to upgrade it's to completely recreate the Nexus Kubernetes cluster using the newer version that is supported.
Unsupported upgrades through `Extended Availability` may be utilized to return to a supported version.

#### Early Termination of support for a release prior to scheduled EOL

As an exceptional measure, Nexus Kubernetes service support for a release can be terminated early. Possible reasons include if there's a defect or discovery that presents a reputational risk to Microsoft or require Microsoft to jeopardize customers on behalf of another. Examples include, but aren't limited to:

* A critical security flaw that impacts individual customers to severe risk but doesn't have cross-customer impacts, within Microsoft’s judgment.
* A critical security flaw that jeopardizes Microsoft infrastructure
* A critical security flaw that introduces threats across customer boundaries.

Nexus Kubernetes service versions that have been marked for early termination:

* Shouldn't be generally available for deployment or upgrade purposes.
* May continue running if there's acceptable risk and possible mitigation put into place in coordination with a specific customer and the customer acceptance of risk
* May be forced to cease operation if the risk is critical or higher. For example, a version that introduces jeopardy across customer boundaries or into Azure infrastructure.

At the end of the Extended Availability of the version, the machine and software images used may no longer be available for use. It's likely that a node of the cluster needing the version’s machine image doesn't have access to retrieve it. Following the end of Extended Availability, the following functions cease operation:

* Deployments of Nexus Kubernetes service using that version
* Upgrades of Nexus Kubernetes service to that version
* Scaling requests or other agent pools for the Nexus Kubernetes service running that version

### Supported `kubectl` versions

You can use one minor version older or newer of `kubectl` relative to your *kube-apiserver* version, consistent with the [Kubernetes support policy for kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

For example, if your *kube-apiserver* is at *1.17*, then you can use versions *1.16* to *1.18* of `kubectl` with that *kube-apiserver*.

To install or update `kubectl` to the latest version, run:

### [Azure CLI](#tab/azure-cli)

```azurecli
az aks install-cli
```

### [Azure PowerShell](#tab/azure-powershell)

```powershell
Install-AzAksKubectl -Version latest
```

---

## Long Term Support (LTS)

AKS provides a Long Term Support (LTS) version of Kubernetes for a two-year period. There's only a single minor version of Kubernetes deemed LTS at any one time.  

|   | Community Support  |Long Term Support   |
|---|---|---|
| **When to use** | When you can keep up with upstream Kubernetes releases | When you need control over when to migrate from one version to another  |
|  **Support versions** | Three GA minor versions | One Kubernetes version (currently *1.27*) for two years  |

The upstream community maintains a minor release of Kubernetes for one year from release. After this period, Microsoft creates and applies security updates to the LTS version of Kubernetes to provide a total of two years of support on AKS.

> [!IMPORTANT]
> Kubernetes version 1.27 will be the first supported LTS version of Kubernetes on AKS. It is not yet available.

## FAQ

### What happens if I don't upgrade my cluster?

If you don't upgrade your cluster, you'll continue to receive support for the Kubernetes version you're running until the end of the support period. After that, you'll no longer receive support for your cluster. You'll need to upgrade your cluster to a supported version to continue receiving support.

### What happens if I don't upgrade my cluster before the end of the extended availability period?

If you don't upgrade your cluster before the end of the extended availability period, you'll no longer be able to upgrade your cluster to a supported version. You'll need to recreate your cluster using a supported version to continue receiving support.

### What happens if I don't upgrade my cluster before the end of the platform support period?

If you don't upgrade your cluster before the end of the platform support period, you'll no longer be able to upgrade your cluster to a supported version. You'll need to recreate your cluster using a supported version to continue receiving support.