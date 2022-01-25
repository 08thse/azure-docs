---
title: Connect to your virtual network in Azure Lab Services | Microsoft Docs
description: Learn how to connect your virtual network with another network. For example, connect your on-premises organization/university network with Lab's virtual network in Azure.  
ms.topic: how-to
ms.date: 11/11/2021
---

# Connect to your virtual network in Azure Lab Services

This article provides information about connecting a lab plan to your virtual network.

Some organizations have advanced network requirements and configurations, such as network traffic control, ports management, access to resources in an internal network, etc., that they want to apply to labs.

In the Azure Lab Services [January 2022 Update (preview)](lab-services-whats-new.md), customers have the option to take full control of the network for the labs using virtual network (VNet) injection. Instead of peering to your virtual network, as was done in previous versions, you can now tell us which virtual network to use, and we’ll inject the necessary resources into your network.

With VNet injection, you can connect to on premise resources such as licensing servers and use user defined routes (UDRs).

## Overview

You can bring your own virtual network to your lab plan when you create the lab plan.

> [!IMPORTANT]
> VNet injection must be configured when creating a lab plan.  It can't be added later.

Before you configure VNet injection for your lab plan:

- You must create a virtual network. See [Create a virtual network](/azure/virtual-network/quick-create-portal).
- The virtual network must be in the same region as the lab plan.
- Create a subnet for the virtual network and delegate it to **Microsoft.LabServices/labplans**. See [Add or remove a subnet delegation](/azure/virtual-network/manage-subnet-delegation).

Certain on-premises networks are connected to Azure Virtual Network either through [ExpressRoute](../expressroute/expressroute-introduction.md) or [Virtual Network Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). These services must be set up outside of Azure Lab Services. To learn more about connecting an on-premises network to Azure using ExpressRoute, see [ExpressRoute overview](../expressroute/expressroute-introduction.md). For on-premises connectivity using a Virtual Network Gateway, the gateway, specified virtual network, and the lab plan must all be in the same region.

> [!NOTE]
> If your school needs to perform content filtering, such as for compliance with the [Children's Internet Protection Act (CIPA)](https://www.fcc.gov/consumers/guides/childrens-internet-protection-act), you will need to use 3rd party software.  For more information, read guidance on [content filtering with Lab Services](./administrator-guide.md#content-filtering).

## Delegate the virtual network subnet for use with a lab plan

After you create a subnet for your virtual network, you must the delegate the subnet for use with a lab plan in Azure Lab Services.

Only one lab plan at a time can be delegated for use with one subnet.

1. In the Virtual Network page, select **Subnets**.

2. Open an existing subnet. Or, create a new subnet first. Then, open the existing subnet.

3. In **Delegate subnet to a service** and then select **Microsoft.LabServices/labplans**.

   :::image type="content" source="./media/how-to-connect-vnet-injection/delegate-subnet-for-azure-lab-services.png" alt-text="Delegate a subnet":::

4. Save the subnet and verify that the lab plan service appears in the **Delegated to** column.

   :::image type="content" source="./media/how-to-connect-vnet-injection/delegated-subnet.png" alt-text="Delegated subnet":::

## Add the virtual network at the time of lab plan creation

1. From the **Basics** tab of the **Create a lab plan** page, select **Next: Networking** at the bottom of the page.
2. To host on a virtual network, select **Enable advanced networking**.

    1. For **Virtual network**, select an existing virtual network for the lab network. For a virtual network to appear in this list, it must be in the same region as the lab plan. For more information, see [Connect to your virtual network](how-to-connect-vnet-injection.md).
    2. Specify an existing **subnet** for VMs in the lab. For a subnet to appear in this list, it must be delegated for use with lab plans when you configure the subnet for the virtual network. For more information, see [Add a virtual network subnet](/azure/virtual-network/virtual-network-manage-subnet).  

        :::image type="content" source="./media/how-to-manage-lab-plans/create-lab-plan-advanced-networking.png" alt-text="Create lab plan -> Networking":::

Once you have a lab plan configured with advanced networking, all labs created with this lab plan use the specified subnet.

## Configure the subnet after the lab plan is created

Once you delegate a subnet for use with Azure Lab Services, the subnet is locked and you can't configure its settings.

So, if you need to make changes to the subnet, first delete the lab, then delete the subnet. Create a new subnet with the desired properties.

## Known issues

- Deleting your virtual network or subnet will cause the lab to stop working
- Changing the DNS label on the public IP will cause the **Connect** button for lab VMs to stop working.
- Azure Firewall isn’t currently supported.

## Next steps

See the following articles:

- [Attach a compute gallery to a lab](how-to-attach-detach-shared-image-gallery.md)
- [View firewall settings for a lab](how-to-configure-firewall-settings.md)
- [Configure other settings for a lab](how-to-configure-auto-shutdown-lab-plans.md)
