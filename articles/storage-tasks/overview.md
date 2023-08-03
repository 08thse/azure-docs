---
title: About Azure Storage Tasks
titleSuffix: Azure Storage
description: Description of Azure Storage Tasks goes here  
services: storage
author: normesta

ms.service: storage-tasks
ms.topic: overview
ms.date: 05/10/2023
ms.author: normesta

---

# What is Azure Storage Tasks?

Azure Storage Tasks can perform operations on containers and blobs in Azure Storage accounts based on a set of conditions that you define. Azure Storage Tasks can process millions of objects in a storage account without provisioning additional compute capacity and without requiring you to write code.

> [!IMPORTANT]
> Azure Storage Tasks is currently in PREVIEW and is available in the following regions: \<List regions here\>.
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
> To enroll, see \<sign-up form link here\>.

## Anatomy of a storage task

Azure Storage Tasks is a service that manages *storage tasks*. A storage task defines a set of *conditions*, *operations*, and *assignments*.

| Component | Description |
|---|---|
| Conditions | A *condition* contains a property, a value, and an operator. When the storage task runs, it uses the operator to compare a property with a value to determine whether the condition is met by the target object. For example, a condition might evaluate whether a `creation-time` property of a blob is greater than five days ago. |
| Operations | An operation is the action a storage task performs on each object that meets the defined conditions. Deleting a blob is an example of an operation. |
| Assignments | An assignment identifies a storage account and a subset of objects in that account that the task will target. An assignment also defines when the task runs and where execution reports are stored. |

See any of these articles to learn more about these components:

- [Storage task conditions and operations](storage-task-conditions-operations.md)
- [Storage Task assignments](storage-task-assignment.md)

## How to use a storage task

Define the conditions and operations of a task and then assign that task to one or more storage accounts. You can use metrics, charts, and reports to monitor task runs.

### Define a task

Start by creating a task. To provision a task, you must define at least one condition and one operation. After the task is created, you can edit those conditions and operations or add more of them in the Azure portal by using a visual designer.

See any of these articles to learn more:

- [Create a storage task](storage-task-create.md)
- [Define storage task conditions and operations]( storage-task-conditions-operations-edit.md)

### Assign a task

You can assign a task to any storage accounts that you own. When you create an assignment, you select the storage account, and assign a role to the system-assigned managed identity of the task. The role that you assign must enable that identity to perform the operations that are defined in the task.

> [!NOTE]
> The system-assigned managed identity is created automatically when the task is provisioned.

A storage task can be assigned to a storage account only by an owner of that account. Therefore, if the task that you define is useful to an owner of another storage account, you must grant that user access to the storage task. Then, that user can assign your task to their storage account. You can grant a user access to your storage task by assigning an Azure role to their user identity.

See any of these articles to learn more:

- [Storage task authorization](storage-task-authorization.md)
- [Create and manage a storage task assignment](storage-task-assignment-create.md).

### Monitor task runs

Tasks run asynchronously according to the schedule that you specify in the assignment. An execution report is created when the run completes. That report itemizes the results of the task run on each object that was targeted by the task.

The overview page of the task presents metrics and visualizations that summarize how many objects met the task condition, and the result of the operations attempted by the storage task on each object. The charts enable you to quickly drill into a specific execution instance.

See any of these articles to learn more:

- [Storage task runs](storage-task-runs.md)
- [Monitor Azure Storage Tasks](monitor-storage-tasks.md)
- [Azure Storage Task monitoring data reference](storage-tasks-monitor-data-reference.md)

## Supported Regions

List supported regions here.

## Pricing and billing

A Task invocation charge for every instance in which a task is triggered on an account.

A meter based on the count of objects targeted (for which object properties are accessed, and conditions are evaluated).

The cost of the underlying operations invoked by the task will be passed through to the storage account as part of the account’s overall transaction cost. This should be reflected on the bill with the Task as the user agent for the transaction.

## Next steps

- [Quickstart: Create, assign, and run a storage task by using the Azure portal](storage-task-quickstart-portal.md)
- [Known issues with Azure Storage Tasks](storage-task-known-issues.md)
