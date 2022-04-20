---
title: Enable file sharing using UI Library and Azure Blob Storage
titleSuffix: An Azure Communication Services tutorial
description: Learn how to use Azure Communication Services with the UI Library to enable file sharing through chat leveraging Azure Blob Storage.
author: anjulgarg
manager: alkwa
services: azure-communication-services

ms.author: anjulgarg
ms.date: 04/04/2022
ms.topic: tutorial
ms.service: azure-communication-services
ms.subservice: chat
---

# Enable file sharing using UI Library and Azure Blob Storage

In this tutorial, we'll be configuring the Azure Communication Services UI Library Chat Composite to enable file sharing. The UI Library Chat Composite provides a set of rich components and UI controls that can be used to enable file sharing. We will be leveraging Azure Blob Storage to enable the storage of the files that are shared through the chat thread.

>[!IMPORTANT]
>Azure Communication Services doesn't provide a file storage service. You will need to use your own file storage service for sharing files. For the pupose of this tutorial, we will be using Azure Blob Storage.**

## Prerequisites

- An Azure account with an active subscription. For details, see [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio Code](https://code.visualstudio.com/) on one of the [supported platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
- [Node.js](https://nodejs.org/), Active LTS and Maintenance LTS versions (10.14.1 recommended). Use the `node --version` command to check your version.
- An active Communication Services resource and connection string. [Create a Communication Services resource](../quickstarts/create-communication-resource.md).

This tutorial assumes that you already know how to setup and run a Chat Composite. You can follow the [Chat Composite tutorial](https://azure.github.io/communication-ui-library/?path=/docs/quickstarts-composites--page) to learn how to set up and run a Chat Composite.

## Overview

The UI Library Chat Composite supports file sharing by enabling developers to pass the URL to a hosted file that is sent through the Azure Communication Services chat service. The UI Library renders the attached file and supports multiple extensions to configure the look and feel of the file sent. More specifically, it supports the following features:

1. Attach file button for picking files through the OS File Picker
2. Configure allowed file extensions. 
3. Enable/disable multiple uploads.
4. File Icons for a wide variety of file types.
5. File upload/download cards with progress indicators.
6. Ability to dynamically validate each file upload and display errors on the UI.
7. Ability to cancel an upload and remove an uploaded file before it is sent.
8. View uploaded files in MessageThread, download them. Allows asynchronous downloads.

The diagram below shows a typical flow of a file sharing scenario for both upload and download. The section marked as `Client Managed` shows the building blocks that need to be implemented by developers.

![Filesharing typical flow](./media/filesharing-typical-flow.png "Filesharing typical flow")

## Setup File Storage using Azure Blob

You can follow the tutorial [Upload file to Azure Blob Storage with an Azure Function](https://docs.microsoft.com/azure/developer/javascript/how-to/with-web-app/azure-function-file-upload) to write the backend code required for file sharing.

Once implemented, you can call this Azure Function inside the `uploadHandler` function to upload files to Azure Blob Storage. For the remaining of the tutorial, we will assume you have generated the function using the tutorial for Azure Blob Storage linked above.

## Configuring Chat Composite to Enable File Sharing

You will need to replace the variable values for both common variable required to initialize the chat composite.

```tsx
import { FileUploadHandler, FileUploadManager } from '@azure/communication-react';
import { initializeFileTypeIcons } from '@fluentui/react-file-type-icons'; 

initializeFileTypeIcons();

function App(): JSX.Element {
  // Common variables
  const endpointUrl = 'INSERT_ENDPOINT_URL';
  const userId = ' INSERT_USER_ID';
  const displayName = 'INSERT_DISPLAY_NAME';
  const token = 'INSERT_ACCESS_TOKEN';
  const threadId = 'INSERT_THREAD_ID';

  const [chatAdapter, setChatAdapter] = useState<ChatAdapter>();

  // We can't even initialize the Chat and Call adapters without a well-formed token.
  const credential = useMemo(() => {
    try {
      return new AzureCommunicationTokenCredential(token);
    } catch {
      console.error('Failed to construct token credential');
      return undefined;
    }
  }, [token]);

  useEffect(() => {
    const createAdapter = async (): Promise<void> => {
      setChatAdapter(
        await createAzureCommunicationChatAdapter({
          endpoint: endpointUrl,
          userId: { communicationUserId: userId },
          displayName,
          credential: new AzureCommunicationTokenCredential(token),
          threadId
        })
      );
    };
    createAdapter();
  }, []);

  if (!!chatAdapter) {
    return (
      <>
        <div style={containerStyle}>
          <ChatComposite
            adapter={chatAdapter}
            options={{
              fileSharing: {
                uploadHandler: fileUploadHandler,
                // If `fileDownloadHandler` is not provided. The file URL is opened in a new tab.
                downloadHandler: fileDownloadHandler,
                accept: 'image/png, image/jpeg, text/plain, .docx',
                multiple: true
              }
            }} />
        </div>
      </>
    );
  }
  if (credential === undefined) {
    return <h3>Failed to construct credential. Provided token is malformed.</h3>;
  }
  return <h3>Initializing...</h3>;
}

const fileUploadHandler: FileUploadHandler = async (userId, fileUploads) => {
  for (const fileUpload of fileUploads) {
    try {
      const { name, url, extension } = await uploadFileToAzureBlob(fileUpload);
      fileUpload.notifyUploadCompleted({ name, extension, url });
    } catch (error) {
      if (error instanceof Error) {
        fileUpload.notifyUploadFailed(error.message);
      }
    }
  }
}

const uploadFileToAzureBlob = async (fileUpload: FileUploadManager) => {
  // You need to handle the file upload here and upload it to Azure Blob Storage.
  // Below you can find snippets for how to configure the upload
  // Optionally, you can also update the file upload progress.
  fileUpload.notifyUploadProgressChanged(0.2);
  return {
    name: 'SampleFile.jpg', // File name displayed during download
    url: 'https://sample.com/sample.jpg', // Download URL of the file.
    extension: 'jpeg' // File extension used for file icon during download.
  };
  
const fileDownloadHandler: FileDownloadHandler = async (userId, fileData) => {
      return new URL(fileData.url);
    }
  };
}

```

## Configure upload method to use Azure Blob Storage

To enable Azure Blob Storage upload, we will modify the `uploadFileToAzureBlob` method we declared above with the following code. You will need to replace the Azure Function information below to enable to upload.

```javascript

const uploadFileToAzureBlob = async (fileUpload: FileUploadManager) => {
  // You need to handle the file upload here and upload it to Azure Blob Storage.
  // Optionally, you can update the file upload progress.
  fileUpload.notifyUploadProgressChanged(0.2);

  const file = fileUpload.file;
  if (!file) {
    throw new Error('fileUpload.file is undefined');
  }

  const filename = file.name;

  // Following is an example of calling an Azure Function to handle file upload
  // The https://docs.microsoft.com/en-us/azure/developer/javascript/how-to/with-web-app/azure-function-file-upload
  // tutorial uses 'username' parameter to specify the storage container name.
  // Note that the container in the tutorial is private by default. To get default downloads working in
  // this sample, you need to change the container's access level to Public via Azure Portal.
  const username = 'ui-library';
  
  // You can get function url from the Azure Portal:
  const azFunctionBaseUri='<YOUR_AZURE_FUNCTION_URL>';
  const uri = `${azFunctionBaseUri}&username=${username}&filename=${filename}`;
  
  const formData = new FormData();
  formData.append(file.name, file);
  const response = await fetch(uri, {
    method: 'POST',
    body: formData
  });

  if (!response.ok) {
    throw new Error(`Failed to upload file, status code:${response.status}`);
  }

  const storageBaseUrl = 'https://<YOUR_STORAGE_ACCOUNT>.blob.core.windows.net';

  return {
    name: filename,
    url: `${storageBaseUrl}/${username}/${filename}`,
    extension: getFileExtension(filename)
  };
}

```

## Error Handling
    
When an upload fails, the UI Library Chat Composite will display an error message. 

![File Upload Error Bar](./media/file-too-big.png "File Upload Error Bar")

Here is sample code showcasing how you can fail an upload due to a size validation error by changing the `fileUploadHandler` above.

```tsx
import { FileUploadHandler } from from '@azure/communication-react';

const fileUploadHandler: FileUploadHandler = async (userId, fileUploads) => {
  for (const fileUpload of fileUploads) {
    if (fileUpload.file && fileUpload.file.size > 99 * 1024 * 1024) {
      // Notify ChatComposite about upload failure. 
      // Allows you to provide a custom error message.
      fileUpload.notifyUploadFailed('File too big. Select a file under 99 MB.');
    }
  }
}
```

## File Downloads - Advanced Usage

By default, the file `url` provided through `notifyUploadCompleted` method  will be used to trigger a file download. However, if you need to handle a download in a different way, you can provide a custom `downloadHandler` to ChatComposite. Below we will modify the `fileDownloadHandler` that we declared above to check for an authorized user before allowing to download the file.

```tsx
import { FileDownloadHandler } from "communication-react";

const isUnauthorizedUser = (userId: string): boolean => {
  // You need to write your own logic here for this example.
}

const fileDownloadHandler: FileDownloadHandler = async (userId, fileData) => {
  if (isUnauthorizedUser(userId)) {
    // Error message will be displayed to the user.
    return { errorMessage: 'You don’t have permission to download this file.' };
  } else {
    // If this function returns a Promise that resolves a URL string, 
    // the URL is opened in a new tab. 
    return new URL(fileData.url);
  }
}

)
```

Download errors will be displayed to users in an error bar on top of the Chat Composite.

![File Download Error](./media/download-error.png "File Download Error")


## Clean up resources

If you want to clean up and remove a Communication Services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it. You can find out more about [cleaning up Azure Communication Service resources](../quickstarts/create-communication-resource.md#clean-up-resources) and [cleaning Azure Function Resources](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources).

## Next steps

> [!div class="nextstepaction"]
> [Check the rest of the UI Library](https://azure.github.io/communication-ui-library/)

You may also want to:

- [Add chat to your app](../quickstarts/chat/get-started.md)
- [Creating user access tokens](../quickstarts/access-tokens.md)
- [Learn about client and server architecture](../concepts/client-and-server-architecture.md)
- [Learn about authentication](../concepts/authentication.md)
