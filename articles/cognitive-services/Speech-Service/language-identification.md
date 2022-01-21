---
title: Language identification - Speech service
titleSuffix: Azure Cognitive Services
description: Language identification is used to determine the language being spoken in audio passed to the Speech SDK when compared against a list of provided languages.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/09/2022
ms.author: eur
zone_pivot_groups: programming-languages-speech-services-nomore-variant
---

# Language identification

Language identification is used to identify natural languages spoken in audio when compared against a list of [supported languages](language-support.md). 

Language identification use cases include:

* [Standalone language identification](#standalone-language-identification) when you only need to detect the natural language in an audio source.
* [Speech-to-text language identification](#speech-to-text) when you need to detect the natural language in an audio source and then transcribe it to text. 
* [Speech translation language identification](#speech-translation) when you need to detect the natural language in an audio source and then translate it to another language. 

## Setup configurations

Whether you use language identification [on its own](#standalone-language-identification), with [speech-to-text](#speech-to-text), or with [speech translation](#speech-translation), there are some common concepts and configuration options. 

- Define a list of [candidate languages](#candidate-languages) that you expect in the audio.
- Decide whether to use [at-start or continuous](#at-start-and-continuous-language-identification) language identification.
- Prioritize [low latency or high accuracy](#accuracy-and-latency-prioritization) of results.

Then you make a recognize once or continuous recognition request to the Speech service. 

Code snippets are included with the concepts described next. Complete samples for each use case are provided further below.

### Candidate languages

You provide candidate languages, at least one of which is expected be in the audio. You can include up to 4 languages for [at-start LID](#at-start-and-continuous-language-identification) or up to 10 languages for [continuous LID](#at-start-and-continuous-language-identification).  

> [!NOTE]
> You must provide the full 4-letter locale, but the Speech service only uses the base language. For example, if you include both "en-US" and "en-GB", the Speech service only uses the Speech-to-text model for English. Do not include multiple locales for the same language. 

::: zone pivot="programming-language-csharp"
```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "ja-JP", "zh-CN" });
```
::: zone-end
::: zone pivot="programming-language-cpp"
```cpp
auto autoDetectSourceLanguageConfig = 
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "ja-JP", "zh-CN" });
```
::: zone-end
::: zone pivot="programming-language-python"
```python
auto_detect_source_language_config = \
    speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "ja-JP", "zh-CN"])
```
::: zone-end
::: zone pivot="programming-language-java"
```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "ja-JP", "zh-CN"));
```
::: zone-end
::: zone pivot="programming-language-javascript"
```javascript
var autoDetectSourceLanguageConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromLanguages([("en-US", "ja-JP", "zh-CN"]);
```
::: zone-end
::: zone pivot="programming-language-objectivec"
```objective-c
NSArray *languages = @[@"en-US", @"ja-JP", @"zh-CN"];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
    [[SPXAutoDetectSourceLanguageConfiguration alloc]init:languages];
```
::: zone-end


### At-start and Continuous language identification

Speech supports both at-start and continuous language identification (LID). 

> [!NOTE]
> Continuous language identification is only supported with Speech SDKs in C#, C++, and Python.

- At-start LID identifies the language within the first few seconds of audio, and makes only one determination per audio. Use at-start LID if the language in the audio won't change.
- Continuous LID can identify multiple languages for the duration of the audio. Use continuous LID if the language in the audio could change. Please note, the accuracy is limited if multiple languages are used within the same utterance. 

You implement at-start LID or continuous LID by calling methods for [recognize once or continuous](#recognize-once-or-continuous). Results also depend upon your [Accuracy and Latency prioritization](#accuracy-and-latency-prioritization).

### Accuracy and Latency prioritization

You can choose to prioritize accuracy or latency with language identification. 

> [!NOTE]
> Low latency is prioritized by default with the Speech SDK. You can choose to prioritize accuracy or latency with the Speech SDKs for C#, C++, and Python.

Prioritize `Latency` if you need a low-latency result such as during live streaming. Set the priority to `Accuracy` if the audio quality may be poor, and more latency is acceptable. For example, a voicemail could have background noise, or some silence at the beginning. Allowing the engine more time will improve language identification results. 

* **At-start:** With at-start LID in `Latency` mode the result is returned in less than 5 seconds. With at-start LID in `Accuracy` mode the result is returned in 30 seconds. You set the priority for at-start LID with the `SpeechServiceConnection_SingleLanguageIdPriority` property.   
* **Continuous:** With continuous LID in `Latency` mode the results are returned every 2 seconds for the duration of the audio. With continuous LID in `Accuracy` mode the results are returned within no set time frame for the duration of the audio. You set the priority for continuous LID with the `SpeechServiceConnection_ContinuousLanguageIdPriority` property. 

Speech uses at-start LID with `Latency` prioritization by default. You need to set a priority property for any other LID configuration. Here is an example of using continuous LID while still prioritizing latency. 

::: zone pivot="programming-language-csharp"
```csharp
speechConfig.SetProperty(PropertyId.SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");
```
::: zone-end
::: zone pivot="programming-language-cpp"
```cpp
speechConfig->SetProperty(PropertyId::SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");
```
::: zone-end
::: zone pivot="programming-language-python"
```python
speech_config.set_property(property_id=speechsdk.PropertyId.SpeechServiceConnection_ContinuousLanguageIdPriority, value='Latency')
```
::: zone-end

If none of the candidate languages are present in the audio or if the language identification confidence is low, the returned result can vary by mode. 
* In `Latency` priority mode the Speech service returns one of the candidate languages provided, even if those languages were not in the audio. For example, if `fr-FR` (French) and `en-US` (English) are provided as candidates, but German is spoken, either "French" or "English" would be returned. 
* In `Accuracy` priority mode the Speech service returns "Unknown" if none of the candidate languages are detected or if the language identification confidence is low. 

### Recognize once or continuous

Language identification is performed during a process called recognition. You will make a request to the Speech service for recognition of audio. 

> [!IMPORTANT]
> Don't confuse recognition with identification. Recognition can be used with or without language identification, but language identification requires recognition.

Let's map these concepts to the code. You will either call the recognize once method, or the start and stop continuous recognition methods. You choose from:
- Recognize once with at-start LID
- Continuous recognition with at start LID
- Continuous recognition with continuous LID 

The `SpeechServiceConnection_ContinuousLanguageIdPriority` property is always required for continuous LID. Without it the speech service defaults to at-start lid. 

::: zone pivot="programming-language-csharp"
```csharp
// Recognize once with At-start LID
var result = await recognizer.RecognizeOnceAsync();

// Start and stop continuous recognition with At-start LID
await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);
await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);

// Start and stop continuous recognition with Continuous LID
speechConfig.SetProperty(PropertyId.SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");
await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);
await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
```
::: zone-end
::: zone pivot="programming-language-cpp"
```cpp
// Recognize once with At-start LID
auto result = recognizer->RecognizeOnceAsync().get();

// Start and stop continuous recognition with At-start LID
recognizer->StartContinuousRecognitionAsync().get();
recognizer->StopContinuousRecognitionAsync().get();

// Start and stop continuous recognition with Continuous LID
speechConfig->SetProperty(PropertyId::SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");
recognizer->StartContinuousRecognitionAsync().get();
recognizer->StopContinuousRecognitionAsync().get();
```
::: zone-end
::: zone pivot="programming-language-python"
```python
# Recognize once with At-start LID
result = recognizer.recognize_once()

# Start and stop continuous recognition with At-start LID
source_language_recognizer.start_continuous_recognition()
source_language_recognizer.stop_continuous_recognition()

# Start and stop continuous recognition with Continuous LID
speech_config.set_property(property_id=speechsdk.PropertyId.SpeechServiceConnection_ContinuousLanguageIdPriority, value='Latency')
source_language_recognizer.start_continuous_recognition()
source_language_recognizer.stop_continuous_recognition()
```
::: zone-end


## Standalone language identification

You use standalone language identification when you only need to detect the natural language in an audio source. 

> [!NOTE]
> Standalone source language recognition is only supported with the Speech SDKs for C#, C++, and Python.

::: zone pivot="programming-language-csharp"

### [Recognize once](#tab/once)

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>","<paste-your-region>");

speechConfig.SetProperty(PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "ja-JP", "zh-CN" });

using (var recognizer = new SourceLanguageRecognizer(speechConfig, autoDetectSourceLanguageConfig))
{
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.RecognizedSpeech)
    {
        var lang = AutoDetectSourceLanguageResult.FromResult(result).Language;
        Console.WriteLine($"DETECTED: Language={lang}");
    }
}
```


### [Continuous recognition](#tab/continuous)

:::code language="csharp" source="~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/standalone_language_detection_samples.cs" id="languageDetectionContinuousWithFile":::

---

See more examples of standalone language identification on [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/standalone_language_detection_samples.cs).

::: zone-end

::: zone pivot="programming-language-cpp"

### [Recognize once](#tab/once)

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto config = SpeechConfig::FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
speechConfig->SetProperty(PropertyId::SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "ja-JP", "zh-CN" });

auto recognizer = SourceLanguageRecognizer::FromConfig(config, autoDetectSourceLanguageConfig);
cout << "Say something...\n";

auto result = recognizer->RecognizeOnceAsync().get();
if (result->Reason == ResultReason::RecognizedSpeech)
{
    auto lidResult = AutoDetectSourceLanguageResult::FromResult(result);
    cout << "DETECTED: Language="<< lidResult->Language << std::endl;
}
```


### [Continuous recognition](#tab/continuous)

:::code language="cpp" source="~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/standalone_language_detection_samples.cpp" id="StandaloneLanguageDetectionInContinuousModeWithFileInput":::

---

See more examples of standalone language identification on [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/standalone_language_detection_samples.cpp).


::: zone-end

::: zone pivot="programming-language-python"

### [Recognize once](#tab/once)


```python
import azure.cognitiveservices.speech as speechsdk

speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
    
speech_speech_config.set_property(property_id=speechsdk.PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, value='Latency')

recognizer = speechsdk.SourceLanguageRecognizer(speech_config=speech_config, auto_detect_source_language_config=auto_detect_source_language_config)

result = recognizer.recognize_once()

if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("RECOGNIZED: {}".format(result))
    detectedSrcLang = result.properties[speechsdk.PropertyId.SpeechServiceConnection_AutoDetectSourceLanguageResult]
    print("Detected Language: {}".format(detectedSrcLang))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized")
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```


### [Continuous recognition](#tab/continuous)


:::code language="python" source="~/samples-cognitive-services-speech-sdk/samples/python/console/speech_language_detection_sample.py" id="SpeechContinuousLanguageDetectionWithFile":::

---

See more examples of standalone language identification on [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_language_detection_sample.py).


::: zone-end


## Speech-to-text

You use Speech-to-text language identification when you need to detect the natural language in an audio source and then transcribe it to text. 

> [!NOTE]
> Speech-to-text recognition with at-start language identification is supported with Speech SDKs in C#, C++, Python, Java, JavaScript, and Objective-C. Speech-to-text recognition with continuous language identification is only supported with Speech SDKs in C#, C++, and Python.

::: zone pivot="programming-language-csharp"

### [Recognize once](#tab/once)

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>","<paste-your-region>");

speechConfig.SetProperty(PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "ja-JP", "zh-CN" });

using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

### [Continuous recognition](#tab/continuous)


```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var region = "<paste-your-region>";
// Continuous LID requires the v2 endpoint. In a future SDK release you won't need to set it. 
var endpointString = $"wss://{region}.stt.speech.microsoft.com/speech/universal/v2";
var endpointUrl = new Uri(endpointString);

var config = SpeechConfig.FromEndpoint(endpointUrl, "<paste-your-subscription-key>");
// can switch "Latency" to "Accuracy" depending on priority
config.SetProperty(PropertyId.SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "ja-JP", "zh-CN" });

var stopRecognition = new TaskCompletionSource<int>();
using (var audioInput = AudioConfig.FromWavFileInput(@"path-to-your-audio-file.wav"))
{
    using (var recognizer = new SpeechRecognizer(config, autoDetectSourceLanguageConfig, audioInput))
    {
        // Subscribes to events.
        recognizer.Recognizing += (s, e) =>
        {
            if (e.Result.Reason == ResultReason.RecognizingSpeech)
            {
                Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(e.Result);
                Console.WriteLine($"DETECTED: Language={autoDetectSourceLanguageResult.Language}");
            }
        };

        recognizer.Recognized += (s, e) =>
        {
            if (e.Result.Reason == ResultReason.RecognizedSpeech)
            {
                Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(e.Result);
                Console.WriteLine($"DETECTED: Language={autoDetectSourceLanguageResult.Language}");
            }
            else if (e.Result.Reason == ResultReason.NoMatch)
            {
                Console.WriteLine($"NOMATCH: Speech could not be recognized.");
            }
        };

        recognizer.Canceled += (s, e) =>
        {
            Console.WriteLine($"CANCELED: Reason={e.Reason}");

            if (e.Reason == CancellationReason.Error)
            {
                Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                Console.WriteLine($"CANCELED: Did you update the subscription info?");
            }

            stopRecognition.TrySetResult(0);
        };

        recognizer.SessionStarted += (s, e) =>
        {
            Console.WriteLine("\n    Session started event.");
        };

        recognizer.SessionStopped += (s, e) =>
        {
            Console.WriteLine("\n    Session stopped event.");
            Console.WriteLine("\nStop recognition.");
            stopRecognition.TrySetResult(0);
        };

        // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
        await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

        // Waits for completion.
        // Use Task.WaitAny to keep the task rooted.
        Task.WaitAny(new[] { stopRecognition.Task });

        // Stops recognition.
        await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
    }
}
```

See more examples of speech-to-text language identification on [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/translation_samples.cs).

::: zone-end

::: zone pivot="programming-language-cpp"


### [Recognize once](#tab/once)

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto speechConfig = SpeechConfig::FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
speechConfig->SetProperty(PropertyId::SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "ja-JP", "zh-CN" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig
    );

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```


### [Continuous recognition](#tab/continuous)


```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto region = "<paste-your-region>";
// Continuous LID requires the v2 endpoint. In a future SDK release you won't need to set it. 
auto endpointString = std::format("wss://{}.stt.speech.microsoft.com/speech/universal/v2", region);
auto config = SpeechConfig::FromEndpoint(endpointString, "<paste-your-subscription-key>");

speechConfig->SetProperty(PropertyId::SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "ja-JP", "zh-CN" });

auto audioInput = AudioConfig::FromWavFileInput("path-to-your-audio-file.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, autoDetectSourceLanguageConfig, audioInput);

// promise for synchronization of recognition end.
promise<void> recognitionEnd;

// Subscribes to events.
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        auto lidResult = AutoDetectSourceLanguageResult::FromResult(e.Result);
        cout << "Recognizing in " << lidResult->Language << ": Text =" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            auto lidResult = AutoDetectSourceLanguageResult::FromResult(e.Result);
            cout << "RECOGNIZED in " << lidResult->Language << ": Text=" << e.Result->Text << "\n"
                << "  Offset=" << e.Result->Offset() << "\n"
                << "  Duration=" << e.Result->Duration() << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;

        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });

recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

See more examples of speech-to-text language identification on [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_with_language_id_samples.cs).


::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

See more examples of speech-to-text language identification on [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java).

::: zone-end

::: zone pivot="programming-language-python"


### [Recognize once](#tab/once)

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```


### [Continuous recognition](#tab/continuous)


```python
def speech_recognize_continuous_from_file():
    """performs continuous speech recognition with input from an audio file"""
    
    speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
    audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)

    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)

    done = False

    def stop_cb(evt):
        """callback that signals to stop continuous recognition upon receiving an event `evt`"""
        print('CLOSING on {}'.format(evt))
        nonlocal done
        done = True

    # Connect callbacks to the events fired by the speech recognizer
    speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
    speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
    speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
    speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
    speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))
    # stop continuous recognition on either session stopped or canceled events
    speech_recognizer.session_stopped.connect(stop_cb)
    speech_recognizer.canceled.connect(stop_cb)

    # Start continuous speech recognition
    speech_recognizer.start_continuous_recognition()
    while not done:
        time.sleep(.5)

    speech_recognizer.stop_continuous_recognition()
