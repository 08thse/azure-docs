---
title: 
description: 
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic:  quickstart
ms.date: 01/05/2021
ms.author: cshoe
---

# Configure Azure Static Web Apps

Configuration for Azure Static Web Apps is defined in the _staticwebapp.config.json_ file which defines the following rules:

- Back-end routing
- Authentication and authorization
- Global header definitions
- Response overrides

The _staticwebapp.config.json_ file may be placed in any location under the _app_ folder.

See the [example configuration](#example-configuration-file) file for details.

> [!IMPORTANT] The [_routes.json_](./routes.md) file is ignored if a _staticwebapp.config.json_ exists.

## Routes

Routing rules are defined as an array in the _staticwebapp.config.json_ file.

- Rules are executed in the order as they appear in the `routes` array.
- Rule evaluation stops at the first match. Routing rules are not chained together.
- Roles are defined in the _staticwebapp.config.json_ file and users are associated to roles via [invitations](./authentication-authorization.md).
- You have full control over role names.

The topic of routing significantly overlaps with authentication and authorization concepts. Make sure to read the [authentication and authorization](authentication-authorization.md) guide along with this article.

## Defining routes

Routes are defined in the _routes.json_ file as an array of rules on the `routes` property. Each rule is composed of a route pattern, along with one or more of the optional rule properties. See the [example route file](#example-route-file) for usage examples.

| Rule property  | Required | Default value | Comment                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Yes      | n/a          | The route pattern requested by the caller.<ul><li>[Wildcards](#wildcards) are supported at the end of route paths. For instance, the route _admin/\*_ matches any route under the _admin_ path.<li>A route serves the folder's _index.html_ file by default.</ul>|
| `redirect`        | No       | n/a          | Defines the file or path destination for a new request.<ul><li>Redirect rules change the brower's location<li>Default response code is [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302), but you can override with a [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) redirect<li>Querystring parameters are not supported</ul> |
| `rewrite`        | No       | n/a          | Defines the file or path returned from the request.<ul><li>Rewrite rules do not change the brower's location<li>Values must point to actual files.<li>Querystring parameters are not supported</ul>  |
| `allowedRoles` | No       | anonymous     | An array of role names. <ul><li>Valid characters include `a-z`, `A-Z`, `0-9`, and `_`.<li>The built-in role [`anonymous`](./authentication-authorization.md) applies to all unauthenticated users.<li>The built-in role [`authenticated`](./authentication-authorization.md) applies to any logged-in user.<li>Users must belong to at least one role.<li>Roles are matched on an _OR_ basis. If a user is in any of the listed roles, then access is granted.<li>Individual users are associated to roles through [invitations](authentication-authorization.md).</ul> |
| `statusCode`   | No       | 200           | The [HTTP status code](https://developer.mozilla.org/docs/Web/HTTP/Status) response for the request. |

## Securing routes with roles

Routes are secured by adding one or more role names into a rule's `allowedRoles` array. See the [example route file](#example-route-file) for usage examples.

By default, every user belongs to the built-in `anonymous` role, and all logged-in users are members of the `authenticated` role.

For instance, to restrict a route to only authenticated users, add the built-in `authenticated` role to the `allowedRoles` array.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

You can create new roles as needed in the `allowedRoles` array. To restrict a route to only administrators, you could define an `administrator` role in the `allowedRoles` array.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- You have full control over role names; there's no master list to which your roles must adhere.
- Individual users are associated to roles through [invitations](authentication-authorization.md).

## Wildcards

Wildcard rules match all requests under a given route pattern. Both `rewrite` and `redirect` rules support wildcards.

For instance, to implement routes for a calendar application, you can redirect all URLs that fall under the _calendar_ route to serve a single file.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

The _calendar.html_ file can then use client-side routing to serve a different view for URL variations like `/calendar/january/1`, `/calendar/2020`, and `/calendar/overview`.

If you don't want a wildcard route to apply JavaScript, CSS and image files, consider placing files referenced in your HTML in a separate folder outside the scope of the wildcard rule.

### Securing routes with wildcards

You can also secure routes with wildcards. In the following example, any file requested under the _admin_ path requires an authenticated user who is a member of the _administrator_ role.

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

## Wildcard routes

- Auth rules
- Caching rules (images, CSS)
- Map to methods

## Global headers

Used to be default headers

## Response overrides

## Example configuration file

## Next steps

> [!div class="nextstepaction"]
> [Setup authentication and authorization](authentication-authorization.md)