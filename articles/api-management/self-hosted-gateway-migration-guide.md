---
title: Self-hosted gateway migration guide | Azure API Management
description: Learn how to migrate to self-hosted gateway v2.
services: api-management
documentationcenter: ''
author: tomkerkhove

ms.service: api-management
ms.topic: article
ms.date: 03/08/2022
ms.author: danlep
---

# Self-hosted gateway migration guide

This article explains how to migrate existing self-hosted gateway deployments to self-hosted gateway v2.

## What's new?

As we strive to make it easier for customers to deploy our self-hosted gateway, we have **introduced a new configuration API** that removes the dependency on Azure Storage, unless you are using [API inspector](api-management-howto-api-inspector.md) or quotas.

This allows customers to more easily adopt, deploy and operate our self-hosted gateway in their existing infrastructure.

We have [introduced new container image tags](how-to-self-hosted-gateway-on-kubernetes-in-production.md#container-image-tag) to let customers choose the best way to try our gateway and deploy it in production.

To help customers run our gateway in production we have extended [our production guidance](how-to-self-hosted-gateway-on-kubernetes-in-production.md) to cover how to autoscale the gateway, and deploy it highly available in your Kubernetes cluster.

Learn more about the connectivity of our gateway, our new infrastructure requirements, and what happens in case of connectivity loss in [this article](self-hosted-gateway-overview.md#connectivity-to-azure).

## Prerequisites

Before you can migrate to self-hosted gateway v2, you need to ensure your infrastructure [meets the requirements](self-hosted-gateway-overview.md#gateway-v2-requirements).

## Migrating to self-hosted gateway v2

Migrating from self-hosted gateway v2 requires a few small steps to be done:

1. [Use the new container image](#container-image)
2. [Use the new configuration API](#using-the-new-configuration-api)
3. [Meet minimal security requirements](#meet-minimal-security-requirements)

### Container Image

Change the image tag in your deployment scripts to use `2.0.0` or above.

Alternatively, you can choose to use one of our other [container image tags](articles/api-management/self-hosted-gateway-overview.md#container-images).

You can find a full list of available tags [here](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list) or find us on [Docker Hub](https://hub.docker.com/_/microsoft-azure-api-management-gateway).

### Using the new configuration API

In order to migrate to self-hosted gateway v2, customers need to use our new Configuration API v2.

Currently, Azure API Management provides the following Configuration APIs for self-hosted gateway:

| Configuration Service | URL | Supported | Requirements |
| --- | --- | --- | --- |
| v2 | `{name}.configuration.azure-api.net` | Yes | [Link](self-hosted-gateway-overview.md#gateway-v2-requirements) |
| v1 | `{name}.management.azure-api.net/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.ApiManagement/service/{name}?api-version=2021-01-01-preview` | No | [Link](self-hosted-gateway-overview.md#gateway-v1-requirements) |

Customer must use the new Configuration API v2 by changing their deployment scripts to use the new URL and meet infrastructure requirements.

> [!IMPORTANT]
> * DNS hostname must be resolvable to IP addresses and the corresponding IP addresses must be reachable.
> This might require additional configuration in case you are using a private DNS, internal VNET or other infrastrutural requirements.

### Meet minimal security requirements

During startup, the self-hosted gateway will prepare the CA certificates that will be used.

Because of that, the gateway container needs to run with at least user ID 1001 and cannot use read-only file system.

When configuring a security context for the container in Kubernetes, the following are required at minimum:

```yaml
securityContext:
  runAsNonRoot: true
  runAsUser: 1001
  readOnlyRootFilesystem: false
```

## Next steps

-   Learn more about [API Management in a Hybrid and Multi-Cloud World](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   Learn more about guidance for [running the self-hosted gateway on Kubernetes in production](how-to-self-hosted-gateway-on-kubernetes-in-production.md)
-   [Deploy self-hosted gateway to Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [Deploy self-hosted gateway to Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
-   [Deploy self-hosted gateway to Azure Arc-enabled Kubernetes cluster](how-to-deploy-self-hosted-gateway-azure-arc.md)