```

See more examples of speech-to-text language identification on [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
NSArray *languages = @[@"en-US", @"ja-JP", @"zh-CN"];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]init:languages];
SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                           autoDetectSourceLanguageConfiguration:autoDetectSourceLanguageConfig
                                              audioConfiguration:audioConfig];
SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXAutoDetectSourceLanguageResult *languageDetectionResult = [[SPXAutoDetectSourceLanguageResult alloc] init:result];
NSString *detectedLanguage = [languageDetectionResult language];
```


::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var autoDetectSourceLanguageConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromLanguages(["en-US", "de-DE"]);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var languageDetectionResult = SpeechSDK.AutoDetectSourceLanguageResult.fromResult(result);
        var detectedLanguage = languageDetectionResult.language;
},
{});
```

::: zone-end


### Using Speech-to-text custom models

In addition to language identification using Speech service base models, you can also specify a custom model for enhanced recognition. If a custom model isn't provided, the service will use the default language model.

The sample below illustrates how to specify a custom model in your call to the Speech service. If the detected language is `en-US`, then the default model is used. If the detected language is `fr-FR`, then the custom model endpoint is used.

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSourceLanguageConfiguration* enLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"en-US"];
SPXSourceLanguageConfiguration* frLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"fr-FR"
                                                     endpointId:@"The Endpoint Id for custom model of fr-FR"];
NSArray *languageConfigs = @[enLanguageConfig, frLanguageConfig];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]initWithSourceLanguageConfigurations:languageConfigs];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var enLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("en-US");
var frLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR");
var autoDetectSourceLanguageConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs([enLanguageConfig, frLanguageConfig]);
```

