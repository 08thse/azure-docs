---
title: "Quickstart: Image Analysis 4.0 client library for .NET"
description: In this quickstart, get started with the Image Analysis 4.0 client library for .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.custom: ignite-2022
ms.topic: include
ms.date: 01/24/2023
ms.author: pafarley
---
 
<a name="HOLTop"></a>

Use the Image Analysis client library for C++ to analyze an image and generate captions. This quickstart defines a method, `AnalyzeImage`, which uses the client object to analyze a remote image and print the results. 

[Reference documentation](tbd) | [Library source code](tbd) | Packages (NuGet): [Core](https://www.nuget.org/packages/Azure.AI.Vision.Core/0.8.1-beta.1) [ImageAnalysis](https://www.nuget.org/packages/Azure.AI.Vision.ImageAnalysis/0.8.1-beta.1) | [Samples](tbd)

> [!TIP]
> You can also analyze a local image. See the [ComputerVisionClient](tbd) methods, such as **tbd**. Or, see the sample code on [GitHub](https://github.com/Azure-Samples/azure-ai-vision-sdk/blob/main/samples/cpp/image-analysis/samples.cpp) for scenarios involving local images.

> [!TIP]
> The Analyze API can do many different operations other than generate captions. See the [Image Analysis how-to guide](../../how-to/call-analyze-image-40.md) for examples that showcase all of the available features.

## Prerequisites

* An Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services/)
* The [Visual Studio IDE](https://visualstudio.microsoft.com/vs/).
* Once you have your Azure subscription, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Create a Computer Vision resource"  target="_blank">create a Computer Vision resource</a> in the Azure portal. You must create it in one of the following Azure regions: East US, France Central, Korea Central, North Europe, Southeast Asia, West Europe, West US. After it deploys, click **Go to resource**.
    * You will need the key and endpoint from the resource you create to connect your application to the Computer Vision service. You'll paste your key and endpoint into the code below later in the quickstart.
    * You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=CPP&Pillar=Vision&Product=Image-analysis&Page=quickstart&Section=Prerequisites" target="_target">I ran into an issue</a>

## Analyze image

1. Create a new C++ application.

    In Visual Studio, open the **File** menu and choose **New** -> **Project** to open the **Create a new Project** dialog. Select the **Console App** template that has C++, Windows, and Console tags, and then choose **Next**.

    In the **Configure your new project** dialog, enter _ImageAnalysisQuickstart_ in the **Project name** edit box. Choose **Create** to create the project.

    ### Install the client library 

    Once you've created a new project, install the client library by right-clicking on the project solution in the **Solution Explorer** and selecting **Manage NuGet Packages**. In the package manager that opens select **Browse**, check **Include prerelease**, and search for `Azure.AI.Vision.Core` and `Azure.AI.Vision.ImageAnalysis`. Select **Install** for each of them. 

1. Find the key and endpoint.

    [!INCLUDE [find key and endpoint](../find-key.md)]

1. From the project directory, open the _ImageAnalysisQuickstart.cpp_ file in your preferred editor or IDE. Clear its contents and paste in the following code:

   [!code-cpp[](~/cognitive-services-quickstart-code/cpp/ComputerVision/ImageAnalysisQuickstart-single-4-0.cpp?name=snippet_single)]

1. Paste your key and endpoint into the code where indicated. Your Computer Vision endpoint has the form `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`.

   > [!IMPORTANT]
   > Remember to remove the key from your code when you're done, and never post it publicly. For production, use a secure way of storing and accessing your credentials like [Azure Key Vault](../../../../key-vault/general/overview.md). See the Cognitive Services [security](../../../cognitive-services-security.md) article for more information.

1. Run the application

   Run the application by clicking the **Debug** button at the top of the IDE window.

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=CPP&Pillar=Vision&Product=Image-analysis&Page=quickstart&Section=Analyze-image" target="_target">I ran into an issue</a>

## Output

```console
tbd
```

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=CPP&Pillar=Vision&Product=Image-analysis&Page=quickstart&Section=Output" target="_target">I ran into an issue</a>

## Clean up resources

If you want to clean up and remove a Cognitive Services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## Next steps

In this quickstart, you learned how to install the Image Analysis client library and make basic image analysis calls. Next, learn more about the Analyze API features.


> [!div class="nextstepaction"]
>[Call the Analyze API](../../how-to/call-analyze-image-40.md)

* [Image Analysis overview](../../overview-image-analysis.md)
* The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/azure-ai-vision-sdk/blob/main/samples/cpp/image-analysis/samples.cpp).
