---
title: Configure traffic mirroring with an encapsulated remote switched port analyzer (ERSPAN) - Microsoft Defender for IoT
description: This article describes traffic mirroring with ERSPAN for monitoring with Microsoft Defender for IoT.
ms.date: 09/20/2022
ms.topic: how-to
---

# Configure traffic mirroring with an encapsulated remote switched port analyzer (ERSPAN)

Use an encapsulated remote switched port analyzer (ERSPAN) to mirror input interfaces to your OT sensor's monitoring interface, to monitor the input traffic with Defender for IoT.

When configuring ERSPAN, we recommend using your receiving router as the generic routing encapsulation (GRE) tunnel destination.

The sensor's monitoring interface doesn't have a specifically allocated IP address <!--it doesn't?-->, and when ERSPAN support is configured, GRE headers are stripped from the monitored traffic.<!--i don't understand any of this. does it make sense?-->

<!--Use this method when TBD-->

> [!NOTE]
> This article provides high-level guidance for configuring traffic mirroring with ERSPAN. Specific implementation details will vary depending on your equiptment vendor.
>

## ERSPAN architecture

ERSPAN sessions include a source session and a destination session configured on different switches. Between the source and destination switches, traffic is encapsulated in GRE, and can be routed over layer 3 networks.

For example:

:::image type="content" source="../media/traffic-mirroring/erspan.png" alt-text="Diagram of traffic mirrored from an air-gapped or industrial network to an OT network sensor using ERSPAN.":::

ERSPAN transports mirrored traffic over an IP network using the following process:

1. A source router encapsulates the traffic and sends the packet over the network.
1. At the destination router, the packet is de-capsulated and sent to the destination interface.

ERSPAN source options include elements such as:

- Ethernet ports and port channels
- VLANs; all supported interfaces in the VLAN are ERSPAN sources
- Fabric port channels
- Satellite ports and host interface port channels

## Configure ERSPAN on your OT network sensor

Newly installed OT network sensors have ERSPAN and GRE header stripping turned off by default. To turn on support for ERSPAN, you'll need to configure your ERSPAN interfaces and then enable the RCDCAP component to restart your monitoring processes:


**To turn on support for ERSPAN on your OT sensor**:

1. Sign in to your sensor via SSH. <!--which user?-->

1. Run the following command to open the `network.properties` for editing:

    ```cli
    sudo nano /var/cyberx/properties/network.properties
    ```

1. Modify the `monitor_erspan_interfaces` value with the interfaces that you want to monitor using ERSPAN. You do *not* need to run the `interfaces-apply` command as the update is automatically applied.

**To restart monitoring processes on your sensor**:

On your sensor, run:

```
sudo cyberx-xsense-components-enable -n "rcdcap"
```

**To verify your updates**:

On your sensor, run:

```cli
ifconfig
```

The system displays a list of all monitored interfaces. If you've configured ERSPAN correctly, you'll see a new interface with ERSPAN.

The ERSPAN interface renames and clones the original interface. The other interface is shown as empty. <!--unclear-->

## Sample configuration on a Cisco switch

The following code shows a sample `ifconfig` output for ERSPAN configured on a Cisco switch:

```cli
monitor session 1 type erspan-source
description ERSPAN to D4IoT
erspan-id 32                              # required, # between 1-1023
vrf default                               # required
destination ip 172.1.2.3                  # IP address of destination
source interface port-channel1 both       # Port(s) to be sniffed
filter vlan 1                             # limit VLAN(s) (optional)
no shut                                   # enable

monitor erspan origin ip-address 172.1.2.1 global
```

## Next steps

For more information, see:

- [Traffic mirroring methods for OT monitoring](../best-practices/traffic-mirroring-methods.md)
- [Prepare your OT network for Microsoft Defender for IoT](../how-to-set-up-your-network.md)