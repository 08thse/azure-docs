---
title: Server variables in Azure Front Door Standard/Premium
description: This article provides a list of the server variables available in Azure Front Door Standard/Premium rule sets.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/03/2022
ms.author: yuajia
---

# Azure Front Door Standard/Premium (Preview) Rule Set server variables

Rule Set server variables provide access to structured information about the request when you work with [Rule Sets](concept-rule-set.md).

When you use [Rule Set match conditions](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json), server variables are available as match conditions so that you can identify requests with specific properties.

When you use [Rule Set actions](concept-rule-set-actions.md), you can use server variables to dynamically change the request and response headers, and rewrite URLs, paths, and query strings, for example, when a new page loads or when a form is posted.

> [!NOTE]
> Server variable is only applicable to Azure Front Door Standard and Premium SKUs. 

## Supported variables

| Variable name | Description |
|-|-|
| `socket_ip`      | The IP address of the direct connection to Azure Front Door edge. If the client used an HTTP proxy or a load balancer to send the request, the value of `socket_ip` is the IP address of the proxy or load balancer.<br/> To access this server variable in a match condition, use [Socket address](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#socket-address). |
| `client_ip`      | The IP address of the client that made the original request. If there was an `X-Forwarded-For` header in the request, then the client IP address is picked from the header.<br />To access this server variable in a match condition, use [Remote address](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#remote-address) and configure the *Operator* to *IP Match* or *IP Not Match*. |
| `client_port`    | The IP port of the client that made the request. <br/> To access this server variable in a match condition, use [Client port](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#client-port).|
| `hostname`       | The host name in the request from the client. <br/> To access this server variable in a match condition, use [Host name](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#host-name).|
| `geo_country`    | Indicates the requester's country/region of origin through its country/region code. <br/> To access this server variable in a match condition, use [Remote address](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#remote-address) and configure the *Ooperator* to *Geo Match* or *Geo Not Match*.|
| `http_method`    | The method used to make the URL request, such as `GET` or `POST`.<br/> To access this server variable in a match condition, use [Request method](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#request-method).|
| `http_version`   | The request protocol. Usually `HTTP/1.0`, `HTTP/1.1`, or `HTTP/2.0`.<br/> To access this server variable in a match condition, use [HTTP version](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#http-version).|
| `query_string`   | The list of variable/value pairs that follows the "?" in the requested URL.<br />For example, in the request `http://contoso.com:8080/article.aspx?id=123&title=fabrikam`, the `query_string` value will be `id=123&title=fabrikam`.<br/> To access this server variable in a match condition, use [Query string](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#query-string).|
| `request_scheme` | The request scheme: `http` or `https`.<br/> To access this server variable in a match condition, use [Request protocol](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#request-protocol).|
| `request_uri`    | The full original request URI (with arguments).<br />For example, in the request `http://contoso.com:8080/article.aspx?id=123&title=fabrikam`, the `request_uri` value will be `/article.aspx?id=123&title=fabrikam`.<br/> To access this server variable in a match condition, use [Request URL](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#request-url).|
| `ssl_protocol`   | The protocol of an established TLS connection.<br/> To access this server variable in a match condition, use [SSL protocol](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#ssl-protocol).|
| `server_port`    | The port of the server that accepted a request.<br/> To access this server variable in a match condition, use [Server port](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#server-port).|
| `url_path`       | Identifies the specific resource in the host that the web client wants to access. This is the part of the request URI without the arguments.<br />For example, in the request `http://contoso.com:8080/article.aspx?id=123&title=fabrikam`, the `uri_path` value will be `/article.aspx`.<br/> To access this server variable in a match condition, use [Request path](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json#request-path).|

## Server variable format 

When you work with Rule Set actions, specify server variables by using the following formats:

* `{variable}`: Include the entire server variable. For example, if the client IP address is `111.222.333.444` then the `{client_ip}` token would evaluate to `111.222.333.444`.
* `{variable:offset}`: Include the server variable after a specific offset, until the end of the variable. The offset is zero-based. For example, if the client IP address is `111.222.333.444` then the `{client_ip:3}` token would evaluate to `.222.333.444`.
* `{variable:offset:length}`: Include the server variable after a specific offset, up to the specified length. The offset is zero-based. For example, if the client IP address is `111.222.333.444` then the `{client_ip:4:3}` token would evaluate to `222`.

## Supported rule set actions

Server variables are supported on the following Rule Set actions:

* Query string caching behavior in [Route configuration override](concept-rule-set-actions.md#RouteConfigurationOverride)
* [Modify request header](concept-rule-set-actions.md#ModifyRequestHeader)
* [Modify response header](concept-rule-set-actions.md#ModifyResponseHeader)
* [URL redirect](concept-rule-set-actions.md#UrlRedirect)
* [URL rewrite](concept-rule-set-actions.md#UrlRewrite)

## Next steps

* Learn more about [Azure Front Door Standard/Premium Rule Set](concept-rule-set.md).
* Learn more about [Rule Set match conditions](../rules-match-conditions.md?toc=%2fazure%2ffrontdoor%2fstandard-premium%2ftoc.json).
* Learn more about [Rule Set actions](concept-rule-set-actions.md).
