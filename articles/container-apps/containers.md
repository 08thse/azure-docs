---
title: Containers in Azure Container Apps
description: Learn how containers are managed and configured in Azure Container Apps
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic:  conceptual
ms.date: 09/16/2021
ms.author: cshoe
---

# Containers in Azure Container Apps

Azure Container Apps manages the details of Kubernetes and container orchestrations for you. Containers in Azure Container Apps can use any runtime, programming language, or development stack of your choice.

Azure Container Apps supports:

- Any Linux-based container image
- Containers from any container registry

There is no required base container image when running containers in Azure Container Apps.

## Configuration

The following example configuration shows the options available when setting up a container.

Any changes to the `containers` section triggers a new [container app revision](application-lifecycle-management.md).

```json
{
  ...
  "template": {
    "containers": [
      {
        "image": "myacr.azurecr.io/myrepo/api-service:v1",
        "name": "my-container-image",
        "command": ["/bin/queue"],
        "args": [],
        "env": [
          {
            "name": "HTTP_PORT",
            "value": "8080"
          }
        ],
        "resources": {
            "cpu": 1,
            "memory": "250Mb"
        }
    }]
  }
}
```

| Setting | Description | Remarks |
|---|---|---|
| `image` | The container image name for your container app. | This value takes the form of `publisher/image-name:tag`. |
| `name` | Friendly name of the container. | Used for reporting and identification. |
| `command` | The container's startup command. | Equivalent to Docker's [entrypoint](https://docs.docker.com/engine/reference/builder/) field. The values in the array are joined together and separated by spaces. This concatenation allows you to have spaces in your startup command.  |
| `args` | Start up command arguments. | Entries in the array are joined together to create a parameter list to pass to the startup command. |
| `env` | An array of key/value pairs that define environment variables. | |
| `resources.cpu` | The number of CPUs allocated to the container. | Possible values include: `1`, `2`, or `4`. |
| `resources.memory` | The amount of RAM allocated to the container. | Possible values include: `250mb`. Each [replica](application-lifecycle-management.md) is assigned the amount of memory defined here. |

## Multiple containers

You can define multiple containers in a single container app. Groups of containers are known as [pods](https://kubernetes.io/docs/concepts/workloads/pods). The containers in a pod share hard disk and network resources and experience the same [application lifecycle](application-lifecycle-managment.md).

You run multiple containers together by defining more than one container in the configuration's `containers` array.

Reasons to run containers together include:

- Use a container as a sidecar to your primary app.
- Use of a shared disk space and virtual network.
- Share scale rules among containers.
- Group together multiple containers that need to always run together.
- Enable direct communication on the same host.

Reasons to separate containers include:

- If you want to run an app as a separate microservice.
- If your apps have different scaling needs.

## Container registries

You can deploy images hosted on private registries where credentials are provided through the Container Apps configuration.

To use a container registry, you first define the required fields to the configuration's `registries` section.

```json
{
  ...
  "registries": {
    "server": "docker.io",
    "username": "my-registry-user-name",
    "passwordSecretRef": "my-password-secretref-name"
  }
}
```

With this set up, the saved credentials are used when you reference a container image in an `image` in the `containers` array.

The following example shows how to deploy an app from the Azure Container Registry.

# [ARM template](#tab/arm-template)

```json
{
  ...
  "configuration": {
      "secrets": [
          {
              "name": "acr-password",
              "value": "my-acr-password"
          }
      ],
      "registries": [
          {
              "server": "myacr.azurecr.io",
              "username": "someuser",
              "passwordSecretRef": "acr-password"
          }
      ]
  }
}
```

# [Azure CLI](#tab/azure-cli)

```sh
# To create a container app
az containerapp create --name myapp \
  --resource-group $RESOURCE_GROUP_NAME \
  --environment $CONTAINERAPPS_ENVIRONMENT_NAME \
  --image myacr.azurecr.io/myrepo/api-service:v1 \
  --registry-login-server myacr.azurecr.io \
  --registry-username someuser \
  --registry-password myacrpassword

# To update an existing container app
az containerapp update --name myapp \
  --resource-group $RESOURCE_GROUP_NAME \
  --image myacr.azurecr.io/myrepo/api-service:v1 \
  --registry-login-server myacr.azurecr.io \
  --registry-username someuser \
  --registry-password myacrpassword
```

---

## Limitations

Azure Container Apps has the following limitations:

- **Privileged containers**: Azure Container Apps can't run privileged containers. If your program attempts to run a process that requires root access, the application inside the container experiences a runtime error.

- **Operating system**: Requires Linux-based container images.

## Next steps

> [!div class="nextstepaction"]
> [Environment](environment.md)
