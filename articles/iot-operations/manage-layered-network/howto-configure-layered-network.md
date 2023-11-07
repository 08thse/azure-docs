---
title: Create sample network environment for Azure IoT Layered Network Management
# titleSuffix: Azure IoT Layered Network Management
description: Set up a test or sample network environment for Azure IoT Layered Network Management.
author: PatAltimore
ms.author: patricka
ms.topic: how-to
ms.date: 10/25/2023

#CustomerIntent: As an operator, I want to configure Layered Network Management so that I have secure isolate devices.
---

# Create sample network environment for Azure IoT Layered Network Management

[!INCLUDE [public-preview-note](../includes/public-preview-note.md)]

To use Azure IoT Layered Network Management service, you can configure an isolated network environment with physical or logical segmentation.

Each isolated layer that's level 3 and lower, requires you to configure a custom DNS.

## Configure isolated network with physical segmentation

The following example configuration is a simple isolated network with minimum physical devices.

![Diagram of a physical device isolated network configuration.](./media/howto-configure-layered-network/physical-device-isolated.png)

- The wireless access point is used for setting up a local network and doesn't provide internet access.
- **Level 4 cluster** is a single node cluster hosted on a dual network interface card (NIC) physical machine that connects to internet and the local network.
- **Level 3 cluster** is a single node cluster hosted on a physical machine. This device cluster only connects to the local network.

>[!IMPORTANT]
> When assigning local IP addresses, avoid using the default address `192.168.0.x`. You should change the address if it's the default setting for your access point.

Layered Network Management is deployed to the dual NIC cluster. The cluster in the local network connects to Layered Network Management as a proxy to access Azure and Arc services. Generally, it would need another DNS server in the local network to provide domain name resolution and point the traffic to Layered Network Management.

## Configure Isolated Network with logical segmentation

The following example is an isolated network environment where each level is logically segmented with subnets. In this test environment, there are multiple clusters one at each level. The clusters can be AKS Edge Essentials or K3S. The Kubernetes cluster in the level 4 network has direct internet access. The Kubernetes clusters in level 3 and below don't have internet access.

![Diagram of a logical segmentation isolated network](./media/howto-configure-layered-network/nested-edge.png)

The multiple levels of networks in this test setup are accomplished using subnets within a network:

- **Level 4 subnet (10.104.0.0/16)** - This subnet has access to the internet. All the requests are sent to the destinations on the internet. This subnet has a single Windows 11 machine with the IP address 10.104.0.10.
- **Level 3 subnet (10.103.0.0/16)** - This subnet doesn't have access to the internet and is configured to only have access to the IP address 10.104.0.10 in Level 4. This subnet contains a Windows 11 machine with the IP address 10.103.0.33 and a Linux machine that hosts a DNS server. The DNS server is configured using the steps in [configure the DNS server](#configure-the-dns-server). All the domains in the DNS configuration must be mapped to the address 10.104.0.10.
- **Level 2 subnet (10.102.0.0/16)** - Like Level 3, this subnet doesn't have access to the internet. It's configured to only have access to the IP address 10.103.0.33 in Level 3. This subnet contains a Windows 11 machine with the IP address 10.102.0.28 and a Linux machine that hosts a DNS server. There's one Windows 11 machine (node) in this network with IP address 10.102.0.28. All the domains in the DNS configuration must be mapped to the address 10.103.0.33.

## Configure custom DNS
A custom DNS is needed for level 3 and below. In an existing or production environment, please incorporate the DNS resolutions below into the DNS mechanism of your design. If you want to setup a test environment for Layered Network Management service and Azure IoT Operations, you can refer to one of the examples below.

# [DNS Server](#tab/dnsserver)

## Configure the DNS server

