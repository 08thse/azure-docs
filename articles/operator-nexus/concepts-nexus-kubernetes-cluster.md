---
title: "Azure Operator Nexus: Nexus Kubernetes cluster"
description: Introduction ou to Nexus Kubernetes cluster.
author: jashobhit
ms.author: shobhitjain
ms.service: azure-operator-nexus
ms.topic: conceptual
ms.date: 06/28/2023
ms.custom: template-concept
---

# Nexus Kubernetes cluster overview

This article describe core concepts of Nexus Kubernetes Cluster, a managed Kubernetes service that you can use to deploy and operate containerized applications on [Azure Operator Nexus Platform](./overview.md)

## What is Kubernetes?

Kubernetes is a rapidly evolving platform that manages container-based
applications and their associated networking and storage components.
Kubernetes focuses on the application workloads, not the underlying
infrastructure components. It provides a declarative approach to
deployments, backed by a robust set of APIs for management operations.
See [What is Kubernetes](../aks/concepts-clusters-workloads.md#what-is-kubernetes)
to learn about Kubernetes.

## Nexus Kubernetes cluster

Nexus Kubernetes cluster (NKS) is an Operator Nexus version of Kubernetes for on-premises use. It is optimized to automate creation of containers to run tenant network function workloads.

Like any Kubernetes cluster, Nexus Kubernetes cluster has two
components:

* Control plane: provides core Kubernetes services and orchestration of
application workloads.

* Nodes: To run your applications and supporting services, you need a Kubernetes node. 
Each NKS cluster has at least one node, a virtual machine (VM), that runs the [Kubernetes node components](../aks/concepts-clusters-workloads.md#nodes).
There are two different node pools in Nexus Kubernetes Clusters 

     * System node pools host critical system pods. 
     * User node pools host application pods. 

However,application pods can be scheduled on system node pools if user wants
only one node pool in their cluster. Every Nexus Kubernetes Cluster must
contain at least one system node pool with at least one node.


## Nexus Kubernetes Cluster Addons

Nexus Kubernetes Cluster Addons is a feature of the Nexus platform that allows customers to enhance their Nexus Kubernetes clusters with additional packages or features. The addons are categorized into two types: required and optional.

* Required Addons: These addons are automatically deployed into provisioned Nexus Kubernetes clusters. Core addons such as Calico, MetalLB, Nexus Storage CSI, IPAM plugins, metrics-server, node-local-dns, Arc for Kubernetes, 
and Arc for Servers are included by default when clusters are created. The successful completion of the cluster provisioning process depends on these addons being installed successfully. If a required addon installation fails and cannot be fixed,
the cluster is designated as failed.

* Optional Addons: These are supplementary resources associated with a Kubernetes Cluster resource. Nexus Kubernetes Cluster customers can choose to activate or deactivate these addons on demand. 
Supplementary features like centralized local image caching within the cluster for disconnected scenarios. Customers install optional addons to access extra features. The status, health, 
and version of each required and optional addon can be monitored within Azure Resource Manager.

Addons are installed once and only updated or upgraded when the customer upgrades the Nexus Kubernetes cluster. This setup enables customers to apply critical production hotfixes without interference from the nc-aks-operator, which could otherwise overwrite their cluster modifications.

## Nexus Available Zones

Nexus has established a golden configuration that delineates distinct Zones for various hardware racks. For instance, in a configuration of 400G with 8 racks, 8 Zones will be configured. 
Each Zone comprises a pair of management servers with redundancy and a collection of compute servers that function as a resource pool. Fully redundant Top-of-Rack (ToR) switches are implemented to ensure transport layer resilience. 
Different zone works as independant group of resource in case of software upgrade or failover, to provide better availability, that's the reason we call it Available Zone.

## Nexus Agent pool and scalability

The Nexus Agent pool consists of a collection of virtual machines (VMs) that function as worker nodes within a Nexus Kubernetes cluster. Each VM in a Nexus Agent pool adheres to a uniform configuration, such as CPU, memory, disk, etc. Once an Agent pool is established, the number of VMs within it remains fixed. To scale the capacity of a Nexus Kubernetes cluster, additional Agent pools can be created and integrated into the existing cluster. In other words, the Nexus Agent pool supports horizontal scaling by allowing the addition or removal of Agent pools within the Nexus Kubernetes cluster.

## Failure domain

Operator Nexus ensures that the Nexus Kubernetes Cluster nodes are
distributed across failure servers and failure domains (physical racks). This distribution is done in a way that improves the resilience and availability of the
cluster. Operator Nexus uses Kubernetes affinity rules to schedule
nodes in specific zones. This ensures that the underlying VMs aren't placed on
the same physical server or in the same failure domain, improving the cluster's
fault tolerance. The utilization of the failure domains is
especially advantageous when operators have diverse performance
requirements for racks. Or when they aim to guarantee that certain workloads
remain isolated in specific racks.

## Next steps

* [Guide to deploy Nexus kubernetes cluster](./quickstarts-kubernetes-cluster-deployment-bicep.md)
* [Supported Kubernetes versions](./reference-nexus-kubernetes-cluster-supported-versions.md)
