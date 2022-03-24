---
title: 
description: 
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 03/22/2022
ms.author: cshoe
---

# Heath probes in Azure Container Apps

Health probes in Azure Container Apps are based on [Kubernetes health probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). You can set up probes using either TCP or HTTP.

HTTP probes allow you to implement custom logic to check the status of application dependencies before reporting a healthy status.

Container Apps support the following probes:

- [Liveness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-a-liveness-command): Reports the overall heath of your replica.
- [Startup](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-startup-probes): Delay reporting on a liveness state for slower apps with a startup probe.
- [Readiness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-readiness-probes): Signals that a replica is ready to accept traffic.

The exec probe is not supported.