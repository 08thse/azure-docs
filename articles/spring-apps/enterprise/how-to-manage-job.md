---
title: Use 
titleSuffix: Azure Spring Apps Enterprise plan
description: Learn how to manage job with the Azure Spring Apps.
author: KarlErickson
ms.author: ninpan
ms.service: spring-apps
ms.topic: how-to
ms.date: 05/12/2024
ms.custom: devx-track-java, devx-track-extended-java
---

# How to manage and use jobs in the Azure Spring Apps Enterprise plan

> [!NOTE]
> Azure Spring Apps is the new name for the Azure Spring Cloud service. Although the service has a new name, you'll see the old name in some places for a while as we work to update assets such as screenshots, videos, and diagrams.

**This article applies to:** ❌ Basic/Standard ✔️ Enterprise

This article shows you how to manage the lifecycle of the job and run it in the Azure Spring Apps Enterprise plan.

## Prerequisites

- An already provisioned Azure Spring Apps Enterprise plan instance. For more information, see [Quickstart: Build and deploy apps to Azure Spring Apps using the Enterprise plan](quickstart-deploy-apps-enterprise.md).

## Create and deploy a job

# [Azure CLI](#tab/azure-cli)

To create a job, run the command `az spring job create` to create job and deploy the job with source code or artifacts of the application.

```
az spring job create --name <job-name>
az spring job deploy --artifact-path <artifact-path> --name <job-name>
```

# [Azure portal](#tab/azure-portal)

To create a job using the Azure portal, follow the steps:

1. Go to your Azure Spring Apps instance. From the navigation pane, select *Jobs* in *Settings*.
1. Select *Create Job* in the *Jobs* blade.
1. Input the job name and other configurations of the job, click *Create* button and wait until the job is created succesfully.
1. Select *Deploy Job* next to *Create Job*, a panel is open.
1. To deploy the job, copy the Azure CLI commands in the panel and run the commands in command line.

---

For public preview, at most 10 jobs can be created per service instance.

## Start and cancel a job execution

# [Azure CLI](#tab/azure-cli)

To start a job execution with Azure CLI, run the following command.

```
az spring job start --name <job-name>
```

If the command runs successfully, the name of the job execution is returned. With `--wait-until-finished true` parameter, the command doesn't return until the job execution finishes.

To query the status of the job execution, use the command, replace the `<execution-name>` with the name returned from start command

```
az spring job execution show --job <job-name> --name <execution-name>
```

To cancel those job executions that are running, run the following command.

```
az spring job execution cancel --job <job-name> --name <execution-name>
```

# [Azure portal](#tab/azure-portal)

1. Open the overview page of the job by selecting the job name in the *Jobs* blade
1. Select *Run* to open settings panel of the job execution
1. Input specific arguments and add environment variables as wanted for this execution, click *Run* button to run the execution.
1. In the *Executions* blade of *Jobs* overview page, find the latest job execution and select *View execution detail* to see the configuration both in job level and execution level

To rerun the job execution, select *Rerun Job* to trigger a new job execution with the same configuration.

To cancel the running job execution, select *Cancel Job*.

---

## Query job execution history

# [Azure CLI](#tab/azure-cli)

To show the execution history, run the command:

```
az spring job execution list --job <job-name>
```

# [Azure portal](#tab/azure-portal)

1. In the *Executions* blade of *Jobs* overview page, latest job executions are listed
1. Select *Cancel Job* to cancel a running job execution
1. Select *View execution detail* to see the configuration both in job level and execution level

---

For the public preview, we retain the latest 10 completed or failed job execution records per job in the history.


## Query job execution logs

For history job executions, you can query the logs in the log analytics using the query in Azure portal:

```
AppPlatformLogsforSpring
| where AppName == '<job-name>' and InstanceName startswith '<execution-name>'
| order by TimeGenerated asc
```

See the step to [set up log analytics workspace]((../basic-standard/quickstart-setup-log-analytics.md?tabs=Azure-Portal#prerequisites)).

For real time log, use the following command:

```
az spring job logs --name <job-name> --execution <execution-name>
```

If there are multiple instances for the job execution, specify `--instance <instance-name>` to view logs of one instance.

## Rerun job execution

# [Azure CLI](#tab/azure-cli)

Using Azure CLI, you can use the same command as the previous one to start the job execution to trigger a new one:

```
az spring job start --name <job-name> --args <argument-value> --envs <key=value>
```

# [Azure portal](#tab/azure-portal)

In Azure Portal, to rerun the job execution:

1. Open the *Executions* blade of *Jobs* overview page
1. Select *Rerun* to trigger a new job execution with the same configuration as the previous one
1. A new job execution is shown in the *Executions* blade

---

## Integrate with managed components

During the public preview, jobs are enabled to integrate seamlessly with Spring Cloud Config Server for efficient configuration management and Tanzu Service Registry for service discovery.

### Spring Cloud Config Server

With Spring Cloud Config Server, configurations or properties that are required by the job can be managed externally on Git repos and then loaded into the job accordingly. After setting up git repo configurations on Spring Cloud Config Server, you need bind the jobs with it as below.

# [Azure CLI](#tab/azure-cli)

1. Bind the job to Spring Cloud Config Server using the command:

```
az spring job create --bind-config-server true --name <job-name>
```

1. For existing jobs binding with Spring Cloud Config Server, you can use the command:
```
az spring config-server bind --job <job-name>
```

# [Azure portal](#tab/azure-portal)

To create the job with Spring Cloud Config Server binded:

1. Go to your Azure Spring Apps instance. From the navigation pane, select *Job*
1. Click *Create Job* button and choose *Spring Cloud Config Server* in the *bind* part
1. Click *Create* button to start creation process

For existing jobs, you can bind them with Spring Cloud Config Server with following steps:

1. Go to your Azure Spring Apps instance. From the navigation pane, select *Spring Cloud Config Server* in *Managed components*.
1. If the component is disabled, select *Manage* to enable it.
1. In the *Settings* tab, configure Spring Cloud Config Server with the right git repository. Click *Validate* and *Apply* to make it take effect.
1. In *Job binding* tab, select *Bind job* and choose the job to apply.
1. After binding successfully, the job name shows in the list.
1. Run the job

---

### Tanzu Service registry

It's pretty common for a job to call an API from a long running app in collaboration for querying some info, notifying, etc. To make the job discover apps running in the same Azure Spring Apps service, you can enable both your apps and jobs binding to managed Service Registry. Please refer to below instructions about how to bind a job with Service Registry.

# [Azure CLI](#tab/azure-cli)

Using the following Azure CLI command:

```
az spring job create --bind-service-registry true
```

# [Azure portal](#tab/azure-portal)

1. Go to your Azure Spring Apps instance. From the navigation pane, select *Service Registry* in *Managed components*.
1. If the component is disabled, select *Manage* to enable it.
1. In *Job binding* tab, select *Bind job* and choose the job to apply.
1. After binding successfully, the job name shows in the list.
1. Run the job

---

When running the job execution, it can access the endpoint of registered apps through Service Registry then.
