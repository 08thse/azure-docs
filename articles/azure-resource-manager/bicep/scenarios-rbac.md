---
title: Create Azure RBAC resources by using Bicep
description: Describes how to create role assignments and role definitions by using Bicep.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 11/10/2021
---
# Create Azure RBAC resources by using Bicep

Azure has a powerful role-based access control (RBAC) system. By using Bicep, you can programmatically define your RBAC role assignments and role definitions.

<!-- TODO move all code samples into the correct repo -->

## Role assignments

To define a role assignment, create a resource with type [`Microsoft.Authorization/roleAssignments`](/azure/templates/microsoft.authorization/roleassignments?tabs=bicep). A role definition has multiple properties, including a scope, a name, a role definition ID, a principal ID, and a principal type.

### Scope

Role assignments are [extension resources](scope-extension-resources.md), which means they apply to another resource. The following example shows how to create a storage account and a role assignment scoped to that storage account:

::: code language="bicep" source="code/scenarios-rbac/scope.bicep" highlight="17" :::

If you don't explicitly specify the scope, Bicep uses the file's `targetScope`. In the following example, no `scope` property is specified, so the role assignment applies to the subscription:

::: code language="bicep" source="code/scenarios-rbac/scope-default.bicep" highlight="4" :::

### Name

A role assignment's resource name must be a globally unique identifier (GUID). It's a good practice to create a GUID that uses the scope, principal ID, and role ID together, as in the example above. This helps you to ensure that each role assignment has a unique name.

> [!TIP]
> Use the `guid()` function to help you to create a deterministic GUID for your role assignment names.

### Role definition ID

You need to specify the role to assign. This can be a built-in role definition or a custom role definition. To use a built-in role definition, [find the appropriate role definition ID](/azure/role-based-access-control/built-in-roles). For example, the *Contributor* role has a role definition ID of `b24988ac-6180-42a0-ab88-20f7382dd24c`.

When you create the role assignment resource, you need to specify a fully qualified resource ID. Built-in role definition IDs are subscription-scoped resources. It's a good practice to use an `existing` resource to refer to the built-in role, and to access its fully qualified resource ID by using the `.id` property:

::: code language="bicep" source="code/scenarios-rbac/built-in-role.bicep" highlight="3-7, 12" :::

### Principal ID and principal type

The `principalId` property must be set to a GUID that represents the Azure Active Directory (Azure AD) identifier for the principal.

The `principalType` property specifies whether the principal is a user, a group, or a service principal. Managed identities are a form of service principal.

> [!TIP]
> It's important to set the `principalType` property when you create a role assignment in Bicep. Otherwise, you might get intermittent deployment errors, especially when you work with service principals and managed identities.

The following example shows how to create a user-assigned managed identity and a role assignment:

::: code language="bicep" source="code/scenarios-rbac/managed-identity.bicep" highlight="15-16" :::

## Custom role definitions

To create a custom role definition, define a resource of type `Microsoft.Authorization/roleDefinitions`. See the [Create a new role def via a subscription level deployment](https://azure.microsoft.com/resources/templates/create-role-def/) quickstart for an example.
<!-- TODO submit a PR to add a Bicep file to this quickstart -->

> [!NOTE]
> Some services manage their own role definitions and assignments. For example, Azure Cosmos DB maintains its own [`Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments`](/azure/templates/microsoft.documentdb/databaseaccounts/sqlroleassignments?tabs=bicep) and [`Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions`](/azure/templates/microsoft.documentdb/databaseaccounts/sqlroledefinitions?tabs=bicep) resources. Refer to the specific service's documentation for more information.

## Related resources

- Resource documentation
  - [`Microsoft.Authorization/roleAssignments`](/azure/templates/microsoft.authorization/roleassignments?tabs=bicep)
  - [`Microsoft.Authorization/roleDefinitions`](/azure/templates/microsoft.authorization/roledefinitions?tabs=bicep)
- [Extension resources](scope-extension-resources.md)
- Scopes
  - [Resource group](deploy-to-resource-group.md)
  - [Subscription](deploy-to-subscription.md)
  - [Management group](deploy-to-management-group.md)
  - [Tenant](deploy-to-tenant.md)
- Quickstart templates <!-- TODO verify these all have Bicep files -->
  - [Create a new role def via a subscription level deployment](https://azure.microsoft.com/resources/templates/create-role-def/)
  - [Assign a role at subscription scope](https://azure.microsoft.com/resources/templates/subscription-role-assignment/)
  - [Assign a role at tenant scope](https://azure.microsoft.com/resources/templates/tenant-role-assignment/)
  - [Create a resourceGroup, apply a lock and RBAC](https://azure.microsoft.com/resources/templates/create-rg-lock-role-assignment/)