::: zone-end


## Speech translation

You use Speech translation language identification when you need to detect the natural language in an audio source and then translate it to another language. 

> [!NOTE]
> Translation recognition with language identification is only supported with Speech SDKs in C#, C++, and Python. 


::: zone pivot="programming-language-csharp"


### [Recognize once](#tab/once)


```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;

public static async Task RecognizeOnceSpeechTranslationAsync()
{
    var speechTranslationConfig = SpeechTranslationConfig.FromSubscription(key, region);

    speechTranslationConfig.SetProperty(PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

    //Source lang is required, but is currently NoOp 
    string fromLanguage = "en-US";
    speechTranslationConfig.SpeechRecognitionLanguage = fromLanguage;

    speechTranslationConfig.AddTargetLanguage("de");
    speechTranslationConfig.AddTargetLanguage("fr");

    var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "ja-JP", "zh-CN" });

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();

    using (var recognizer = new TranslationRecognizer(
        speechTranslationConfig, 
        autoDetectSourceLanguageConfig,
        audioConfig))
    {
        
        Console.WriteLine("Say something or read from file...");
        var result = await recognizer.RecognizeOnceAsync().ConfigureAwait(false);

        if (result.Reason == ResultReason.TranslatedSpeech)
        {
            var lidResult = result.Properties.GetProperty(PropertyId.SpeechServiceConnection_AutoDetectSourceLanguageResult);

            Console.WriteLine($"RECOGNIZED in '{lidResult}': Text={result.Text}");
            foreach (var element in result.Translations)
            {
                Console.WriteLine($"    TRANSLATED into '{element.Key}': {element.Value}");
            }
        }
    }
}
```

