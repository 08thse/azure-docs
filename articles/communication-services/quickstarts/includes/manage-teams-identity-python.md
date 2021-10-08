---
title: include file
description: include file
services: azure-communication-services
author: gistefan
manager: soricos

ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
---

## Prerequisites

- [Python](https://www.python.org/downloads/) 2.7 or 3.6+.

## Setting Up

### Create a new Python application

1. Open your terminal or command window create a new directory for your app, and navigate to it.

   ```console
   mkdir teams-access-tokens-quickstart && cd teams-access-tokens-quickstart
   ```

1. Use a text editor to create a file called **exchange-teams-access-tokens.py** in the project root directory and add the structure for the program, including basic exception handling. You'll add all the source code for this quickstart to this file in the following sections.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### Install the package

While still in the application directory, install the Azure Communication Services Identity SDK for Python package by using the `pip install` command.

```console
pip install azure-communication-identity
pip install msal
```

### Step 1: Receive the Azure AD user token via the MSAL library

First step in the token exchange flow is getting a token for your Teams user by using the [Microsoft.Identity.Client](https://docs.microsoft.com/en-us/azure/active-directory/develop/reference-v2-libraries).

```python
from msal import PublicClientApplication

client_id = "Contoso's_Application_ID"
authority = "https://login.microsoftonline.com/common"

app = PublicClientApplication(client_id, authority=authority)

scope = "https://auth.msft.communication.azure.com/VoIP"
teams_token_result = app.acquire_token_interactive(scope)

```

### Step 2: Initialize the CommunicationIdentityClient

Instantiate a `CommunicationIdentityClient` with your connection string. The code below retrieves the connection string for the resource from an environment variable named `COMMUNICATION_SERVICES_CONNECTION_STRING`. Learn how to [manage your resource's connection string](../create-communication-resource.md#store-your-connection-string).

Add this code inside the `try` block:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

Alternatively, if you have an Azure Active Directory(AD) application set up, see [Use service principals](../identity/service-principal.md), you may also authenticate with AD.
```python
endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"]
client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
```

### Step 3: Exchange the Azure AD user token for the Teams access token

Use the `ExchangeTeamsTokenAsync` method to issue an access token for the Teams user that can be used with the Azure Communicatin Services SDKs.

```python
token_result = client.exchange_user_token(teams_token_result.access_token);
print("Token: " + token_result.token)
```

## Run the code

From a console prompt, navigate to the directory containing the *exchange-teams-access-tokens.py* file, then execute the following `python` command to run the app.

```console
python ./exchange-teams-access-tokens.py
```
