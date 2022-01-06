---
title: Administrator guide | Microsoft Docs
description: This guide helps administrators who create and manage lab plans by using Azure Lab Services. 
ms.topic: how-to
ms.date: 01/04/2020
---

# Azure Lab Services - Administrator guide

Information technology (IT) administrators who manage a university's cloud resources are ordinarily responsible for setting up the lab plan for their school.

[!INCLUDE [preview note](./includes/lab-services-new-update-note.md)]

After they've set up a lab plan, administrators or educators create the labs that are associated with the plan. This article provides a high-level overview of the Azure resources that are involved and the guidance for creating them.

![Diagram of a high-level view of Azure resources in a lab plan.](./media/administrator-guide/high-level-view.png)

- Labs are hosted within an Azure subscription that's owned by Azure Lab Services.
- Lab plans, a shared image gallery, and image versions and are hosted within your subscription.
- If using advanced networking, the virtual network and network-related resources for lab VMs are hosted within your subscription.
- You can have your lab plan and the shared image gallery in the same resource group. In this diagram, they are in different resource groups.

For more information about the architecture, see [Labs architecture fundamentals](./classroom-labs-fundamentals.md).

## Subscription

Your university might have one or more Azure subscriptions. You use subscriptions to manage billing and security for all Azure resources and services that are used within it, including lab plans.

The relationship between a lab plan and its subscription is important because:

- Billing is reported through the subscription that contains the lab plan.
- You can grant users in the subscription's Azure Active Directory (Azure AD) tenant manage Azure Lab Services lab plans and labs. You can add a user as a lab plan owner, lab plan contributor, lab creator, or lab owner. For more information about built-in RBAC roles, see [Manage identity](#rbac-roles).

Labs virtual machines (VMs) are managed and hosted for you within a subscription that's owned by Azure Lab Services.

## Resource group

A subscription contains one or more resource groups. Resource groups are used to create logical groupings of Azure resources that are used together within the same solution.  

