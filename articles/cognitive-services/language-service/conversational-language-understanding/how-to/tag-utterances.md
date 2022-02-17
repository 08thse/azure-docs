---
title: How to tag utterances in Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Use this article to tag your utterances in Conversational Language Understanding projects
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
---

# How to tag utterances

Once you have [built a schema](build-schema.md) for your project, you should add training utterances to your project. The utterances should be similar to what your users will use when interacting with the project. When you add an utterance you have to assign which intent it belongs to. After the utterance is added, label the words within your utterance with the entities in your project. Your labels for entities should be consistent across the different utterances.

Tagging is the process of assigning your utterances to intents, and labeling them with entities. You will want to spend time tagging your utterances - introducing and refining the data that will train the underlying machine learning models for your project. The machine learning models generalize based on the examples you provide it. The more examples you provide, the more data points the model has to make better generalizations.

> [!NOTE]
>  An entity's learned components is only defined when you label utterances for that entity. You can also have entities that include _only_ list or prebuilt components without labelling learned components. see the [entity components](../concepts/entity-components.md) article for more information.

When you enable multiple languages in your project, you must also specify the language of the utterance you are adding. As part of the multilingual capabilities of Conversational Language Understanding, you can train your project in a dominant language, and then predict in the other available languages. Adding examples to other languages increases the model's performance in these languages if you determine it isn't doing well, but avoid duplicating your data across all the languages you would like to support. 

For example, to improve a calender bot's performance with users, a developer might add examples mostly in English, and a few in Spanish or French as well. They might add utterances such as:

* "_Set a meeting with **Matt** and **Kevin** **tomorrow** at **12 PM**._" (English)
* "_Reply as **tentative** to the **weekly update** meeting._" (English)
* "_Cancelar mi **próxima** reunión_." (Spanish)

In Orchestration Workflow projects, the data used to train connected intents isn't provided within the project. Instead, the project pulls the data from the connected service (such as connected LUIS applications, Conversational Language Understanding projects, or Custom Question Answering knowledge bases) during training. However, if you create intents that are not connected to any service, you still need to add utterances to those intents.

For example, a developer might create an intent for each of their skills, and connect it to a respective calendar project, email project, and company FAQ knowledge base. 

## Tag utterances

:::image type="content" source="../media/tag-utterances.png" alt-text="A screenshot of the page for tagging utterances in Language Studio." lightbox="../media/tag-utterances.png":::

*Section 1* is where you choose which dataset you are viewing. You can add utterances in Train dataset or Test dataset.

Your utterances are split into two sets:

* Train dataset is the main set used to train the model. The contents of this set are introduced to the model during training for it learn from your tagged data.
* Test dataset is used to provide an unbiased evaluation of the model. This set is not introduced to the model during training and used for testing the model and evaluating its performance.

When adding utterances, you have the option to add to a specific set explicitly (Train or test). If you choose to do this you need to set your split type in Train model page to “User defined”, otherwise keep all your utterances in the Train set and use “Random data split” splitting. Learn more about how your data is split before training [here]()

*Section 2* is where you add your utterances. You must select one of the intents from the drop-down list, the language of the utterance (if applicable) and the utterance itself. Press the Enter key in the utterance's text box to add the utterance.

*Section 3* includes your project's entities and distribution of intents and entities across your Training set and Test set. 

You can select any of the entities you've added, and then hover over the text to label the entities within your utterances, shown in section 4. You can also add new entities here by pressing the + Add Entity button. You can also hide those entity's labels within your utterances.

For distribution section, you can View tag distribution across:
* Entities in tags: shows distribution if each of your entities across the train and test sets. 
* Entities in utterances:  shows how your entities are distributed among the different utterances you have.
* Intents: shows the distribution of intents across your train and test sets.

:::image type="content" source="../media/label-distribution.png" alt-text="Distribution" lightbox="../media/label-distribution.png":::

*Section 4* includes the utterances you've added. You can drag over the text you want to label, and a contextual menu of the entities will appear.

:::image type="content" source="../media/label-utterance-menu.png" alt-text="An example of selecting text to add a label." lightbox="../media/label-utterance-menu.png":::

> [!NOTE]
> Unlike LUIS, you cannot label overlapping entities. The same characters cannot be labeled by more than one entity.

## Filter Utterances

Clicking on **Filter** lets you view only the utterances associated to the intents and entities you select in the filter pane.
When clicking on an intent in the [build schema](./build-schema.md) page then you'll be moved to the **Tag Utterances** page, with that intent filtered automatically. 

## Next Steps
* [Train and Evaluate Model](./train-model.md)
