---
title: Create a placement policy
description: Learn how to create a placement policy.
ms.topic: how-to 
ms.date: 8/10/2021

#Customer intent: As an Azure service administrator, I want control the placement of virtual machines on hosts within a cluster in my private cloud. 

---

# Create a placement policy in Azure VMware Solution

In Azure VMware Solution, clusters in a private cloud are a managed resource. As a result, the cloudadmin role can't make certain changes to the cluster from the vSphere Client, including the management of Distributed Resource Scheduler (DRS) rules.

Placement policies in Azure VMware Solution let you control the placement of virtual machines (VMs) on hosts within a cluster in your private cloud through the Azure portal. When you create a placement policy, it's comprised of a DRS rule in the specified vSphere cluster and additional logic for interoperability with Azure VMware Solution operations.

A placement policy has at least five required components: 

- **Name** - Defines the name of the policy and is subject to the naming constraints of [Azure Resources](../azure-resource-manager/management/resource-name-rules.md).

- **Type** - Defines the type of control you want to apply to the resources contained in the policy.

- **Cluster** - Defines the cluster for the policy. The scope of a placement policy is a vSphere cluster, so only resources from the same cluster may be part of the same placement policy.

- **State** - Defines whether the policy is enabled or disabled. In certain scenarios, a policy might be disabled automatically when a conflicting rule gets created. For more information, see [Considerations](#considerations) below.

- **Virtual machine** - Defines the VMs and hosts for the policy. Depending on the type of rule you create, your policy may require you to specify some number of VMs and hosts. For more information, see [Placement policy types](#placement-policy-types) below.


## Prerequisites

You must have _Contributor_ level access to the private cloud to manage placement policies.



## Placement policy types

### VM-VM policies

**VM-VM** policies specify whether selected VMs should run on the same host or be kept on separate hosts.  In addition to choosing a name and cluster for the policy, **VM-VM** policies requires that you select at least two VMs to assign. The assignment of hosts is not required or permitted for this policy type.

- **VM-VM Affinity** policies instruct DRS to try keeping the specified VMs together on the same host. It's useful for performance reasons, for example.

- **VM-VM Anti-Affinity** policies instruct DRS to try keeping the specified VMs apart from each other on separate hosts. It's useful in scenarios where a problem with one host doesn't affect multiple VMs within the same policy.


### VM-Host policies

**VM-Host** policies specify whether or not selected VMs can run on selected hosts.  To avoid interference with platform-managed operations such as host maintenance mode and host replacement, **VM-Host** policies in Azure VMware Solution are always preferential (also known as "should" rules). Accordingly, **VM-Host** policies may not be honored in certain scenarios. For more information, see [Monitor the operation of a policy](#monitor-the-operation-of-a-policy) below.

Certain platform operations will dynamically update the list of hosts defined in **VM-Host** policies. For example, when you delete a host that is a member of a placement policy, the host is removed if more than one host is part of that policy. Also, if a host is part of a policy and needs to be replaced as part of a platform-managed operation, the policy is updated dynamically with the new host.

In addition to choosing a name and cluster for the policy, a **VM-Host** policy requires that you select at least one VM and one host to assign to the policy.

- **VM-Host Affinity** policies instruct DRS to try running the specified VMs on the hosts defined.

- **VM-Host Anti-Affinity** policies instruct DRS to try running the specified VMs on hosts other than those defined.




## Considerations



### Cluster scale in

Azure VMware Solution attempts to prevent certain DRS rule violations from occurring when performing cluster scale-in operations.

You can't to have a VM-VM Anti Affinity policy with more VMs than the number of hosts in a cluster. If removing a host would result in fewer hosts in the cluster than VMs, you'll receive an error preventing the operation. You can remediate this by first removing VMs from the rule and then removing the host from the cluster.


### Rule conflicts

If DRS rule conflicts are detected when you create a VM-VM policy, it results in that policy being created in a disabled state following standard [VMware DRS Rule behavior](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-69C738B6-5FC8-4189-9CB5-DD90A5A05979.html). For more information on viewing rule conflicts, see [Monitor the operation of a policy](#monitor-the-operation-of-a-policy) below.



## Create a policy

1. In your Azure VMware Solution private cloud, under **Manage**, select **Placement policies** > **Add placement policy**.

   :::image type="content" source="media/placement-policies/add-placement-policy-1.png" alt-text="Screenshot showing " lightbox="media/placement-policies/add-placement-policy-1.png":::

1. Provide a descriptive name, select the policy type, and then select the cluster where the policy is being created.

   :::image type="content" source="media/placement-policies/add-placement-policy-2.png" alt-text="Screenshot showing the Create new policy options.":::

   >[!NOTE]
   >You may also select the Cluster from the Placement Policy overview pane before selecting ‘+ Add placement policy’




1.	If desired, you may deselect the Enabled checkbox. This will create the policy and underlying DRS rule but the policy actions will be ignored until the policy is enabled.
1.	select ‘+ Add virtual machine’
1.	On the Select Virtual Machines pane, select the virtual machine to include in the policy by selecting the checkbox next to the virtual machine name in the list. Select multiple as required or desired.
1.	select ‘+ Add virtual machine’ at the top of the Select Virtual Machines pane.



!NOTE: See placement policy types section for more information on requirements by policy type.



1.	If  you see an ‘+ Add host’ option, then your policy type requires the selection of a host. 
a.	select ‘+ Add host’ to open the Select Hosts pane.
b.	On the Select Hosts pane, select the host to include in the policy by selecting the checkbox next to the host name in the list. Select multiple as desired.


!NOTE: Associated policies & virtual machines blurb next to host - explain 



c.	select Select at the top of the Select Hosts pane.
11.	select Next: Review and Create.
12.	Review the summary page. select Back: Basics to make any desired changes, otherwise select Create Policy.  






## Edit a policy

Editing a policy allows you to unassign existing resources in the policy, add new resources to the policy, or change the state of a policy.


### Change the policy state

In order to change the state of a policy to enabled or disabled, perform these steps:
1.	From the placement policy overview page, for the policy you would like to edit, select on the action wheel dropdown     and then select Edit.
2.	Uncheck the Enabled checkbox to disable the policy or check it to disable the policy.
3.	select Next: Review and Update.
 
4.	Review the summary page. select Back: Basics to make any desired changes, otherwise select Update Policy.  
!TIP: You can disable a policy directly from Placement Policies overview page by selecting on the action wheel and selecting Disable. To enable a policy, you must edit the policy as outlined above. 






### Update the resources in a policy

In order to add new resources to a policy or remove existing ones, perform the following steps:
1.	From the placement policy overview page, for the policy you would like to edit, select on the action wheel dropdown  and then select Edit.
2.	To remove an existing resource from a policy, select the checkbox next to the resource’s name, then select Unassign. Choose multiple as desired.
3.	To add a new resource to the policy, select Edit virtual machine or Edit host, select the resource you’d like to add, then select Save & Close.
4.	select Next: Review and Update.
5.	Review the summary page. select Back: Basics to make any desired changes, otherwise select Update Policy.  





## Delete a policy

In order to delete a placement policy and its corresponding DRS rule, perform the following steps:
1.	From the placement policy overview page, for the policy you would like to delete, select on the action wheel dropdown and then select Delete. 
2.	select Delete on the confirmation message.



## Monitor the operation of a policy

Use the vSphere Client to monitor the operation of a placement policy's corresponding DRS rule. 

As a holder of the cloudadmin role, you can view, but not edit, the DRS rules created by a placement policy on the cluster’s Configure tab under VM/Host Rules. This allows you to view some additional information, such as whether the DRS rules are in a conflict state.

Additionally, you can monitor various DRS rule operations, such as recommendations and faults, from the cluster’s Monitor tab.





## Next steps
