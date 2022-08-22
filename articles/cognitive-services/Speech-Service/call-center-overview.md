---
title: Speech in Call Center Overview
titleSuffix: Azure Cognitive Services
description: A common scenario for speech-to-text is transcribing large volumes of telephony data that come from various systems, such as interactive voice response (IVR) in batch and real-time. By using Speech services a business can get high-quality audio/voice integration to support these scenarios.
services: cognitive-services
author: goergenj
ms.author: jagoerge
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/10/2022
ms.custom: template-concept
---

# Call Center Overview


Azure Cognitive Services enables customers to realize partial and full automation of telephony-based customer interactions as well as provide additional accessibility capabilities on all channels. Further they support the analysis of calls in real-time and post-call using Language services. You can extract and redact Personally Identifying Information (PII), summarize the transcription and detect the sentiment as well.

The main business scenarios for the application of Microsoft Speech Services in AI-driven Call and Contact Centers are:
- Virtual Agents: Conversational AI based telephony-integrated VoiceBots and voice-enabled ChatBots
- Agent-Assist: Real-Time transcription and analysis of a call to improve the customer experience by providing additional insights and suggest actions to Agents
- Post-Call Analytics: Post-Call analysis to create insights into customer conversations to improve understanding and support continuous improvement of call handling, optimization of quality assurance and compliance control as well as other insights driven optimizations

> [!TIP]
> Check-Out the [Post-Call Analytics quickstart]() ***COMMENT: LINK TO BE ADDED*** to learn how to quickly build and deploy a solution.

## Speech and Language technologies for the Call Center

Telephony data typically used in Call Centers is generated through landlines, mobile phones, and radios is ordinarily of low quality. This data is often narrowband, in the range of 8 KHz, which can create challenges when you're converting speech to text. Our speech to text models are trained to ensure customers can get high-quality transcriptions whatever systems they use to capture audio.

On top of it’s best-in-market speech recognition models Azure Cognitive Services provides key building blocks to incorporate AI in Call and Contact Center use cases.

Once you have transcribed your audio, you can use the Language Service to perform post-transcription analytics on your call center data such as: sentiment analysis, summarizing the reason for customer calls, how they were resolved, extracting and redacting PII, and more.

A holistic implementation typically incorporates technologies from the Speech and Language Cognitive Services. 

The Speech Service offers the following features:
- [Real-time speech-to-text](./how-to-recognize-speech.md): Allow real-time recognition of audio from multiple inputs
    - [Continuous recognition](./how-to-recognize-speech.md#use-continuous-recognition): Mostly used with real-time Call Center scenarios (Virtual Agents, Agent Assist) allows you to continuously recognize audio input and control how to process results based on multiple events
- [Batch speech-to-text](./batch-transcription.md): Enables you to transcribe large amounts of audio files asynchronously including speaker diarization and is typically used in Post Call Analytics scenarios
- [Text-to-speech](./index-text-to-speech): Text-to-speech enables your applications, tools, or devices to convert text into humanlike synthesized speech
- [Speaker identification](./speaker-recognition-overview): Helps you determine an unknown speaker’s identity within a group of enrolled speakers and is typically used for Call Center customer verification scenarios or fraud detection
- [Language Identification](./language-identification.md): Is used to identify languages spoken in audio and can be used in real-time and post call analysis to control the environment (e.g. output language of a Virtual Agent) or to gain additional insights

And the Language Service offers the following:
- [Personally Identifiable Information (PII) extraction](/azure/cognitive-services/language-service/personally-identifiable-information/overview): Identify, categorize, and redact sensitive information in unstructured text.
- [Conversation summarization](/azure/cognitive-services/language-service/summarization/overview?tabs=conversation-summarization): Provides a summary of what each conversation participant said about the predefined issues and resolutions.
- [Sentiment analysis and opinion mining](/azure/cognitive-services/language-service/sentiment-opinion-mining/overview): Analyze and associate positive, neutral, or negative sentiment at the transcription sentence and document-level.

## Customization
### Speech Customization

The Speech service works well with built-in models. However, you might want to further customize and tune the experience for your product or environment. Customization options range from acoustic model tuning to unique voice fonts for your brand. After you've built a custom model, you can use it with any of the Speech service features in real-time or batch mode.

Typical examples for customization are:
- Optimizing for business or use-case specific language (e.g. customer product names, industry terminology, etc.).
- Optimizing for improved transcription of use- case specific entities (e.g. alpha-numeric customer/case/contract IDs, license plates, names, etc.)

| Speech feature | Model | Description |
| -------------- | ----- | ----------- |
| Speech-to-text | [Custom Speech overview](./custom-speech-overview.md) | With Custom Speech, you can evaluate and improve the Microsoft speech-to-text accuracy for your applications and products. |
| Text-to-speech | [Custom Neural Voice overview](./custom-neural-voice.md) | Custom Neural Voice is a text-to-speech feature that lets you create a one-of-a-kind, customized, synthetic voice for your applications. |

### Language Customization
The Language service works well with built-in models. However you might want to further customize and tune models to extract more information from your data.

You can find an overview of all Language Service features and customization options [here](/azure/cognitive-services/language-service/overview#available-features).

## Next steps

***Comment: Add correct links!***
- [Check out Speech Portal](https://speech.microsoft.com)
- [Check out the Post-Call Analytics Quickstart](/azure/cognitive-services/speech-service/call-center-quickstart?branch=pr-en-us-206946&pivots=programming-language-csharp)
- [Explore Custom Speech ](./custom-speech-overview.md)
- [Explore Custom Neural Voice](./custom-neural-voice.md)
- [Explore Lanugage Service Features](/azure/cognitive-services/language-service/overview#available-features).

<!--
Remove all the comments in this template before you sign-off or merge to the 
main branch.
-->