When you create a lab plan, you must configure the resource group that contains the lab plan. Name your resource group carefully.  Labs are grouped by resource group in the [Azure Lab Services portal](https://labs.azure.com).

A resource group is also required when you create a [shared image gallery](#shared-image-gallery). You can place your lab plan and shared image gallery in the same resource group or in two separate resource groups. You might want to take this second approach if you plan to share the image gallery across various solutions.

We recommend that you invest time up front to plan the structure of your resource groups.  It's *not* possible to change a lab plan or shared image gallery resource group once it's created. If you need to change the resource group for these resources, you'll need to delete and re-create your lab plan or shared image gallery.

## Lab plan

A lab plan is associated with one or more labs. When you're getting started with Azure Lab Services, it's most common to have a single lab plan. As your lab usage scales up, you can choose to create more lab plans later.

The following list highlights scenarios where more than one lab plan might be beneficial:

- **Manage different policy requirements across labs**

  When you set up a lab plan, you set policies that apply to all newly created labs, such as:

  - The Azure virtual network with shared resources that the lab can access. For example, you might have a set of labs that need access to a license server within a virtual network.
  - The virtual machine images that the labs can use to create VMs. For example, you might have a set of labs that need access to the [Data Science VM for Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Azure Marketplace image.

  If each of your labs has unique policy requirements, it might be beneficial to create separate lab plans for managing each lab separately.

- **Isolate pilot labs from active or production labs**
  
  You might have cases where you want to pilot policy changes for a lab plan without potentially affecting your active or production labs. In this type of scenario, creating a separate lab plan for piloting purposes allows you to isolate changes.

## Lab

A lab contains VMs that are each assigned to a single student.  In general, you can expect to:

- Have one lab for each class.
- Create a new set of labs for each semester, quarter, or other academic system you're using. For classes that need to use the same image, you should use a [shared image gallery](#shared-image-gallery). This way, you can reuse images across labs and academic periods.

When you're determining how to structure your labs, consider the following points:

- **All VMs within a lab are deployed with the same image that's published**

    As a result, if you have a class that requires different lab images to be published at the same time, a separate lab must be created for each image.
  
- **The usage quota is set at the lab level and applies to all users within the lab**

    To set different quotas for users, you must create separate labs. However, it's possible to add more hours to specific users after you've set the quota.
  
- **The startup or shutdown schedule is set at the lab level and applies to all VMs within the lab**

    Similar to quota setting, if you need to set different schedules for users, you need to create a separate lab for each schedule.

By default, each lab has its own virtual network.  If you have [virtual network injection](how-to-connect-vnet-injection.md) enabled, each lab will use the specified network.

## Shared image gallery

A shared image gallery is attached to a lab plan and serves as a central repository for storing images. An image is saved in the gallery when an educator chooses to export it from a lab's template VM. Each time an educator makes changes to the template VM and exports it, new image definitions and\or versions are created in the gallery.  

Instructors can publish an image version from the shared image gallery when they create a new lab. Although the gallery stores multiple versions of an image, educators can select only the most recent version during lab creation.  The most recent version is chosen based on the highest value of MajorVersion, then MinorVersion, then Patch.  For more information about versioning, see [Image versions](../virtual-machines/shared-image-galleries.md#image-versions).

The shared image gallery service is an optional resource that you might not need immediately if you're starting with only a few labs. However, shared image gallery offers many benefits that are helpful as you scale up to more labs:

- **You can save and manage versions of a template VM image**

    It's useful to create a custom image or make changes (software, configuration, and so on) to an image from the Azure Marketplace gallery.  For example, it's common for educators to require different software or tooling be installed. Rather than requiring students to manually install these prerequisites on their own, different versions of the template VM image can be exported to a shared image gallery. You can then use these image versions when you create new labs.

- **You can share and reuse template VM images across labs**

    You can save and reuse an image so that you don't have to configure it from scratch each time that you create a new lab. For example, if multiple classes need to use the same image, you can create it once and export it to the shared image gallery so that it can be shared across labs.

- **You can upload your own custom images from other environments outside of labs**

    You can [upload custom images other environments outside of the context of labs](how-to-attach-detach-shared-image-gallery.md).  For example, you can upload images from your own physical lab environment or from an Azure VM into shared image gallery.  Once an image is imported into the gallery, you can then use the images to create labs.

To logically group shared images, you can do either of the following methods:

- Create multiple shared image galleries. Each lab plan can connect to only one shared image gallery, so this option also requires you to create multiple lab plans.
- Use a single shared image gallery that's shared by multiple lab plans. In this case, each lab plan can enable only images that are applicable to the labs in that plan.

## Naming

As you get started with Azure Lab Services, we recommend that you establish naming conventions for Azure and Azure Lab Services related resources.  Although the naming conventions that you establish will be unique to the needs of your organization, the following table provides general guidelines:

| Resource type | Role | Suggested pattern | Examples |
| ------------- | ---- | ----------------- | -------- |
| Resource group | Contains one or more lab plans, labs and/or shared image galleries. |{org-name}labs-{env}-rg, {dept-name}labs-rg | contosolabs-rg, contosolabs-pilot-rg, contosolabs-prod-rg, mathdept-rg |
| Lab plan | Template for newly created labs. | {org-name}-{env}-lp, {dept-name}-{env}-lp | contoso-lp, mathdept-lp, cs-pilot-lp |
| Lab | Contains one or more student VMs. | {class-name}-{time}-{educator} | CS101-Fall2021, CS101-Fall2021-JohnDoe |
| Shared image gallery | Contains one or more VM image versions | {org-name}-sig, {dept-name}-sig | contoso-sig, mathdept-sig |

In the proceeding table, we used some terms and tokens in the suggested name patterns.  Let's go over those terms in a little more detail.

| Pattern term/token | Definition | Example |
| ------------ | ---------- | ------- |
| {org-name} | Token for organization short name with no spaces. | contoso |
| {dept-name} | Token for short name of department in organization. | math, bio, cs |
| {env} | Token for environment name | prod for production, pilot for small test |
| {class-name} | Token for short name or code for class being supported. | CS101, Bio101 |
| {educator} | Alias of educator running the lab. | johndoe |
| {time} | Token for short name (with no spaces) for time the class is being offered. | Spring2021, Dec 2021|
| rg | Indicates resource is a resource group. | |
| lp | Indicates resource is a lab plan. | |
| sig | Indicates resource is a shared image gallery. | |

For more information about naming other Azure resources, see [Naming conventions for Azure resources](/azure/architecture/best-practices/naming-conventions).

## Regions

When you set up your Azure Lab Services resources, you're required to provide a region or location of the data center that will host the resources. Lab plans can enable one or more regions in which labs may be created. The next sections describe how a region or location might affect each resource that's involved with setting up a lab.

- **Resource group**. The region specifies the datacenter where information about a resource group is stored. Azure resources contained within the resource group can be in a different region from that of their parent.
- **Lab plan**. A lab plan's location indicates the region that a resource exists in.  When a lab plan is connected to your own virtual network, the network must be in the same region as the lab plan. Also, labs will be created in the same Azure region as that virtual network.
- **Lab**.  The location that a lab exists in varies, and doesn't need to be in the same location as the lab plan. Administrators control which regions labs can be created in through the lab plan settings. A general rule is to set a resource's region to one that's closest to its users. For labs, this means creating the lab that's closest to your students. For online courses whose students are located all over the world, use your best judgment to create a lab that's centrally located. Or you can split a class into multiple labs according to your students' regions.

> [!NOTE]
> To help ensure that a region has sufficient VM capacity, it's important to first [request capacity](capacity-limits.md#request-a-limit-increase).

## VM sizing

When administrators or Lab Creators create a lab, they can choose from various VM sizes, depending on the needs of their classroom. Remember that the size availability depends on the region that your lab plan is located in.

For information on VM sizes and their cost, see the [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE]
> You may not see some of the expected VM sizes in the list when creating a lab. The list is populated based on the current capacity of the lab's location.

## RBAC roles

By using [Azure role-based access control (RBAC)](../role-based-access-control/overview.md) for access to lab plans and labs, you can assign the following roles:

- **Owner**

  An administrator who creates a lab plan is automatically assigned the lab plan Owner role. The Owner role can:

  - Change the lab plan settings.
  - Grant other administrators access to the lab plan as an Owner or Contributor.
  - Grant educators access to labs as a Creator, Owner, or Contributor.
  - Create and manage all labs in the lab plan.

- **Contributor**

  An administrator who's assigned the Contributor role can:

  - Change the lab plan settings.
  - Create and manage all labs in the lab plan.

  However, the Contributor *can't* grant other users access to either lab plans or labs.

- **Lab Creator**

  When set on the lab plan, this role enables the user account to create labs from the lab plan. The user account can also see existing labs that are in the same resource group as the lab plan. When applied to a resource group, this role enables the user to view existing lab and create new labs. They will have full control over any labs they create as they are assigned as Owner to those created labs. For more information, see [Add a user to the Lab Creator role](./tutorial-setup-lab-plan.md#add-a-user-to-the-lab-creator-role).

- **Lab Contributor**

  When applied to an existing lab, this role enables the user to fully manage the lab. When applied to a resource group enables the user account to fully manage existing labs and create new labs in that resource group.

  A key difference between the lab Owner and Contributor roles is that only an Owner can grant other users access to manage a lab. A Contributor *can't* grant other users access to manage a lab.

- **Lab Operator**

  When applied to a resource group or a lab, this role enables the user to have limited ability to manage existing labs. This role won’t give the user the ability to create new labs. In an existing lab, the user can manage users, adjust individual users’ quota, manage schedules, and start/stop VMs. The user account will be able to publish a lab. The user will not have the ability to change lab capacity or change quota at the lab level. The user won’t be able to change the template title or description.

- **Lab Assistant**

  When applied to a resource group or a lab, enables the user to view an existing lab and can only perform actions on the lab VMs (reset, start, stop, connect) and send invites to the lab. The user will not have the ability to change a lab, create a lab, publish a lab, change lab capacity, or manage quota and schedules. This user can’t adjust individual quota.

- **Lab Services Contributor**

  When applied to a resource group, enables the user to fully control all Lab Services scenarios in that resource group.

- **Lab Services Reader**

  When applied to a resource group, enables the user to view, but not change, all lab plans and lab resources. External resources like image galleries and virtual networks that may be connected to a lab plan aren't included.

When you're assigning roles, it helps to follow these tips:

- Ordinarily, only administrators should be members of a lab plan Owner or Contributor role. The lab plan might have more than one Owner or Contributor.
- To give educators the ability to create new labs and manage the labs that they create, you need only assign them the Lab Creator role.
- To give educators the ability to manage specific labs, but *not* the ability to create new labs, assign them either the Owner or Contributor role for each lab that they'll manage. For example, you might want to allow a professor and a teaching assistant to co-own a lab.

## Content filtering

Your school may need to do content filtering to prevent students from accessing inappropriate websites.  For example, to comply with the [Children's Internet Protection Act (CIPA)](https://www.fcc.gov/consumers/guides/childrens-internet-protection-act).  Lab Services doesn't offer built-in support for content filtering.

There are two approaches that schools typically consider for content filtering:

- Configure a firewall to filter content at the network level.
- Install 3rd party software directly on each computer that performs content filtering.

By default, Azure Lab Services hosts each lab's virtual network within a Microsoft-managed Azure subscription.  You'll need to use [advanced networking](how-to-connect-vnet-injection.md) in the lab plan.  Make sure to check known limitations of VNet injection before proceeding.

We recommend the second approach, which is to install 3rd party software on each lab's template VM.  There are a few key points to highlight as part of this solution:

- If you plan to use the [auto-shutdown settings](./cost-management-guide.md#automatic-shutdown-settings-for-cost-control), you will need to unblock several Azure host names with the 3rd party software.  The auto-shutdown settings use a diagnostic extension that must be able to communicate back to Lab Services.  Otherwise, the auto-shutdown settings will fail to enable for the lab.
- You may also want to have each student use a non-admin account on their VM so that they can't uninstall the content filtering software.  Adding a non-admin account must be done when creating the lab.

If your school needs to do content filtering, contact us via the [Azure Lab Services' Q&A](https://aka.ms/azlabs/questions) for more information.

## Endpoint management

Many endpoint management tools, such as [Microsoft Endpoint Manager](https://techcommunity.microsoft.com/t5/azure-lab-services/configuration-manager-azure-lab-services/ba-p/1754407), require Windows VMs to have unique machine security identifiers (SIDs).  Using SysPrep to create a *generalized* image typically ensures that each Windows machine will have a new, unique machine SID generated when the VM boots from the image.

With Lab Services, even if you use a *generalized* image to create a lab, the template VM and student VMs will all have the same machine SID.  The VMs have the same SID because the template VM's image is in a *specialized* state when it's published to create the student VMs.

For example, the Azure Marketplace images are generalized.  If you create a lab from the Windows 10 marketplace image and publish the template VM, all of the student VMs within a lab will have the same machine SID as the template VM.  The machine SIDs can be verified by using a tool such as [PsGetSid](/sysinternals/downloads/psgetsid).

If you plan to use an endpoint management tool or similar software, we recommend that you test it with lab VMs to ensure that it works properly when machine SIDs are the same.  

## Pricing

### Azure Lab Services

To learn about pricing, see [Azure Lab Services pricing](https://azure.microsoft.com/pricing/details/lab-services/).

Billing entries in Azure Cost Management are per lab VM. Tags for lab plan ID and lab name are automatically added to each entry for more flexible analysis and budgeting.

### Shared Image Gallery

You also need to consider the pricing for the Shared Image Gallery service if you plan to use shared image galleries for storing and managing image versions.

Creating a shared image gallery and attaching it to your lab plan is free. No cost is incurred until you save an image version to the gallery. The pricing for using a shared image gallery is ordinarily fairly negligible, but it's important to understand how it's calculated, because it isn't included in the pricing for Azure Lab Services.  

#### Storage charges

To store image versions, a shared image gallery uses standard hard disk drive (HDD) managed disks by default.  We recommend using HDD-managed disks when using shared image gallery with Lab Services.  The size of the HDD-managed disk that's used depends on the size of the image version that's being stored.  Lab Services supports image and disk sizes up to 128 GB.  To learn about pricing, see [Managed disks pricing](https://azure.microsoft.com/pricing/details/managed-disks/).

#### Replication and network egress charges

When you save an image version by using a lab template VM, Azure Lab Services first stores it in a source region.  However, you will most likely need to replicate the source image version to one or more target regions.

A network egress charge occurs when an image version is replicated from the source region to other target regions. The amount charged is based on the size of the image version when the image's data is initially transferred outbound from the source region.  For pricing details, see [Bandwidth pricing details](https://azure.microsoft.com/pricing/details/bandwidth/).

Egress charges might be waived for [Education Solutions](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) customers. To learn more, contact your account manager.

For more information, see "What data transfer programs exist for academic customers and how do I qualify?" in the FAQ section of the [Programs for educational institutions](https://azure.microsoft.com/pricing/details/bandwidth/) page.

For information about costs to store images and their replications, see [billing in an Azure Compute Gallery](/azure/virtual-machines/shared-image-galleries).

#### Pricing example

Let's look at an example of the cost of saving a template VM image to a shared image gallery. Assume the following scenarios:

- You have one custom VM image.
- You're saving two versions of the image.
- You replicated the image to eight regions.
- Each image version is 32 GB in

The total cost per month is estimated as:

Number of images &times; number of versions &times; number of replicas &times; managed disk price = total cost per month

In this example, the cost is:

1 custom image (32 GB) &times; 2 versions &times; 8 US regions &times; $1.54 = $24.64 per month

> [!NOTE]
> The preceding calculation is for example purposes only. It covers storage costs associated with using Shared Image Gallery and does *not* include egress costs. For actual pricing for storage, see [Managed Disks pricing](https://azure.microsoft.com/pricing/details/managed-disks/).

#### Cost management

It's important for lab plan administrators to manage costs by routinely deleting unneeded image versions from the gallery.

Be wary of removing replication to specific regions as a way to reduce the costs.  Replication changes might have adverse effects on the ability of Azure Lab Services to publish VMs from images saved within a shared image gallery.

## Next steps

For more information about setting up and managing labs, see:

- [Lab plan setup guide](lab-plan-setup-guide.md)  
- [Lab setup guide](setup-guide.md)
- [Cost management for labs](cost-management-guide.md)  