A DNS server is only needed for levels 3 and below. This example uses a [dnsmasq](https://dnsmasq.org/) server, running on Ubuntu for DNS resolution.

1. Install an Ubuntu machine in the local network.
1. Enable the *dnsmasq* service on the Ubuntu machine.

    ```bash
    apt update
    apt install dnsmasq
    systemctl status dnsmasq
    ```
1. Modify the `/etc/dnsmasq.conf` file as shown to route these domains to the upper level.
    - Change the IPv4 address from 10.104.0.10 to respective destination address for that level. In this case, the IP address of the Layered Network Management service in the parent level.
    - Verify the `interface` where you're running the *dnsmasq* and change the value as needed.

    The following configuration only contains the necessary endpoints for enabling Azure IoT Operations.

    ```conf
    # Add domains which you want to force to an IP address here.
    address=/management.azure.com/10.104.0.10
    address=/dp.kubernetesconfiguration.azure.com/10.104.0.10
    address=/.dp.kubernetesconfiguration.azure.com/10.104.0.10
    address=/login.microsoftonline.com/10.104.0.10
    address=/.login.microsoft.com/10.104.0.10
    address=/.login.microsoftonline.com/10.104.0.10
    address=/login.microsoft.com/10.104.0.10
    address=/mcr.microsoft.com/10.104.0.10
    address=/.data.mcr.microsoft.com/10.104.0.10
    address=/gbl.his.arc.azure.com/10.104.0.10
    address=/.his.arc.azure.com/10.104.0.10
    address=/k8connecthelm.azureedge.net/10.104.0.10
    address=/guestnotificationservice.azure.com/10.104.0.10
    address=/.guestnotificationservice.azure.com/10.104.0.10
    address=/sts.windows.nets/10.104.0.10
    address=/k8sconnectcsp.azureedge.net/10.104.0.10
    address=/.servicebus.windows.net/10.104.0.10
    address=/servicebus.windows.net/10.104.0.10
    address=/obo.arc.azure.com/10.104.0.10
    address=/.obo.arc.azure.com/10.104.0.10
    address=/adhs.events.data.microsoft.com/10.104.0.10
    address=/dc.services.visualstudio.com/10.104.0.10
    address=/go.microsoft.com/10.104.0.10
    address=/onegetcdn.azureedge.net/10.104.0.10
    address=/www.powershellgallery.com/10.104.0.10
    address=/self.events.data.microsoft.com/10.104.0.10
    address=/psg-prod-eastus.azureedge.net/10.104.0.10
    address=/.azureedge.net/10.104.0.10
    address=/api.segment.io/10.104.0.10
    address=/nw-umwatson.events.data.microsoft.com/10.104.0.10
    address=/sts.windows.net/10.104.0.10
    address=/.azurecr.io/10.104.0.10
    address=/.blob.core.windows.net/10.104.0.10
    address=/global.metrics.azure.microsoft.scloud/10.104.0.10
    address=/.prod.hot.ingestion.msftcloudes.com/10.104.0.10
    address=/.prod.microsoftmetrics.com/10.104.0.10
    address=/global.metrics.azure.eaglex.ic.gov/10.104.0.10

    # --address (and --server) work with IPv6 addresses too.
    address=/guestnotificationservice.azure.com/fe80::20d:60ff:fe36:f83
    address=/.guestnotificationservice.azure.com/fe80::20d:60ff:fe36:f833
    address=/.servicebus.windows.net/fe80::20d:60ff:fe36:f833
    address=/servicebus.windows.net/fe80::20d:60ff:fe36:f833

    # If you want dnsmasq to listen for DHCP and DNS requests only on
    # specified interfaces (and the loopback) give the name of the
    # interface (eg eth0) here.
    # Repeat the line for more than one interface.
    interface=enp1s0

    listen-address=::1,127.0.0.1,10.102.0.72

    no-hosts
    ```

1. As an alternative, you can put `address=/#/<IP of upper level Layered Network Management service>` in the IPv4 address section. For example:

    ```conf
    # Add domains which you want to force to an IP address here.
    address=/#/<IP of upper level Layered Network Management service>

    # --address (and --server) work with IPv6 addresses too.
    address=/#/fe80::20d:60ff:fe36:f833

    # If you want dnsmasq to listen for DHCP and DNS requests only on
    # specified interfaces (and the loopback) give the name of the
    # interface (eg eth0) here.
    # Repeat the line for more than one interface.
    interface=enp1s0

    listen-address=::1,127.0.0.1,10.102.0.72

    no-hosts
    ```

1. Restart the *dnsmasq* service to apply the changes.

    ```bash
    sudo systemctl restart dnsmasq
    systemctl status dnsmasq
    ```

# [CoreDNS](#tab/coredns)

## Configure CoreDNS

### Create configmap from level 4 Layered Network Management
After the level 4 cluster and Layered Network Management are ready, perform the following steps.
1. Confirm the IP address of Layered Network Management service with the following command:
    ```bash
    kubectl get services
    ```
    The output should look like the following. The IP address of the service is `20.81.111.118`.
    ```
    NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    lnm-level4   LoadBalancer   10.0.141.101   20.81.111.118   80:30960/TCP,443:31214/TCP   29s
    ```
1. View the config maps with following command:
    ```bash
    kubectl get cm
    ```
    The output should look like the following:
    ```
    NAME                           DATA   AGE
    aio-lnm-level4-config          1      50s
    aio-lnm-level4-client-config   1      50s
    ```
1. Customize the `aio-lnm-level4-client-config`. This will be needed as part of the level 3 setup to forward traffic from the level 3 cluster to the top level Layered Network Management instance.
    ```bash
    # set the env var PARENT_IP_ADDR to the ip address of level 4 LNM instance.
      export PARENT_IP_ADDR="20.81.111.118"
    
    # run the script to generate a config map yaml
      kubectl get cm aio-lnm-level4-client-config -o yaml | yq eval '.metadata = {"name": "coredns-custom", "namespace": "kube-system"}' -| sed 's/PARENT_IP/'"$PARENT_IP_ADDR"'/' > configmap-custom-level4.yaml
    ```
    This step creates a file named `configmap-custom-level4.yaml`

### Configure the level 3 CoreDNS of K3S

---

## Related content

[What is Azure IoT Layered Network Management?](./overview-layered-network.md)


