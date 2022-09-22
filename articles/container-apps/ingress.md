---
title: Set up HTTPS ingress in Azure Container Apps
description: Enable public and private endpoints in your app with Azure Container Apps
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021, event-tier1-build-2022
---

# Set up HTTPS ingress in Azure Container Apps

Azure Container Apps allows you to expose your container app to the public web, to your VNET, or to other Container Apps within your environment by enabling ingress. When you enable ingress, you don't need to create an Azure Load Balancer, public IP address, or any other Azure resources to enable incoming HTTPS requests.

With ingress enabled, your container app features the following characteristics:

- Supports TLS termination
- Supports HTTP/1.1 and HTTP/2
- Supports WebSocket and gRPC
- HTTPS endpoints always use TLS 1.2, terminated at the ingress point
- Endpoints always expose ports 80 (for HTTP) and 443 (for HTTPS).
  - By default, HTTP requests to port 80 are automatically redirected to HTTPS on 443.
- Request timeout is 240 seconds.

## Configuration

Ingress is an application-wide setting. Changes to ingress settings apply to all revisions simultaneously, and don't generate new revisions.

The ingress configuration section has the following form:

```json
{
  ...
  "configuration": {
      "ingress": {
          "external": true,
          "targetPort": 80,
          "transport": auto
      }
  }
}
```

The following settings are available when configuring ingress:

| Property | Description | Values | Required |
|---|---|---|---|
| `external` | Your ingress IP and app fully qualified domain name (FQDN) can either be visible externally from the internet, or internally from a VNET depending on whether the app environment has an external or internal endpoint, respectively - or visibility from within the app environment only. |`true` for visibility from internet or VNET, depending on app environment endpoint is configured, `false` for visibility within app environment only. (default) | Yes |
| `targetPort` | The port your container listens to for incoming requests. | Set this value to the port number that your container uses. Your application ingress endpoint is always exposed on port `443`. | Yes |
| `transport` | You can use either HTTP/1.1 or HTTP/2, or you can set it to automatically detect the transport type. | `http` for HTTP/1, `http2` for HTTP/2, `auto` to automatically detect the transport type (default) | No |
| `allowInsecure` | Allows insecure traffic to your container app. | `false` (default), `true`<br><br>If set to `true`, HTTP requests to port 80 aren't automatically redirected to port 443 using HTTPS, allowing insecure connections. | No |

> [!NOTE]
> To disable ingress for your application, you can omit the `ingress` configuration property entirely.

## IP addresses and domain names

With ingress enabled, your application is assigned a fully qualified domain name (FQDN). The domain name takes the following forms:

|Ingress visibility setting | Fully qualified domain name |
|---|---|
| External | `<APP_NAME>.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io`|
| Internal | `<APP_NAME>.internal.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io` |

HTTP traffic is routed to individual applications based on the FQDN in the host header.

For applications with external ingress visibility, the following conditions apply:
- An internal Container Apps environment has a single private IP address for applications. For Container Apps in internal Environments, you must configure [DNS](./networking.md#dns) for VNET-scope ingress.
- An external Container App environment or Container App environment that is not in a VNET has a single public IP address for applications.

You can get access to the environment's unique identifier by querying the environment settings.

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

> [!div class="nextstepaction"]
> [Manage scaling](scale-app.md)
