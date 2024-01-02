---
title: Runtime environment in Azure Automation
description: This article provides an overview on runtime environment in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 01/02/2024
ms.topic: conceptual
ms.custom:
---

# Runtime environment in Azure Automation

This article provides an overview on Runtime environment, scope and its capabilities.

## About Runtime environment

**Runtime environment** enables you to configure the job execution environment and provides the flexibility to choose the runtime language and runtime version according to your requirements. It's the single source of truth to define and manage the environment in which a job is executed. Every runbook has two components:

- Script code
- Runtime environment - defines the Runtime language, Runtime version and Packages required during job execution.

You can change these components independently without impacting the other.

> [!NOTE]
> You can associate each runbook with a single Runtime environment. However, a Runtime environment could be linked to multiple runbooks.

## Switch between new and old experience

While the new Runtime environment experience is recommended, you can also switch to the default experience anytime. Learn more on [how to toggle between the two experiences](manage-runtime-environment.md#switch-between-runtime-environment-and-old-experience).

### Limitations
 Changes in runbooks would persist between old and new experience. For example if runbook is patched with runtime environment in new experience, customer should revert the updates made to runbooks before switching to old experience to avoid runbook execution in runtime environment.


## Components of Runtime environment

Runtime environment captures the following details about the job execution environment:

1. **Language** - the scripting language targeted for runbook execution. For example, PowerShell and Python.
1. **Runtime version** - the version of the language selected for runbook execution. For example - PowerShell 7.2 and Python 3.8.
1. **Packages** - the packages are the assemblies and the *.dll* files that you import and required by runbooks for execution. There are two types of packages supported for Runtime environment.
    
   |**Package Types** | **Description** |
   |---------|---------|
   |**Default packages**  | The packages enable you to manage Azure resources. For example, Az PowerShell 8.0.0, Azure CLI 2.52.0|
   |**Customer-provided packages** | These are custom packages that are required by runbooks during execution. The packages can be from: </br> - Public gallery: PSGallery, pypi </br> - Self-authored. |

> [!NOTE]
> Azure CLI commands are supported (public preview) in runbooks associated with PowerShell 7.2 Runtime environment. Azure CLI commands version 2.52.0 is available as a default package in PowerShell 7.2 Runtime environment.

## System-generated Runtime environments

Azure Automation creates system-generated Runtime environments based on the Runtime language, version, and packages/modules present in your Azure Automation account. There are six system-generated Runtime environments:

- PowerShell-5.1
- PowerShell-7.1
- PowerShell-7.2
- Python-2.7
- Python-3.8
- Python-3.10

You can't edit these Runtime environments. However, any changes that are made in Modules/Packages for the Automation account are automatically reflected in these system-generated Runtime environments. 

> [!NOTE]
> Packages present in System-generated Runtime environments are unique to your Azure Automation account and might vary across different accounts. 


## Key Benefits

- **Granular control** - enables you to configure the script execution environment by choosing the Runtime language, its version, and dependent modules.
- **Runbook update** - allows easy portability of runbooks across different runtime versions by updating runtime environment of runbooks to keep pace with the latest PowerShell and Python releases. You can test updates before publishing them to production.
- **Module management** - enables you to test compatibility during module updates and avoid unexpected changes that might affect the execution of their production scenarios.
- **Rollback capability** - allows you to easily revert runbook to a previous runtime environment. In case a runbook update introduces issues or unexpected behavior.
- **Streamlined code** - allows you to organize your code easily by linking runbooks to different runtime environments without the need to create multiple Automation accounts.

## Known limitations

- Runtime environment is currently supported in all Public regions except Central India, Germany North, Italy North, Israel Central, Poland Central, UAE Central, and Government clouds.
- Currently only cloud jobs are supported for runbooks linked to Runtime environment. For Hybrid jobs, ensure to install the modules required for runbook execution on Hybrid Runbook Worker
- PowerShell Workflow, Graphical PowerShell, and Graphical PowerShell Workflow runbooks only work with System-generated PowerShell-5.1 runtime environment.
- RBAC permissions cannot be assigned to Runtime environment.
- Runtime environment can't be configured through Azure Automation extension for Visual Studio Code.
- Deleted runtime environments cannot be recovered.  
- The feature is only supported through Azure portal and REST API.


## Next steps

* To work with runbooks and Runtime environment, see [Manage Runtime environment](manage-runtime-environment.md).
* For details of PowerShell, see [PowerShell Docs](/powershell/scripting/overview).

