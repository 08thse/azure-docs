---
title: What is Microsoft Dev Box?
description: Microsoft Dev Box gives you self-service access to high-performance, preconfigured, and ready-to-code cloud-based workstations.
services: dev-box
ms.service: dev-box
ms.topic: overview
ms.author: rosemalcolm
author: RoseHJM
ms.date: 03/21/2022
adobe-target: true
---

# What is Microsoft Dev Box Preview?

Microsoft Dev Box gives you self-service access to high-performance, preconfigured, and ready-to-code cloud-based workstations called Dev Boxes. You can set up Dev Boxes with the tools, source code, and pre-built binaries specific to your project, so you can immediately start work. Whether you’re a developer, tester, or QA professional, you can use Dev Boxes in your day-to-day workflows. You can even move between projects or tasks by using multiple Dev Boxes. 

The Dev Box service was designed with three distinct personas in mind: DevCenter Owners, Project Admins, and Dev Box Users. DevCenter Owners create and manage DevCenters, top-level Dev Box resources that represent the units of organization within an enterprise. They work with Project Admins to create projects and define images for Dev Boxes. Dev box image definitions can use any developer IDE, SDK, or internal tool that runs on Windows. Project admins organize Dev Box Definitions into Pools for their projects and manage access for Dev Box Users. As a Dev Box User, you can select one or more Dev Boxes from the Dev Box Pools that a Project Admins gives you access to. 

Dev Box bridges the gap between development teams and IT, bringing control of project resources closer to the DevCenter owners and project admins. 

## Key capabilities
### For Dev Box Users
- **Get started quickly** 
    - Create new Dev Boxes from a predefined pool whenever you need them and delete them when you are done. 
    - Use separate Dev Boxes for separate projects or tasks. 
- **Handle demanding workloads** 
    - With a range of SKUs from 4 to 32 cores and up to 128 GB of memory, Dev Boxes can scale to meet demanding workloads.
- **Use multiple Dev Boxes to isolate and parallelize work** 
    - Tasks which take considerable time, like a full re-build before submitting a PR can run in the background while you create a new Dev Box to start the next task. 
    - You can safely test changes in your code, or make significant edits without affecting your primary workspace.
- **Access from anywhere** 
    - Dev boxes can be accessed from any device and from any OS. Use a web browser while on the road or remote desktop from your Windows, Mac, or Linux desktop. 

### For Project Admins and developer teams
**Use Dev Box Pools to separate projects** 
- Create Dev Box Pools, add appropriate Dev Box Definitions, and assign access for only Dev Box Users working on those specific projects. 
- Each Pool brings together an SKU, a custom image, and a network configuration that automatically joins the Dev Box to your native Azure Active Directory (AAD) or Active Directory domain. This combination gives teams flexibility to define specific development environments for any scenario.

**Control costs**
- Dev Box brings cost control within the reach of Project Admins. 
- Use start/stop schedules for individual Dev Box Pools to make sure Dev Boxes are available at the start of day and are only running when needed. 
- Hibernation ensures that stopping a Dev Box will not cause the loss of anyone’s work.

**Streamline collaboration**
- Dev Boxes are deployed in Dev Box User’s local region and connected via Azure’s Global Network, so all your users get the benefit of a high-fidelity remote experience and gigabit connection speed for transferring data.

**Team scenarios**
- Create Dev Boxes for various roles on a team. Standard Dev Boxes might be configured with admin rights, giving full-time developers greater control, while more restricted permissions are applied for contractors.

### For IT admins and DevCenter Owners
**Manage Dev Boxes like any other device**
- Manage Dev Boxes enrolled in Intune like any other Windows 365 device on your network. Dev Boxes are managed with Microsoft Endpoint Manager alongside the other deployed devices. 
- Keep all Windows devices up to date by using Intune’s expedited quality updates to deploy zero-day patches across your organization. 
- If a Dev Box is compromised, you can isolate it while helping the Dev Box User get back up and running on a new Dev Box.

**Provide secure access in a secure environment**
- Access controls in AAD enable you to organize access by project or user type. You can automatically join Dev Boxes natively to an ADD or Active Directory domain, set conditional access polices that require users to connect via a compliant device, require multi-factor authentication (MFA) sign in, or configure risk-based sign in polices for Dev Boxes that access sensitive source code and customer data.



## Next steps

Start using Microsoft Dev Box:
- [Quickstart: Create a Dev Box Pool](./quickstart-create-dev-box-pool.md)
- [Quickstart: Create a Dev Box](./quickstart-create-dev-box.md)

