---
title: Model customization concepts - Image Analysis 4.0
titleSuffix: Azure Cognitive Services
description: Concepts related to the custom model feature of the Image Analysis 4.0 API.
services: cognitive-services
author: PatrickFarley
manager: nitinme

ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/06/2023
ms.author: pafarley
---

# Model customization

Model customization lets you train a specialized Image Analysis model for your own use case. Custom models can do either image classification (tags apply to the whole image) or object detection (tags apply to specific areas of the image). Once your custom model is created and trained, it belongs to your Computer Vision resource and you can call it using the [Analyze Image API](./how-to/call-analyze-image-40.md).

## Scenario components

The main components of a model customization system are the training images, association file, dataset object, and model object.

### Training images

Your set of training images should include several examples of each of the labels you want to detect. The images need to be stored in an Azure Storage container in order to be accessible to the model.

### Association file

The association file references all of the training images and provides their labeling information. In the case of object detection, it specified the bounding box coordinates of each tag on each image. This file must be in the COCO format, which is a specific type of JSON file. The association file should be stored in the same Azure Storage container as the training images.

> [!TIP]
> [!INCLUDE [coco-files](includes/coco-files.md)]


### Dataset object

The **Dataset** object is a data structure stored by the Image Analysis service that references the association file. You need to create a **Dataset** object before you can create and train a model.

### Model object

The **Model** object is a data structure stored by the Image Analysis service that represents a custom model. It must be associated with a **Dataset** in order to do initial training. Once it's trained, you can query your model by entering its name in the `model-version` query parameter of the [Analyze Image API call](./how-to/call-analyze-image-40.md).

## Frequently asked questions

### Why does training take longer/shorter than my specified budget?

The specified training budget is the calibrated **compute time**, not the **wall-clock time**. Some common reasons for the difference are listed:

- **Longer than specified budget:**
   - Image Analysis experiences a high training traffic, and GPU resources may be tight. Your job may wait in the queue or be put on hold during training.
   - The backend training process ran into unexpected failures, which resulted in retrying logic. The failed runs don't consume your budget, but this can lead to longer training time in general.
- **Shorter than specified budget:** The following factors speed up training at the cost of using more budget in certain wall-clock time.
   - Image Analysis sometimes trains with multiple GPUs depending on your data. 
   - Image Analysis sometimes trains multiple exploration trials on multiple GPUs at the same time.
   - Image Analysis sometimes uses premier (faster) GPU SKUs to train.

### Why does my training fail and what I should do?

The following are some common reasons for training failure:

- `Training diverged`: The training can't learn meaningful things from your data. Some common causes are:
   - Data is not enough: providing more data should help.
   - Data is of poor quality: check if your images are of low resolution, extreme aspect ratios, or if annotations are wrong.
- `Budget not enough`: Your specified budget isn't enough for the size of your dataset and model type you're training. Specify a larger budget.
- `Dataset corrupt`: Usually this means your provided images aren't accessible or the annotation file is in the wrong format.
- `Unknown`: This could be a backend issue. Reach out to support for investigation.

### Can I control the hyper-parameters or use my own models in training?

No, Image Analysis model customization service uses a low-code AutoML training system that handles hyper-param search and base model selection in the backend.

### Can I export my model after training?

Currently, the prediction API is only supported through the cloud service.

### What metrics are used for evaluating the models?

See the [Vision Evaluation repository](https://github.com/microsoft/vision-evaluation/blob/main/README.md) for the list of metrics we use for model evaluation.

### How many images are required for reasonable/good/best model quality?

Although Florence models have great few-shot capability (achieving great model performance under limited data availability), in general more data makes your trained model better and more robust. Some scenarios require little data (like classifying an apple against a banana), but others require more (like detecting 200 kinds of insects in a rainforest). This makes it difficult to give a single recommendation.

If your data labeling budget is constrained, our recommended workflow is to repeat the following steps:

1. Collect $N$ images per class, where $N$ images are easy for you to collect (for example, $N=3$)
1. Train a model and test it on your evaluation set.
1. If the model performance is-
   -  **Good enough** (performance is better than expected or close to your previous experiments with less data collected): Stop here and use this model.
   - **Not good enough** (performance is still below expectation or better than your previous experiment with less data collected at a reasonable margin): Collect $N+\delta$ images, and go back to Step 2.

### How much training budget should I specify?

You should specify the upper limit of budget that you're willing to consume. Image Analysis uses an AutoML system in its backend to try out different models and training recipes to find the best model for your use case. The more budget that's given, the higher the chance of finding a better model.

The AutoML system also stops automatically if it concludes there's no need to try more, even if there is still remaining budget. So, it doesn't always exhaust your specified budget. You're guaranteed not to be billed over your specified budget.

## Next steps

* [Get started creating and training a custom model](./how-to/model-customization.md)
