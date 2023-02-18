---
title: Language support in Azure Video Indexer
description: This article provides a comprehensive list of language support by service features in Azure Video Indexer.
author: Juliako
manager: femila
ms.topic: conceptual
ms.custom: ignite-2022
ms.author: juliako
ms.date: 02/17/2023
---

# Language support in Azure Video Indexer**

This article explains Video Indexer's language options and provides a list of language support for each one. It includes the languages support for Video Indexer features, translation, language identification, customization, and the language settings of the Video Indexer website.

## Supported languages per scenario

Language support in Video Indexer relates to many areas of the service. The following table indicates when area is supported.

> [!IMPORTANT]
> All of the languages listed support translation when indexing through the API.

### Column explanations

- **Supported source language** – The language spoken in the media file supported for transcription, translation, and search.
- **Language identification** -   Whether the language can be automatically detected by Video Indexer when language identification is used for indexing. To learn more, see [Use Azure Video Indexer to auto identify spoken languages](language-identification-model.md) and the **Language Identification** section.
- **Customization (language model)** - Whether the language can be used when customizing language models in Video Indexer. To learn more, see [Customize a Language model in Azure Video Indexer](customize-language-model-overview.md)
- **Website Translation** – Whether the language is supported for translation when using the [Azure Video Indexer website](https://aka.ms/vi-portal-link). Select the translated language in the language drop-down menu.
    <!-- ![](media/303d21a81909bb008e8d998e43986b50.png) -->
  - The following insights are translated and all other insights appear in English when using translation.
        - Transcript
            - Keywords
            - Topics
            - Labels
            - Frame patterns (Only to Hebrew as of now)

- **Website Language** - Whether the language can be selected for use on the [Azure Video Indexer website](https://aka.ms/vi-portal-link). Select it by navigating to the settings icon at the top right and then the Language settings dropdown.

<!-- ![](media/d4397cc1972298a204e03abbb2f2d92f.png) -->

| **Language**                     | **Code**   | **Supported source language** | **Language identification** | **Customization (language model)** | **Website Translation** | **Website Language** |
|:----------------------------------|:----------:|:-----------------------------:|:---------------------------:|:----------------------------------:|:-----------------------:|:--------------------:|
| Afrikaans                        | af-ZA      |                               |                             |                                    | ✔                      |                      |
| Arabic (Israel)                  | ar-IL      | ✔                            |                             | ✔                                  |                         |                      |
| Arabic (Iraq)                    | ar-IQ      | ✔                            | ✔                           |                                    |                         |                      |
| Arabic (Jordan)                  | ar-JO      | ✔                            | ✔                           | ✔                                 |                         |                      |
| Arabic (Kuwait)                  | ar-KW      | ✔                            | ✔                           | ✔                                 |                         |                      |
| Arabic (Lebanon)                 | ar-LB      | ✔                            |                             | ✔                                  |                         |                      |
| Arabic (Oman)                    | ar-OM      | ✔                            | ✔                           | ✔                                 |                         |                      |
| Arabic (Palestinian Authority)   | ar-PS      | ✔                            |                             | ✔                                  |                         |                      |
| Arabic (Qatar)                   | ar-QA      | ✔                            | ✔                           | ✔                                 |                         |                      |
| Arabic (Saudi Arabia)            | ar-SA      | ✔                            | ✔                           | ✔                                 |                         |                      |
| Arabic (United Arab Emirates)    | ar-AE      | ✔                            | ✔                           | ✔                                 |                         |                      |
| Arabic Egypt                     | ar-EG      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| Arabic Modern Standard (Bahrain) | ar-BH      | ✔                            | ✔                           | ✔                                 |                         |                      |
| Arabic Syrian Arab Republic      | ar-SY      | ✔                            | ✔                           | ✔                                 |                         |                      |
| Armenian                         | hy-AM      | ✔                            |                             |                                    |                         |                      |
| Bangla                           | bn-BD      |                               |                             |                                    | ✔                      |                      |
| Bosnian                          | bs-Latn    |                               |                             |                                    | ✔                      |                      |
| Bulgarian                        | bg-BG      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Catalan                          | ca-ES      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Chinese (Cantonese Traditional)  | zh-HK      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| Chinese (Simplified)             | zh-Hans    | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Chinese (Simplified)             | zh-CK      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Chinese (Traditional)            | zh-Hant    |                               |                             |                                    | ✔                      |                      |
| Croatian                         | hr-HR      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Czech                            | cs-CZ      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Danish                           | da-DK      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| Dutch                            | nl-NL      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| English Australia                | en-AU      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| English United Kingdom           | en-GB      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| English United States            | en-US      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Estonian                         | et-EE      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Fijian                           | en-FJ      |                               |                             |                                    | ✔                      |                      |
| Filipino                         | fil-PH     |                               |                             |                                    | ✔                      |                      |
| Finnish                          | fi-FI      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| French                           | fr-FR      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| French (Canada)                  | fr-CA      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| German                           | de-DE      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Greek                            | el-GR      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Gujarati                         | gu-IN      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Haitian                          | fr-HT      |                               |                             |                                    | ✔                      |                      |
| Hebrew                           | he-IL      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| Hindi                            | hi-IN      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Hungarian                        | hu-HU      |                               | ✔                          |                                    | ✔                       | ✔                   |
| Icelandic                        | is-IS      | ✔                            |                             |                                    |                         |                      |
| Indonesian                       | id-ID      |                               |                             |                                    | ✔                      |                      |
| Irish                            | ga-IE      | ✔                            | ✔                           |                                    |                         |                      |
| Italian                          | it-IT      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Japanese                         | ja-JP      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Kannada                          | kn-IN      | ✔                            | ✔                           |                                    |                         |                      |
| Kiswahili                        | sw-KE      |                               |                             |                                    | ✔                      |                      |
| Korean                           | ko-KR      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Latvian                          | lv-LV      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Lithuanian                       | lt-LT      |                               |                             |                                    | ✔                      |                      |
| Malagasy                         | mg-MG      |                               |                             |                                    | ✔                      |                      |
| Malay                            | ms-MY      | ✔                            |                             |                                    | ✔                       |                      |
| Malayalam                        | ml-IN      | ✔                            | ✔                           |                                    |                         |                      |
| Maltese                          | mt-MT      |                               |                             |                                    | ✔                      |                      |
| Norwegian                        | nb-NO      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| Persian                          | fa-IR      | ✔                            |                             | ✔                                  | ✔                      |                      |
| Polish                           | pl-PL      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Portuguese                       | pt-BR      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Portuguese (Portugal)            | pt-PT      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| Romanian                         | ro-RO      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Russian                          | ru-RU      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Samoan                           | en-WS      |                               |                             |                                    |                         |                      |
| Serbian (Cyrillic)               | sr-Cyrl-RS |                               |                             |                                    | ✔                      |                      |
| Serbian (Latin)                  | sr-Latn-RS |                               |                             |                                    | ✔                      |                      |
| Slovak                           | sk-SK      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Slovenian                        | sl-SI      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Spanish                          | es-ES      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Spanish (Mexico)                 | es-MX      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| Swedish                          | sv-SE      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Tamil                            | ta-IN      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Telugu                           | te-IN      | ✔                            | ✔                           |                                    |                         |                      |
| Thai                             | th-TH      | ✔                            | ✔                           | ✔                                 | ✔                       |                      |
| Tongan                           | to-TO      |                               |                             |                                    | ✔                      |                      |
| Turkish                          | tr-TR      | ✔                            | ✔                           | ✔                                 | ✔                       | ✔                   |
| Ukrainian                        | uk-UA      | ✔                            | ✔                           |                                    | ✔                      |                      |
| Urdu                             | ur-PK      |                               |                             |                                    | ✔                      |                      |
| Vietnamese                       | vi-VN      | ✔                            | ✔                           |                                    | ✔                      |                      |

## Get supported languages through the API

You can call our API with the Get Supported Languages API call to pull a full list of supported languages per area - see [Get Supported Languages](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Supported-Languages).

The API returns a list of supported languages with the following values:

```json
{
    "name": "Language",
    "languageCode": "Code",
    "isRightToLeft": true/false,
    "isSourceLanguage": true/false,
    "isAutoDetect": true/false
}
```

- Supported source language:
    If `isSourceLanguage` is false, the language is supported for translation only.
    If `isSourceLanguage` is true, the language is supported as source for transcription, translation, and search.

- Language identification (auto detection):

    If `isAutoDetect` is true, the language is supported for language identification (LID) and multi-language identification (MLID).

## Language Identification

When uploading a media file to Video Indexer, if you're aware of its spoken language, you can specify the language in advance. On the website, you can select a language during the upload experience, and when indexing through the API, by using the language parameter when submitting the indexing job. The selected language is used to generate the transcription of the file (assuming the file has an audio channel).

If you aren't sure of the source language of the media file or if it may contain multiple languages, you can select either Auto-detect single language (LID) or multi-language (MLID) for the media file’s source language.

There is a limit of 10 languages allowed for identification during the indexing of a media file for both LID and MLID.languages. The following are the 9 **default** languages of Language identification (LID) and Multi-language identification (MILD):

- German (de-DE)
- English United States (en-US)
- Spanish (es-ES)
- French (fr-FR)
- Italian (it-IT)
- Japanese (ja-JP)
- Portuguese (pt-BR)
- Russian (ru-RU)
- Chinese (Simplified) (zh-Hans)

## How to change the list of default languages

If you need to use languages for identification that aren't used by default, you can customize the list of identification languages to any 10 languages that support customization through either the website or the API.

### Use the website to change the list

1. Select the **Language ID** tab under Model customization. The list of languages is specific to the Video Indexer account you're using and for the signed-in user. The default list of languages is saved per user on their local device, per device, and browser. As a result, each user can configure their own default identified language list.
1. Use **Add language** to search and add more languages. If 10 languages are already selected, you first must remove one of the existing detected languages before adding a new one.

<!--![Graphical user interface, text, application Description automatically generated](media/9903104004b75cbe8c5703631d779e1a.png)-->

### Use the API to change the list

When you upload a file, the Video Indexer language model cross references 9 languages by default. If there's a match, the model generates the transcription for the file with the detected language. Use the language parameter to specify `multi` (MLID) or `auto` (LID) parameters. Use the `customLanguages` parameter to specify up to 10 languages. (The parameter is used only when the language parameter is set to `multi` or `auto`.) To learn more about using the API, see [Use the Azure Video Indexer API](video-indexer-use-apis.md).

## Next steps

- [Overview](video-indexer-overview.md)
- [Release notes](release-notes.md)
