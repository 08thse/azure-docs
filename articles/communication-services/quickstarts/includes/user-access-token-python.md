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

## Setting Up

### Create a new C# application

In a console window (such as cmd, PowerShell, or Bash), use the `dotnet new` command to create a new console app with the name `TeamsAccessTokensQuickstart`. This command creates a simple "Hello World" C# project with a single source file: **Program.cs**.

```console
dotnet new console -o TeamsAccessTokensQuickstart
```

Change your directory to the newly created app folder and use the `dotnet build` command to compile your application.

```console
cd TeamsAccessTokensQuickstart
dotnet build
```

### Install the package

While still in the application directory, install the Azure Communication Services Identity library for .NET package by using the `dotnet add package` command.

```console
dotnet add package Azure.Communication.Identity --version 1.0.0
dotnet add package Microsoft.Identity.Client -Version 4.36.2
```

### Set up the app framework

From the project directory:

1. Open **Program.cs** file in a text editor
1. Add a `using` directive to include the `Azure.Communication.Identity` namespace
1. Update the `Main` method declaration to support async code

Use the following code to begin:

```csharp
using System;
using System.Threading.Tasks;
using Azure;
using Azure.Core;
using Azure.Communication.Identity;

namespace TeamsAccessTokensQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Teams Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

### Step 1: Receive the Azure AD user token via the MSAL library

First step in the token exchange flow is getting a token for your Teams user by using the [Microsoft.Identity.Client](https://docs.microsoft.com/en-us/azure/active-directory/develop/reference-v2-libraries).

```csharp
string appId = "Contoso's_Application_ID";
string authority = "https://login.microsoftonline.com/common";
string redirectUri = "http://localhost";

var aadClient = PublicClientApplicationBuilder
                .Create(appId)
                .WithAuthority(authority)
                .WithRedirectUri(redirectUri)
                .Build();

string scope = "https://auth.msft.communication.azure.com/VoIP";

var teamsUserAadToken = await aadClient
                        .AcquireTokenInteractive(new List<string> { scope })
                        .ExecuteAsync();
```

### Step 2: Initialize the CommunicationIdentityClient

Initialize a `CommunicationIdentityClient` with your connection string. The code below retrieves the connection string for the resource from an environment variable named `COMMUNICATION_SERVICES_CONNECTION_STRING`. Learn how to [manage your resource's connection string](../create-communication-resource.md#store-your-connection-string).

Add the following code to the `Main` method:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

Alternatively, you can separate endpoint and access key.
```csharp
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
string endpoint = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ENDPOINT");
string accessKey = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ACCESSKEY");
var client = new CommunicationIdentityClient(new Uri(endpoint), new AzureKeyCredential(accessKey));
```

If you have an Azure Active Directory(AD) application set up, see [Use Service Principals](../identity/service-principal.md), you may also authenticate with AD.
```csharp
TokenCredential tokenCredential = new DefaultAzureCredential();
var client = new CommunicationIdentityClient(new Uri(endpoint), tokenCredential);
```

### Step 3: Exchange the Azure AD user token for the Teams access token

Use the `ExchangeTeamsTokenAsync` method to issue an access token for the Teams user that can be used with the Azure Communicatin Services SDKs.

```csharp
var accessToken = await client.ExchangeTeamsTokenAsync(tokenResult.AccessToken);
Console.WriteLine($"Token:{accessToken.Value.Token}");
```

## Run the code

Run the application from your application directory with the `dotnet run` command.

```console
dotnet run
```
