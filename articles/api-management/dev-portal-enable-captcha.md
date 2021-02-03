---
title: Enable CAPTCHA
titleSuffix: Azure API Management
description: Learn how to enable CAPTCHA for your self-hosted developer portal.
author: erikadoyle
ms.author: apimpm
ms.date: 02/01/2021
ms.service: api-management
ms.topic: how-to
---

# Enable CAPTCHA in the self-hosted portal

In the previous tutorial [Self-host the portal](dev-portal-self-host-portal.md#configure-json-files-static-website-and-cors-settings), you may have disabled CAPTCHA through the `useHipCaptcha` setting. Communication with CAPTCHA happens through an endpoint, which lets Cross-Origin Resource Sharing (CORS) happen for only the managed developer portal hostname. If your portal is self-hosted, it uses a different hostname and CAPTCHA won't allow the communication.

## Update the JSON config files

To enable the CAPTCHA in your self-hosted portal:

1. Assign a custom domain (for example, `api.contoso.com`) to the **Developer portal** endpoint of your API Management service.

    This domain applies to the managed version of your portal and the CAPTCHA endpoint. See [Configure a custom domain name for your Azure API Management instance](configure-custom-domain.md).

1. Go to the `src` folder in your self-hosted portal.

1. Update the configuration `json` files:

    | File | New value | Note |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | Replace `<custom-domain>` with the custom domain you set up in the first step. |
    |  | `"useHipCaptcha": true` | Change the value to `true` |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | Replace `<custom-domain>` with the custom domain you set up in the first step. |
    |  | `"useHipCaptcha": true` | Change the value to `true` |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | Replace `<custom-domain>` with the custom domain you set up in the first step. |

1. Publish the portal.

1. Upload and host the newly published portal.

1. Expose the self-hosted portal through a custom domain.

The portal domain's first and second levels need to match the domain set up in the first step. For example, `portal.contoso.com`. The exact steps depend on your hosting platform of choice. If you used the Azure Storage Account, you can refer to [Map a custom domain to an Azure Blob Storage endpoint](../storage/blobs/storage-custom-domain-name.md) for instructions.

## Next steps

- [Migrate portal between services](dev-portal-migrate-portal-between-services.md)