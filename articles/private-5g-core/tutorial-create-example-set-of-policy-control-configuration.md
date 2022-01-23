---
title: Tutorial - Create an example policy control configuration set
titlesuffix: Azure Private 5G Core Preview
description: In this tutorial, you'll create an example policy control configuration set with traffic handling for common scenarios. 
author: djrmetaswitch
ms.author: drichards
ms.service: private-5g-core
ms.topic: tutorial 
ms.date: 01/16/2022
ms.custom: template-tutorial
---

# Tutorial: Create an example policy control configuration set for Azure Private 5G Core

Azure Private 5G Core Preview provides flexible traffic handling, allowing you to customize how your packet core instance applies Quality of Service (QoS) characteristics to traffic to meet its needs, as well as block or limit certain flows. The following tutorial takes you through the steps of creating services and SIM policies for common use cases, and then provisioning SIMs to use the new policy control configuration.

In this tutorial, you'll learn how to:

> [!div class="checklist"]
> * Create a new service that filters packets based on their protocol.
> * Create a new service that blocks traffic labeled with specific remote IP addresses and ports.
> * Create a new service that limits the bandwidth of traffic on matching flows.
> * Create two new SIM policies and assign services to them.
> * Provision two new SIMs and assign them SIM policies.

## Prerequisites

* Read the information in [Policy control](policy-control.md) and familiarize yourself with Azure Private 5G Core policy control configuration.
* Ensure you can sign in to the Azure Portal using an account with access to the active subscription you identified in [Complete the prerequisite tasks for deploying a private mobile network](complete-private-mobile-network-prerequisites.md). This account must have the built-in Contributor role at the subscription scope.
* Identify the name of the Mobile Network resource corresponding to your private mobile network.

## Create a service for protocol filtering

In this step, we'll create a service that filters packets based on their protocol. Specifically, it'll do the following.

* Block ICMP packets flowing away from UEs.
* Block UDP packets flowing away from UEs on port 11.
* Allow all other ICMP and UDP traffic in both directions, but no other IP traffic.

Do the following to create the service.

