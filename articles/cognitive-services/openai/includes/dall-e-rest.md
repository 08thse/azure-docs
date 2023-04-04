---
title: 'Quickstart: Use the OpenAI Service image generation REST APIs'
titleSuffix: Azure OpenAI Service
description: Walkthrough on how to get started with Azure OpenAI image generation using the REST API. 
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: openai
ms.topic: include
ms.date: 04/04/2023
keywords: 
---

## Prerequisites

- An Azure subscription - <a href="https://azure.microsoft.com/free/cognitive-services" target="_blank">Create one for free</a>
- Access granted to Azure OpenAI in the desired Azure subscription
    Currently, access to this service is granted only by application. You can apply for access to the Azure OpenAI service by completing the form at <a href="https://aka.ms/oai/access" target="_blank">https://aka.ms/oai/access</a>. Open an issue on this repo to contact us if you have an issue.
- <a href="https://www.python.org/" target="_blank">Python 3.7.1 or later version</a>
- The following Python libraries: os, requests, json
- - An Azure OpenAI resource created in the East US region. For more information about model deployment, see the [resource deployment guide](../how-to/create-resource.md).

## Retrieve key and endpoint

To successfully make a call against Azure OpenAI, you'll need the following:

|Variable name | Value |
|--------------------------|-------------|
| `ENDPOINT`               | This value can be found in the **Keys & Endpoint** section when examining your resource from the Azure portal. Alternatively, you can find the value in **Azure OpenAI Studio** > **Playground** > **Code View**. An example endpoint is: `https://docs-test-001.openai.azure.com/`.|
| `API-KEY` | This value can be found in the **Keys & Endpoint** section when examining your resource from the Azure portal. You can use either `KEY1` or `KEY2`.|

Go to your resource in the Azure portal. The **Endpoint and Keys** can be found in the **Resource Management** section. Copy your endpoint and access key as you'll need both for authenticating your API calls. You can use either `KEY1` or `KEY2`. Always having two keys allows you to securely rotate and regenerate keys without causing a service disruption.

:::image type="content" source="../media/quickstarts/endpoint.png" alt-text="Screenshot of the overview blade for an OpenAI Resource in the Azure portal with the endpoint & access keys location circled in red." lightbox="../media/quickstarts/endpoint.png":::

## Create a new Python application

Create a new Python file called quickstart.py. Then open it up in your preferred editor or IDE.

1. Replace the contents of quickstart.py with the following code.

    ```python
    import requests
    import time
    import os
    api_base = 'YOUR_API_ENDPOINT_URL'
    api_key = 'YOUR_OPENAI_KEY'
    api_version = '2022-08-03-preview'
    url = "{}dalle/text-to-image?api-version={}".format(api_base, api_version)
    headers= { "api-key": api_key, "Content-Type": "application/json" }
    body = {
        "caption": "YOUR_IMAGE_PROMPT",
        "resolution": "1024x1024"
    }
    submission = requests.post(url, headers=headers, json=body)
    operation_location = submission.headers['Operation-Location']
    retry_after = submission.headers['Retry-after']
    status = ""
    while (status != "Succeeded"):
        time.sleep(int(retry_after))
        response = requests.get(operation_location, headers=headers)
        status = response.json()['status']
    image_url = response.json()['result']['contentUrl']
    ```

    > [!IMPORTANT]
    > Remember to remove the key from your code when you're done, and never post it publicly. For production, use a secure way of storing and accessing your credentials. For example, [Azure Key Vault](../../../key-vault/general/overview.md).

1. Run the application with the `python` command:

    ```console
    python quickstart.py
    ```

    The script will loop until the generated image is ready.

## Output

The output from a successful image generation API call will look like this.

```json
{
    "id": "6b22b459-485c-4730-893d-598ede809f2e",
    "result":
    {
        "caption": "An avocado chair.",
        "contentUrl": "<URL_TO_IMAGE>",
        "contentUrlExpiresAt": "2022-08-14T19:28:43Z",
        "createdDateTime": "2022-08-12T17:48:11Z"
    },
    "status": "Succeeded"
}
```

The image generation APIs come with a content moderation filter. If the service recognizes your prompt as harmful content, it won't return a generated image. For more information, see the [content filter](../concepts/content-filter.md) article. The system will return an operation status of `Failed` and the `error.code` in the message will be set to `ContentFilter`. Here is an example.

```json
{
    "id": "93e5e7c7-0ef4-4ba2-add3-3ed5751f4a94",
    "status": "Failed",
    "error":
    {
        "code": "ContentFilter",
        "message": "Your task failed as a result of our safety system."
    }
}
```


## Clean up resources

If you want to clean up and remove an OpenAI resource, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it.

- [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
- [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## Next steps

* tbd
* For more examples check out the [Azure OpenAI Samples GitHub repository](https://github.com/Azure/openai-samples).