### [Continuous recognition](#tab/continuous)

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;

public static async Task MultiLingualTranslation()
{
    var region = "<paste-your-region>";
    // Continuous LID requires the v2 endpoint. In a future SDK release you won't need to set it. 
    var endpointString = $"wss://{region}.stt.speech.microsoft.com/speech/universal/v2";
    var endpointUrl = new Uri(endpointString);
    
    var config = SpeechTranslationConfig.FromEndpoint(endpointUrl, "<paste-your-subscription-key>");

    // Source lang is required, but is currently NoOp 
    string fromLanguage = "en-US";
    config.SpeechRecognitionLanguage = fromLanguage;

    config.AddTargetLanguage("de");
    config.AddTargetLanguage("fr");

    config.SetProperty(PropertyId.SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");
    var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "ja-JP", "zh-CN" });

    var stopTranslation = new TaskCompletionSource<int>();
    using (var audioInput = AudioConfig.FromWavFileInput(@"path-to-your-audio-file.wav"))
    {
        using (var recognizer = new TranslationRecognizer(config, autoDetectSourceLanguageConfig, audioInput))
        {
            recognizer.Recognizing += (s, e) =>
            {
                var lidResult = e.Result.Properties.GetProperty(PropertyId.SpeechServiceConnection_AutoDetectSourceLanguageResult);

                Console.WriteLine($"RECOGNIZING in '{lidResult}': Text={e.Result.Text}");
                foreach (var element in e.Result.Translations)
                {
                    Console.WriteLine($"    TRANSLATING into '{element.Key}': {element.Value}");
                }
            };

            recognizer.Recognized += (s, e) => {
                if (e.Result.Reason == ResultReason.TranslatedSpeech)
                {
                    var lidResult = e.Result.Properties.GetProperty(PropertyId.SpeechServiceConnection_AutoDetectSourceLanguageResult);

                    Console.WriteLine($"RECOGNIZED in '{lidResult}': Text={e.Result.Text}");
                    foreach (var element in e.Result.Translations)
                    {
                        Console.WriteLine($"    TRANSLATED into '{element.Key}': {element.Value}");
                    }
                }
                else if (e.Result.Reason == ResultReason.RecognizedSpeech)
                {
                    Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                    Console.WriteLine($"    Speech not translated.");
                }
                else if (e.Result.Reason == ResultReason.NoMatch)
                {
                    Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                }
            };

            recognizer.Canceled += (s, e) =>
            {
                Console.WriteLine($"CANCELED: Reason={e.Reason}");

                if (e.Reason == CancellationReason.Error)
                {
                    Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                    Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                    Console.WriteLine($"CANCELED: Did you update the subscription info?");
                }

                stopTranslation.TrySetResult(0);
            };

            recognizer.SpeechStartDetected += (s, e) => {
                Console.WriteLine("\nSpeech start detected event.");
            };

            recognizer.SpeechEndDetected += (s, e) => {
                Console.WriteLine("\nSpeech end detected event.");
            };

            recognizer.SessionStarted += (s, e) => {
                Console.WriteLine("\nSession started event.");
            };

            recognizer.SessionStopped += (s, e) => {
                Console.WriteLine("\nSession stopped event.");
                Console.WriteLine($"\nStop translation.");
                stopTranslation.TrySetResult(0);
            };

            // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
            Console.WriteLine("Start translation...");
            await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

            Task.WaitAny(new[] { stopTranslation.Task });
            await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
        }
    }
}
```

See more examples of speech translation language identification on [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/translation_samples.cs).

::: zone-end

::: zone pivot="programming-language-cpp"


### [Recognize once](#tab/once)

:::code language="cpp" source="~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp" id="TranslationAndLanguageIdWithMicrophone":::

### [Continuous recognition](#tab/continuous)

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;

void MultiLingualTranslation()
{
    auto region = "<paste-your-region>";
    // Continuous LID requires the v2 endpoint. In a future SDK release you won't need to set it. 
    auto endpointString = std::format("wss://{}.stt.speech.microsoft.com/speech/universal/v2", region);
    auto config = SpeechTranslationConfig::FromEndpoint(endpointString, "<paste-your-subscription-key>");

    speechConfig->SetProperty(PropertyId::SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");
    auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "ja-JP", "zh-CN" });

    promise<void> recognitionEnd;
    // Source lang is required, but is currently NoOp 
    auto fromLanguage = "en-US";
    config->SetSpeechRecognitionLanguage(fromLanguage);
    config->AddTargetLanguage("de");
    config->AddTargetLanguage("fr");

    auto audioInput = AudioConfig::FromWavFileInput("path-to-your-audio-file.wav");
    auto recognizer = TranslationRecognizer::FromConfig(config, autoDetectSourceLanguageConfig, audioInput);

    recognizer->Recognizing.Connect([](const TranslationRecognitionEventArgs& e)
        {
            std::string lidResult = e.Result->Properties.GetProperty(PropertyId::SpeechServiceConnection_AutoDetectSourceLanguageResult);

            cout << "Recognizing in Language = "<< lidResult << ":" << e.Result->Text << std::endl;
            for (const auto& it : e.Result->Translations)
            {
                cout << "  Translated into '" << it.first.c_str() << "': " << it.second.c_str() << std::endl;
            }
        });

    recognizer->Recognized.Connect([](const TranslationRecognitionEventArgs& e)
        {
            if (e.Result->Reason == ResultReason::TranslatedSpeech)
            {
                std::string lidResult = e.Result->Properties.GetProperty(PropertyId::SpeechServiceConnection_AutoDetectSourceLanguageResult);
                cout << "RECOGNIZED in Language = " << lidResult << ": Text=" << e.Result->Text << std::endl;
            }
            else if (e.Result->Reason == ResultReason::RecognizedSpeech)
            {
                cout << "RECOGNIZED: Text=" << e.Result->Text << " (text could not be translated)" << std::endl;
            }
            else if (e.Result->Reason == ResultReason::NoMatch)
            {
                cout << "NOMATCH: Speech could not be recognized." << std::endl;
            }

            for (const auto& it : e.Result->Translations)
            {
                cout << "  Translated into '" << it.first.c_str() << "': " << it.second.c_str() << std::endl;
            }
        });

    recognizer->Canceled.Connect([&recognitionEnd](const TranslationRecognitionCanceledEventArgs& e)
        {
            cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
            if (e.Reason == CancellationReason::Error)
            {
                cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << e.ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;

                recognitionEnd.set_value();
            }
        });

    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto size = e.Result->Audio.size();
            cout << "Translation synthesis result: size of audio data: " << size
                << (size == 0 ? "(END)" : "");
        });

    recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
        {
            cout << "Session stopped.";
            recognitionEnd.set_value();
        });

    // Starts continuos recognition. Use StopContinuousRecognitionAsync() to stop recognition.
    recognizer->StartContinuousRecognitionAsync().get();
    recognitionEnd.get_future().get();
    recognizer->StopContinuousRecognitionAsync().get();
}
```

See more examples of speech translation language identification on [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/translation_samples.cpp).

::: zone-end

::: zone pivot="programming-language-python"


### [Recognize once](#tab/once)

:::code language="python" source="~/samples-cognitive-services-speech-sdk/samples/python/console/translation_sample.py" id="TranslationOnceWithFile":::

### [Continuous recognition](#tab/continuous)

:::code language="python" source="~/samples-cognitive-services-speech-sdk/samples/python/console/translation_sample.py" id="TranslationContinuous":::

---

See more examples of speech translation language identification on [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/translation_sample.py).

::: zone-end
