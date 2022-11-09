---
title: Enable Azure Function in chat app
titleSuffix: An Azure Communication Services tutorial
description: Learn how to enable Azure Function 
author: jiminwen
services: azure-communication-services
ms.author: jiminwen
ms.date: 11/03/2022
ms.topic: tutorial
ms.service: azure-communication-services
---

# Integrate Azure Function
## Introduction
This tutorial provides detailed guidance on how to set up an Azure Function to receive user related information. Setting up an Azure Function is highly recommended. It helps to avoid hard-coding application parameters in the Contoso APP(such as user ID and user token). This information is highly credential. More importantly, we refresh user tokens periodically on the backend. Hard-coded the user ID and token combination requires editing hard-coded value after every refresh.

## Prerequisites

Before you get started, make sure to:

- Create an Azure account with an active subscription. For details, see [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Install Visual Studio Code. 

## Setting up functions
1. Install Azure Function extension in Visual Studio Code. It could be installed within Visual Studio Code application or follow this [Link](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
2. Set up local Azure Function APP following this [Link](../../azure-functions/functions-develop-vs-code.md?tabs=csharp#create-an-azure-functions-project). We need to create a local function using the HTTP trigger template in JavaScript. 
3. Install communication services libraries. We'll use the Identity library to generate User Access Tokens. Run the npm install command in Local Azure Function APP directory, to install the Azure Communication Services Identity SDK for JavaScript.

```
    npm install @azure/communication-identity --save
```
4. Modify the index.js file to code below:
```JavaScript
const { CommunicationIdentityClient } = require('@azure/communication-identity');
const connectionString = '<your_connection_string>'
const acsEndpoint = "<your_ACS_endpoint>"

module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const userIDHolder = await tokenClient.createUser();
    const userId = userIDHolder.communicationUserId

    const userToken = await (await tokenClient.getToken(userIDHolder, ["chat"])).token;

    context.res = {
        body: {
            acsEndpoint,
            userId,
            userToken
        }
    };
}
```
Explanation to code above. The first line import the interface for the CommunicationIdentityClient. The connection string in second line could be found from your ACS resource in Azure portal. The ACSEndpoint is the URL of the ACS resource that has been created. 

5. Open the local Azure Function folder in Visual Studio Code. Open the index.js and run the local Azure Function. A local Azure Function Endpoint will be created and be printed in terminal. The printed message looks similar to:

```
Functions:
HttpTrigger1: [GET,POST] http://localhost:7071/api/HttpTrigger1
```

Open the link in a browser. Example result will be similar to this:
```
{
  "acsEndpoint": "<Azure Function endpoint>",
  "userId": "8:acs:a636364c-c565-435d-9818-95247f8a1471_00000014-f43f-b90f-9f3b-8e3a0d00c5d9",
  "userToken": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjEwNiIsIng1dCI6Im9QMWFxQnlfR3hZU3pSaXhuQ25zdE5PU2p2cyIsInR5cCI6IkpXVCJ9.eyJza3lwZWlkIjoiYWNzOmE2MzYzNjRjLWM1NjUtNDM1ZC05ODE4LTk1MjQ3ZjhhMTQ3MV8wMDAwMDAxNC1mNDNmLWI5MGYtOWYzYi04ZTNhMGQwMGM1ZDkiLCJzY3AiOjE3OTIsImNzaSI6IjE2Njc4NjI3NjIiLCJleHAiOjE2Njc5NDkxNjIsImFjc1Njb3BlIjoiY2hhdCIsInJlc291cmNlSWQiOiJhNjM2MzY0Yy1jNTY1LTQzNWQtOTgxOC05NTI0N2Y4YTE0NzEiLCJyZXNvdXJjZUxvY2F0aW9uIjoidW5pdGVkc3RhdGVzIiwiaWF0IjoxNjY3ODYyNzYyfQ.t-WpaUUmLJaD0V2vgn3M5EKdJUQ_JnR2jnBUZq3J0zMADTnFss6TNHMIQ-Zvsumwy14T1rpw-1FMjR-zz2icxo_mcTEM6hG77gHzEgMR4ClGuE1uRN7O4-326ql5MDixczFeIvIG8s9kAeJQl8N9YjulvRkGS_JZaqMs2T8Mu7qzdIOiXxxlmcl0HeplxLaW59ICF_M4VPgUYFb4PWMRqLXWjKyQ_WhiaDC3FvhpE_Bdb5U1eQXDw793V1_CRyx9jMuOB8Ao7DzqLBQEhgNN3A9jfEvIE3gdwafpBWlQEdw-Uuf2p1_xzvr0Akf3ziWUsVXb9pxNlQQCc19ztl3MIQ"
}
```

6. Deploy the local function to cloud. More details could be found from this [documentation](../../azure-functions/functions-develop-vs-code.md).

7. Test the deployed Azure Function. First find your Azure Function in Azure portal. Then client the Get Function URL button to get the Azure Function endpoint. Hitting the endpoint. The result should be similar to step 5. The Azure Funcion endpoint will be used in APP for initialing application parameters.

8. Implement UserTokenClient, which is used to call target Azure Function resource and obtain ACS endpoint, user ID and user token from returned JSON object. Refer to sample APP for the useage.

## Trouble shooting guide
1. If the Azure Function extension failed to deploy local function to the Azure side, it's likely due to the versions of VS code, and Azure Function extension being used have a bug. This version combination has been tested to work: VS code version '1.68.1' and Azure Function extension version '1.2.1'.
2. The place to initialize application constants is tricky but important. Double check the [chat Android quick-start](https://learn.microsoft.com/azure/communication-services/quickstarts/chat/get-started). The highlight note in section 'Set up application constants'. And compare with the sample APP with the version you are consuming.

## (Optional) secure the Azure Function endpoint
For demonstration purposes, this sample uses a publicly accessible endpoint by default to fetch an Azure Communication Services token. For production scenarios, one option is to use your own secured endpoint to provision your own tokens.

With extra configuration, this sample supports connecting to an Azure Active Directory (Azure AD) protected endpoint so that user log in is required for the app to fetch an Azure Communication Services token. The user will be required to sign in with Microsoft account to access the APP. This set up increases the security level while users are required to log in. Decide whether to enable it based on the use cases.

Note that we currently don't support Azure AD in sample code. Follow links below to enable it in your APP and Azure Function:

[Register your app under Azure Active Directory (using Android platform settings)](../../active-directory/develop/tutorial-v2-android.md).

[Configure your App Service or Azure Functions app to use Azure AD log in](../../app-service/configure-authentication-provider-aad.md).
