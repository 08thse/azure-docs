---
title: Deploy a network function on Azure Kubernetes Services
description: Learn the high-level process to deploy a network function on Advanced Kubernetes services.
author: HollyCl
ms.author: HollyCl
ms.service: azure
ms.topic: quickstart #required; leave this attribute/value as-is
ms.date: 01/31/2024

#CustomerIntent: As a < type of user >, I want < what? > so that < why? >.
---

# Quickstart: Deploy a network function on Advanced Kubernetes Services (AKS)

This quickstart shows you how to deploy various network functions, including SMF, UPF, NRF, NSSF, AMF, MME, and a VNF_Agent in Azure Operator 5G Core.

Use the following Azure CLI commands to deploy the network function:

```azurecli
New-AzResourceGroupDeployment `
-Name <DEPLOYMENT NAME> `
-ResourceGroupName <RESOURCE GROUP> `
-TemplateFile ./releases/2311.0-1/AKS/bicep/<NF NAME>Template.bicep `
-TemplateParameterFile ./releases/2311.0-1/AKS/params/<NF NAME>Params.json `
-resourceName <RESOURCE NAME> –Verbose
```

## Related content

- [Quickstart: Monitor the  status of your Azure Operator 5G Core deployment](how-to-monitor-deployment-status.md)