---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
---

The container provides websocket-based query endpoint APIs, that are accessed through the [Speech SDK](../index.yml). By default, the Speech SDK uses online speech services. To use the container, you need to change the initialization method.

> [!TIP]
> When using the Speech SDK with containers, you do not need to provide the Azure Speech resource [subscription key or an authentication bearer token](../rest-speech-to-text.md#authentication).

See the examples below.

# [C#](#tab/csharp)

Change from using this Azure-cloud initialization call:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

To using this call with the container [host](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?preserve-view=true&view=azure-dotnet):

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# [Python](#tab/python)

Change from using this Azure-cloud initialization call:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

To using this call with the container [endpoint](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python):

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---