1. Sign in to the Azure portal at [https://aka.ms/PMNSPortal](https://aka.ms/PMNSPortal).<!-- Is this the correct link? -->
1. Search for and select the Mobile Network resource representing your private mobile network.

    :::image type="content" source="media/mobile-network-search.png" alt-text="Screenshot of the Azure portal showing the results for a search for a Mobile Network resource.":::

1. In the resource menu, select **Services**.

    :::image type="content" source="media\configure-service-azure-portal\services-resource-menu-option.png" alt-text="Screenshot of the Azure portal showing the Services option in the resource menu of a Mobile Network resource.":::

1. In the command bar, select **Create**.

    :::image type="content" source="media\configure-service-azure-portal\create-command-bar-option.png" alt-text="Screenshot of the Azure portal showing the Create option in the command bar.":::

1. We'll now enter values to define the QoS characteristics that will be applied to Service Data Flows that match this service. On the Basics tab, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Service name**     |`service_restricted_udp_and_icmp`         |
    |**Service precedence**     | `100`        |
    |**Maximum Bit Rate (MBR) - Uplink**     | `10 Mbps`        |
    |**Maximum Bit Rate (MBR) - Downlink**     | `10 Mbps`        |
    |**Allocation and Retention Priority level**     | `2`        |
    |**5G QoS Indicator (5QI)**     | `9`        |
    |**Preemption capability**     | Select **May not preempt**.        |
    |**Preemption vulnerability**     | Select **Not preemptable**.        |

1. Select **Add a policy rule**.
1. We'll now create a data flow policy rule that blocks any packets that match the data flow template we'll configure in the next step. On the **Add a policy rule** blade, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Rule name**     |`rule_block_icmp_and_udp_uplink_traffic`         |
    |**Policy rule precedence**     | Select **10**.        |
    |**Allow traffic**     | Select **Blocked**.        |

1. We'll now create a data flow template that matches on ICMP packets flowing away from UEs, so that they can be blocked by the `rule_block_icmp_uplink_traffic` rule.
    Select **Add a data flow template**. In the **Add a data flow template** pop-up, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Template name**     |`icmp_uplink_traffic`         |
    |**Protocols**     | Select **ICMP**.        |
    |**Direction**     | Select **Uplink**.        |
    |**Remote IPs**     | `any`        |
    |**Ports**     | Leave blank.        |

1. Select **Add**.
1. We'll now create another data flow template for the same rule that matches on UDP packets flowing away UEs on port 11. 

    Select **Add a data flow template**. In the **Add a data flow template** pop-up, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Template name**     |`udp_uplink_traffic_port_11`         |
    |**Protocols**     | Select **UDP**.        |
    |**Direction**     | Select **Uplink**.        |
    |**Remote IPs**     | `any`        |
    |**Ports**     | `11`        |

1. We can now finalize the rule. On the **Add a policy rule** blade, select **Add**.
1. Finally, we'll create a data policy flow rule that allows all other ICMP and UDP traffic.

    Select **Add a policy rule** and then fill out the fields on the **Add a policy rule** blade as follows.

    |Field  |Value  |
    |---------|---------|
    |**Rule name**     |`rule_allow_other_icmp_and_udp_traffic`         |
    |**Policy rule precedence**     | Select **15**.        |
    |**Allow traffic**     | Select **Enabled**.        |

1. We'll now create a data flow template that matches on all ICMP and UDP in both directions.

    Select **Add a data flow template**. In the **Add a data flow template** pop-up, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Template name**     |`icmp_and udp_traffic`         |
    |**Protocols**     | Tick both the **UDP** and **ICMP** checkboxes.        |
    |**Direction**     | Select **Bidirectional**.        |
    |**Remote IPs**     | `any`        |
    |**Ports**     | Leave blank.        |

1. Select **Add**.
1. We can now finalize the rule. On the **Add a policy rule** blade, select **Add**.
1. We now have two configured data flow policy rules on the service, which are displayed under the **Data flow policy rules** heading. Note that the `rule_block_icmp_and_udp_uplink_traffic` rule has a lower precedence value than the `rule_allow_other_icmp_and_udp_traffic` rule (10 and 15 respectively). This ensures that the `rule_block_icmp_and_udp_uplink_traffic` rule to block packets is applied first, before the wider `rule_allow_other_icmp_and_udp_traffic` is applied to all remaining packets.

    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\example-protocol-filtering-service.png" alt-text="Screenshot of the Azure portal. It shows the create a service screen with all fields correctly filled out and two data flow policy rules.":::

1. On the **Basics** configuration tab, select **Review + create**.
1. Select **Create** to create the service.
 
    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\create-example-protocol-filtering-service.png" alt-text="Screenshot of the Azure portal. It shows the Review and create tab with complete configuration for a service.":::

1. The Azure portal will display the following confirmation screen when the service has been created. Select **Go to resource** to see the new service resource.

    :::image type="content" source="media\configure-service-azure-portal\service-resource-deployment-confirmation.png" alt-text="Screenshot of the Azure portal showing the successful deployment of a service resource and the Go to resource button.":::

1. Confirm that the QoS characteristics, data flow policy rules, and service data flow templates listed at the bottom of the screen are configured as expected.

    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\example-protocol-filtering-service-complete.png" alt-text="Screenshot of the Azure portal. It shows a Service resource, with configured QoS characteristics and data flow policy rules highlighted." lightbox="media\tutorial-create-example-set-of-policy-control-configuration\example-protocol-filtering-service-complete.png":::

## Create a service for blocking traffic from specific sources

In this step, we'll create a service that blocks traffic from specific sources. Specifically, it'll do the following.

* Block UDP packets labeled with the remote address 10.204.141.200 and port 12 flowing towards UEs.
* Block UDP packets labeled with any remote address in the range 10.204.141.0/24 and port 15 flowing in both directions
* Allow all other IP traffic in both directions.

Do the following to create the service.

1. Search for and select the Mobile Network resource representing your private mobile network.

    :::image type="content" source="media/mobile-network-search.png" alt-text="Screenshot of the Azure portal showing the results for a search for a Mobile Network resource.":::

1. In the resource menu, select **Services**.

     :::image type="content" source="media\configure-service-azure-portal\services-resource-menu-option.png" alt-text="Screenshot of the Azure portal showing the Services option in the resource menu of a Mobile Network resource.":::

1. In the command bar, select **Create**.

    :::image type="content" source="media\configure-service-azure-portal\create-command-bar-option.png" alt-text="Screenshot of the Azure portal showing the Create option in the command bar.":::

1. We'll now enter values to define the QoS characteristics that will be applied to Service Data Flows that match this service. On the **Basics** tab, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Service name**     |`service_blocking_udp_from_specific_sources`         |
    |**Service precedence**     | `253`        |
    |**Maximum Bit Rate (MBR) - Uplink**     | `10 Mbps`        |
    |**Maximum Bit Rate (MBR) - Downlink**     | `10 Mbps`        |
    |**Allocation and Retention Priority level**     | `2`        |
    |**5G QoS Indicator (5QI)**     | `9`        |
    |**Preemption capability**     | Select **May not preempt**.        |
    |**Preemption vulnerability**     | Select **Not preemptable**.        |

1. Select **Add a policy rule**.
1. We'll now create a data flow policy rule that blocks any packets that match the data flow template we'll configure in the next step. On the **Add a policy rule** blade, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Rule name**     |`rule_block_udp_from_specific_sources`         |
    |**Policy rule precedence**     | Select **11**.        |
    |**Allow traffic**     | Select **Blocked**.        |

1. We'll now create a data flow template that matches on UDP packets flowing towards UEs from 10.204.141.200 on port 12, so that they can be blocked by the `rule_block_udp_from_specific_sources` rule.

    Select **Add a data flow template**. In the **Add a data flow template** pop-up, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Template name**     |`udp_downlink_traffic`         |
    |**Protocols**     | Select **UDP**.        |
    |**Direction**     | Select **Downlink**.        |
    |**Remote IPs**     | `10.204.141.200/32`        |
    |**Ports**     | `12`        |

1. Select **Add**.
1. We'll now create another data flow template for the same rule that matches on UDP packets flowing in either direction that are labelled with any remote address in the range 10.204.141.0/24 and port 15.

    Select **Add a data flow template**. In the **Add a data flow template** pop-up, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Template name**     |`udp_bidirectional_traffic`         |
    |**Protocols**     | Select **UDP**.        |
    |**Direction**     | Select **Bidirectional**.        |
    |**Remote IPs**     | `10.204.141.0/24`        |
    |**Ports**     | `15`        |

1. Select **Add**.
1. We can now finalize the rule. Under **Add a policy rule**, select **Add**.


    :::image type="complex" source="media\tutorial-create-example-set-of-policy-control-configuration\example-udp-blocking-rule.png" alt-text="Screenshot of the Azure portal. It shows the Add a policy rule screen with configuration for a rule to block certain UDP traffic.":::
    Screenshot of the Azure portal. It shows the Add a policy rule screen with all fields correctly filled out for a rule to block certain UDP traffic. It includes two configured data flow templates. The first matches on UDP packets flowing towards UEs from 10.204.141.200 on port 12. The second matches on UDP packets flowing in either direction that are labelled with any remote address in the range 10.204.141.0/24 and port 15. The Add button is highlighted.
    :::image-end:::

1. Finally, we'll create a data policy flow rule that allows all other IP traffic.

    Select **Add a policy rule** and then fill out the fields on the **Add a policy rule** blade as follows.

    |Field  |Value  |
    |---------|---------|
    |**Rule name**     |`rule_allow_other_ip_traffic`         |
    |**Policy rule precedence**     | Select **20**.        |
    |**Allow traffic**     | Select **Enabled**.        |

1. We'll now create a data flow template that matches on all IP traffic in both directions.

    Select **Add a data flow template**. In the **Add a data flow template** pop-up, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Template name**     |`ip_traffic`         |
    |**Protocols**     | Select **All**.        |
    |**Direction**     | Select **Bidirectional**.        |
    |**Remote IPs**     | `any`        |
    |**Ports**     | Leave blank        |

1. Select **Add**.
1. We can now finalize the rule. Under **Add a policy rule**, select **Add**.
1. We now have two configured data flow policy rules on the service, which are displayed under the **Data flow policy rules** heading. Note that the `rule_block_udp_from_specific_sources` rule has a lower precedence value than the `rule_allow_other_ip_traffic` rule (11 and 20 respectively). This ensures that the `rule_block_udp_from_specific_sources` rule to block packets is applied first, before the wider `rule_allow_other_ip_traffic` is applied to all remaining packets.

    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\example-traffic-blocking-service.png" alt-text="Screenshot of the Azure portal. It shows completed fields for a service to block UDP from specific sources, including data flow policy rules.":::  

1. On the **Basics** configuration tab, select **Review + create**.
1. Select **Create** to create the service.

    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\create-example-traffic-blocking-service.png" alt-text="Screenshot of the Azure portal. It shows the Review and create tab with complete configuration for a service.":::

1. The Azure portal will display the following confirmation screen when the service has been created. Select **Go to resource** to see the new service resource.

     :::image type="content" source="media\configure-service-azure-portal\service-resource-deployment-confirmation.png" alt-text="Screenshot of the Azure portal showing the successful deployment of a service resource and the Go to resource button.":::

1. Confirm that the data flow policy rules and service data flow templates listed at the bottom of the screen are configured as expected.

    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\example-traffic-blocking-service-complete.png" alt-text="Screenshot of the Azure portal. It shows a service resource. QoS characteristics and data flow policy rules are highlighted." lightbox="media\tutorial-create-example-set-of-policy-control-configuration\example-traffic-blocking-service-complete.png":::

## Create a service for limiting traffic

In this step, we'll create a service that limits the bandwidth of traffic on matching flows. Specifically, it'll do the following.

* Limit the Maximum Bit Rate (MBR) for packets flowing away from UEs to 2 Gbps.
* Limit the Maximum Bit Rate (MBR) for packets flowing towards UEs to 1 Gbps.

Do the following to create the service.

1. Search for and select the Mobile Network resource representing your private mobile network.

    :::image type="content" source="media/mobile-network-search.png" alt-text="Screenshot of the Azure portal showing the results for a search for a Mobile Network resource.":::

1. In the resource menu, select **Services**.

    :::image type="content" source="media\configure-service-azure-portal\services-resource-menu-option.png" alt-text="Screenshot of the Azure portal showing the Services option in the resource menu of a Mobile Network resource.":::

1. In the command bar, select **Create**.

    :::image type="content" source="media\configure-service-azure-portal\create-command-bar-option.png" alt-text="Screenshot of the Azure portal showing the Create option in the command bar.":::

1. We'll now enter values to define the QoS characteristics that will be applied to Service Data Flows that match this service. We'll use the **Maximum Bit Rate (MBR) - Uplink** and **Maximum Bit Rate (MBR) - Downlink** fields to set our bandwidth limits. On the **Basics** tab, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Service name**     |`service_traffic_limits`         |
    |**Service precedence**     | `150`        |
    |**Maximum Bit Rate (MBR) - Uplink**     | `2 Gbps`        |
    |**Maximum Bit Rate (MBR) - Downlink**     | `1 Gbps`        |
    |**Allocation and Retention Priority level**     | `2`        |
    |**5G QoS Indicator (5QI)**     | `9`        |
    |**Preemption capability**     | Select **May not preempt**.        |
    |**Preemption vulnerability**     | Select **Not preemptable**.        |

1. Select **Add a policy rule**.

1. Select **Add a policy rule** and then fill out the fields on the **Add a policy rule** blade as follows.

    |Field  |Value  |
    |---------|---------|
    |**Rule name**     |`rule_bidirectional_limits`         |
    |**Policy rule precedence**     | Select **12**.        |
    |**Allow traffic**     | Select **Enabled**.        |

1. We'll now create a data flow template that matches on all IP traffic in both directions.

    Select **Add a data flow template**. In the **Add a data flow template** pop-up, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Template name**     |`ip_traffic`         |
    |**Protocols**     | Select **All**.        |
    |**Direction**     | Select **Bidirectional**.        |
    |**Remote IPs**     | `any`        |
    |**Ports**     | Leave blank        |

1. Select **Add**.
1. We can now finalize the rule. On the **Add a policy rule** blade, select **Add**.
1. We now have a single data flow policy rule configured on the service.

     :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\example-traffic-limiting-service.png" alt-text="Screenshot of the Azure portal showing the successful deployment of a service resource and the Go to resource button.":::

1. On the **Basics** configuration tab, select **Review + create**.
1. Select **Create** to create the service.

    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\create-example-traffic-limiting-service.png" alt-text="Screenshot of the Azure portal. It shows the Review and create tab with complete configuration for a service. The Create button is highlighted.":::

1. The Azure portal will display the following confirmation screen when the service has been created. Select **Go to resource** to see the new service resource.

     :::image type="content" source="media\configure-service-azure-portal\service-resource-deployment-confirmation.png" alt-text="Screenshot of the Azure portal showing the successful deployment of a service resource and the Go to resource button.":::

1. Confirm that the data flow policy rules and service data flow templates listed at the bottom of the screen are configured as expected.

    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\example-traffic-limiting-service-complete.png" alt-text="Screenshot of the Azure portal. It shows a service resource. QoS characteristics and data flow policy rules are highlighted." lightbox="media\tutorial-create-example-set-of-policy-control-configuration\example-traffic-limiting-service-complete.png":::

## Configure SIM policies

In this step, we will create two SIM policies. The first SIM policy will use the service we created in [Create a service for protocol filtering](#create-a-service-for-protocol-filtering), and the second will use the service we created in [Create a service for blocking traffic from specific sources](#create-a-service-for-blocking-traffic-from-specific-sources). Both SIM policies will use the third service we created in [Create a service for limiting traffic](#create-a-service-for-limiting-traffic).

1. Search for and select the Mobile Network resource representing your private mobile network.

    :::image type="content" source="media/mobile-network-search.png" alt-text="Screenshot of the Azure portal showing the results for a search for a Mobile Network resource.":::

1. In the resource menu, select **SIM policies**.

    :::image type="content" source="media\configure-sim-policy-azure-portal\sim-policies-resource-menu-option.png" alt-text="Screenshot of the Azure portal showing the SIM policies option in the resource menu of a Mobile Network resource.":::

1. In the command bar, select **Create**.
1. Under **Create a SIM policy**, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Policy name**     |`sim-policy-1`         |
    |**Total bandwidth allowed - Uplink**     | `10 Gbps`        |
    |**Total bandwidth allowed - Downlink**     | `10 Gbps` |
    |**Default slice**     | Select **slice-1 (Default)**.        |
    |**Registration timer**     | `3240`        |
    |**RFSP index**     | `2`        |

    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\example-sim-policy-1.png" alt-text="Screenshot of the Azure portal. It shows each of the fields filled out for an example SIM policy.":::    

1. Select **Add a network scope**.
1. Under **Add a network scope**, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Slice**     | Select **slice-1 (Default)**         |
    |**Data network**     | Select the data network to which your private mobile network connects.        |
    |**Service configuration**     | Select **rule_block_icmp_and_udp_uplink_traffic** and **service_traffic_limits**. |
    |**Session aggregate maximum bit rate - Uplink**     | `2 Gbps`        |
    |**Session aggregate maximum bit rate - Downlink**     | `2 Gbps`        |
    |**5G QoS Indicator (5QI)**     | `9`        |
    |**Allocation and Retention Priority level**     | `9`        |
    |**Preemption capability**     | Select **May not preempt**.        |
    |**Preemption vulnerability**     | Select **Preemptable**.        |
    |**Default session type**     | Select **IPv4**.        |
    |**Additional allowed session types**     | Select **IPv6**.        |

1. Select **Add**.
1. On the **Basics** configuration tab, select **Review + create**.
1. The Azure portal will display the following confirmation screen when the SIM policy has been created.

    :::image type="content" source="media\configure-sim-policy-azure-portal\sim-policy-deployment-confirmation.png" alt-text="Screenshot of the Azure portal showing confirmation of the successful deployment of a SIM policy.":::

1. Select **Go to resource group**.
1. In the resource group that appears, select the **Mobile network** resource representing your private mobile network.
1. In the resource menu, select **SIM policies**.

    :::image type="content" source="media\configure-sim-policy-azure-portal\sim-policies-resource-menu-option.png" alt-text="Screenshot of the Azure portal showing the SIM policies option in the resource menu of a Mobile Network resource.":::

1. Select **sim-policy-1**.

    :::image type="content" source="media\sim-policies-list.png" alt-text="Screenshot of the Azure portal with a list of configured SIM policies for a private mobile network. The sim-policy-1 resource is highlighted.":::

1. Check that the configuration for the SIM policy is as expected.

    - The top level settings for the SIM policy are shown under the **Essentials** heading.
    - The network scope configuration is shown under the **Network scope** and **Quality of service (QoS)** headings.
    - The configured services are shown under the **Service configuration heading**.
    
    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\complete-example-sim-policy-1.png" alt-text="Screenshot of the Azure portal showing a SIM policy resource. Essentials, network scope, and service configuration are highlighted." "lightbox="media\tutorial-create-example-set-of-policy-control-configuration\complete-example-sim-policy-1.png":::

1. We'll now create the other SIM policy. Search for and select the Mobile Network resource representing the private mobile network for which you want to configure a service.

    :::image type="content" source="media/mobile-network-search.png" alt-text="Screenshot of the Azure portal showing the results for a search for a Mobile Network resource.":::

1. In the resource menu, select **SIM policies**.

    :::image type="content" source="media\configure-sim-policy-azure-portal\sim-policies-resource-menu-option.png" alt-text="Screenshot of the Azure portal showing the SIM policies option in the resource menu of a Mobile Network resource.":::

1. In the command bar, select **Create**.
1. On the **Create a SIM policy** blade that appears, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Policy name**     |`sim-policy-2`         |
    |**Total bandwidth allowed - Uplink**     | `10 Gbps`        |
    |**Total bandwidth allowed - Downlink**     | `10 Gbps` |
    |**Default slice**     | `slice-1`        |
    |**Registration timer**     | `3240`        |
    |**RFSP index**     | `2`        |

1. Select **Add a network scope**.
1. On the **Add a network scope** blade, fill out the fields as follows.

    |Field  |Value  |
    |---------|---------|
    |**Slice**     | Select **slice-1 (Default)**         |
    |**Data network**     | Select the data network to which your private mobile network connects.        |
    |**Service configuration**     | Select **service_blocking_udp_from_specific_sources** and **service_traffic_limits**. |
    |**Session aggregate maximum bit rate - Uplink**     | `2 Gbps`        |
    |**Session aggregate maximum bit rate - Downlink**     | `2 Gbps`        |
    |**5G QoS Indicator (5QI)**     | `9`        |
    |**Allocation and Retention Priority level**     | `9`        |
    |**Preemption capability**     | Select **May not preempt**.        |
    |**Preemption vulnerability**     | Select **Preemptable**.        |
    |**Default session type**     | Select **IPv4**.        |
    |**Additional allowed session types**     | Select **IPv6**.        |

1. Select **Add**.
1. On the **Basics** configuration tab, select **Review + create**.
1. On the **Review + create** configuration tab, select **Review + create**.
1. The Azure portal will display the following confirmation screen when the SIM policy has been created.

    :::image type="content" source="media\configure-sim-policy-azure-portal\sim-policy-deployment-confirmation.png" alt-text="Screenshot of the Azure portal showing confirmation of the successful deployment of a SIM policy.":::

1. Select **Go to resource group**.
1. In the resource group that appears, select the **Mobile network** resource representing your private mobile network.
1. In the resource menu, select **SIM policies**.

    :::image type="content" source="media\configure-sim-policy-azure-portal\sim-policies-resource-menu-option.png" alt-text="Screenshot of the Azure portal showing the SIM policies option in the resource menu of a Mobile Network resource.":::

1. Select **sim-policy-2**.

    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\sim-policies-list-example-2.png" alt-text="Screenshot of the Azure portal with a list of configured SIM policies for a private mobile network. The sim-policy-2 resource is highlighted.""lightbox="media\tutorial-create-example-set-of-policy-control-configuration\sim-policies-list-example-2.png":::

1. Check that the configuration for the SIM policy is as expected.

    - The top level settings for the SIM policy are shown under the **Essentials** heading.
    - The network scope configuration is shown under the **Network scope** and **Quality of service (QoS)** headings.
    - The configured services are shown under the **Service configuration heading**.
    
    :::image type="content" source="media\tutorial-create-example-set-of-policy-control-configuration\complete-example-sim-policy-2.png" alt-text="Screenshot of the Azure portal showing a SIM policy resource. Essentials, network scope, and service configuration are highlighted." "lightbox="media\tutorial-create-example-set-of-policy-control-configuration\complete-example-sim-policy-2.png":::

## Provision SIMs

In this step, we will provision two SIMs and assign a SIM policy to each one. This will allow the SIMs to connect to the private mobile network and receive the correct QoS policy.

1. Save the following content as a JSON file and make a note of the filepath.
    ```json
    [
     {
      "simName": "SIM1",
      "integratedCircuitCardIdentifier": "8912345678901234566",
      "internationalMobileSubscriberIdentity": "001019990010001",
      "authenticationKey": "00112233445566778899AABBCCDDEEFF",
      "operatorKeyCode": "63bfa50ee6523365ff14c1f45f88737d",
      "deviceType": "Cellphone"
     },
     {
      "simName": "SIM2",
      "simProfileName": "profile2",
      "integratedCircuitCardIdentifier": "8922345678901234567",
      "internationalMobileSubscriberIdentity": "001019990010002",
      "authenticationKey": "11112233445566778899AABBCCDDEEFF",
      "operatorKeyCode": "63bfa50ee6523365ff14c1f45f88738d",
      "deviceType": "Sensor"
     }
    ]
    ```
1. Search for and select the Mobile Network resource representing your private mobile network.

    :::image type="content" source="media/mobile-network-search.png" alt-text="Screenshot of the Azure portal showing the results for a search for a Mobile Network resource.":::

1. In the resource menu, select **Add SIMs**.

    :::image type="content" source="media/provision-sims-azure-portal/add-sims.png" alt-text="Screenshot of the Azure portal showing the Add SIMs button on a Mobile Network resource":::

1. Select **Create** and then **Upload JSON from file**.

    :::image type="content" source="media/provision-sims-azure-portal/create-new-sim.png" alt-text="Screenshot of the Azure portal showing the Create button and its options - Upload J S O N from file and Add manually.":::

1. Select **Browse** and then select the JSON file you created at the start of this step.
1. Select **Add**.
1. The Azure portal will now begin deploying the SIMs. When the deployment is complete, select **Go to resource group**.

    :::image type="content" source="media/provision-sims-azure-portal/multiple-sim-resource-deployment.png" alt-text="Screenshot of the Azure portal showing a completed deployment of SIM resources through a J S O N file and the Go to resource button.":::

1. In the resource group that appears, select the **Mobile network** resource representing your private mobile network.
1. In the resource menu, select **SIMs**.

    :::image type="content" source="media/tutorial-create-example-set-of-policy-control-configuration/sims-resource-menu-option.png" alt-text="Screenshot of the Azure portal. The SIMs option in the resource menu for a private mobile network is highlighted.":::

1. Your new **SIM1** and **SIM2** SIM resources are shown in the list.

    :::image type="content" source="media/tutorial-create-example-set-of-policy-control-configuration/sims-list.png" alt-text="Screenshot of the Azure portal. It shows the SIMs currently provisioned for the private mobile network.":::

1. Tick the checkbox next to **SIM1**.
1. In the command bar, select **Assign SIM policy**.
1. Under **Assign SIM policy** on the right, set the **SIM policy** field to **sim-policy-1**.
1. Select the **Assign SIM policy** button.
1. Once the deployment is complete, select **Go to Resource**.
1. Check the **SIM policy** field in the **Management** section to confirm **sim-policy-1** has been successfully assigned.
1. Search for and select the Mobile Network resource representing your private mobile network.

    :::image type="content" source="media/mobile-network-search.png" alt-text="Screenshot of the Azure portal showing the results for a search for a Mobile Network resource.":::

1. In the resource menu, select **SIMs**.

    :::image type="content" source="media/tutorial-create-example-set-of-policy-control-configuration/sims-resource-menu-option.png" alt-text="Screenshot of the Azure portal. The SIMs option in the resource menu for a private mobile network is highlighted.":::

1. Tick the checkbox next to **SIM2**.
1. In the command bar, select **Assign SIM policy**.
1. Under **Assign SIM policy** on the right, set the **SIM policy** field to **sim-policy-2**.
1. Select the **Assign SIM policy** button.
1. Once the deployment is complete, select **Go to Resource**.
1. Check the **SIM policy** field in the **Management** section to confirm **sim-policy-1** has been successfully assigned.

You now have now provisioned two SIMs and assigned each of them a different SIM policy. Each of these SIM policies provides access to a different set of services.

## Clean up resources

You can now delete each of the resources we've created during this tutorial.

1. Search for and select the Mobile Network resource representing your private mobile network.
1. In the resource menu, select **SIMs**.
1. Tick the checkboxes next to **SIM1** and **SIM2**, and then select **Delete** from the command bar. 
1. Select **Delete** to confirm your choice.
1. Once the SIMs have been deleted, select **SIM policies** from the resource menu.
1. Tick the checkboxes next to **sim-policy-1** and **sim-policy-2**, and then select **Delete** from the command bar.
1. Select **Delete** to confirm your choice.
1. Once the SIM policies have been deleted, select **Services** from the resource menu.
1. Tick the checkboxes next to **service_unrestricted_udp_and_icmp**, **service_blocking_udp_from_specific_sources**, and **service_traffic_limits**, and then select **Delete** from the command bar.

## Next steps

> [!div class="nextstepaction"]
> [Find out how to design your own policy control configuration](policy-control.md)
