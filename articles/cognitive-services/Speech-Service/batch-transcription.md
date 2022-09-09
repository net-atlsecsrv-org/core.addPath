---
title: How to use batch transcription - Speech service
titleSuffix: Azure Cognitive Services
description: Batch transcription is ideal if you want to transcribe a large quantity of audio in storage, such as Azure Blobs. By using the dedicated REST API, you can point to audio files with a shared access signature (SAS) URI and asynchronously receive transcriptions.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: panosper
---

# How to use batch transcription

Batch transcription is ideal for transcribing a large amount of audio in storage. By using the dedicated REST API, you can point to audio files with a shared access signature (SAS) URI and asynchronously receive transcription results.

The API offers asynchronous speech-to-text transcription and other features. You can use REST API to expose methods to:

- Create a batch processing requests
- Query the status
- Download transcription results
- Delete transcription information from the service

The detailed API is available as a [Swagger document](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A), under the heading `Custom Speech transcriptions`.

Batch transcription jobs are scheduled on a best effort basis. Currently there is no estimate for when a job will change into the running state. Under normal system load, it should happen within minutes. Once in the running state, the actual transcription is processed faster than the audio real time.

Next to the easy-to-use API, you don't need to deploy custom endpoints, and you don't have any concurrency requirements to observe.

## Prerequisites

### Subscription Key

As with all features of the Speech service, you create a subscription key from the [Azure portal](https://portal.azure.com) by following our [Get started guide](get-started.md).

>[!NOTE]
> A standard subscription (S0) for Speech service is required to use batch transcription. Free subscription keys (F0) will not work. For more information, see [pricing and limits](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### Custom models

If you plan to customize acoustic or language models, follow the steps in [Customize acoustic models](how-to-customize-acoustic-models.md) and [Design customization language models](how-to-customize-language-model.md). To use the created models in batch transcription, you need their model IDs. You can retrieve the model ID when you inspect the details of the model. A deployed custom endpoint is not needed for the batch transcription service.

## The Batch Transcription API

### Supported formats

The Batch Transcription API supports the following formats:

| Format | Codec | Bitrate | Sample Rate |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 8 kHz or 16 kHz, mono or stereo |
| MP3 | PCM | 16-bit | 8 kHz or 16 kHz, mono or stereo |
| OGG | OPUS | 16-bit | 8 kHz or 16 kHz, mono or stereo |

For stereo audio streams, the left and right channels are split during the transcription. For each channel, a JSON result file is being created. The timestamps generated per utterance enable the developer to create an ordered final transcript.

### Configuration

Configuration parameters are provided as JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<SAS to Azure container to store results into (write permission required)>"
  }
}
```

### Configuration properties

Use these optional properties to configure transcription:

| Parameter | Description |
|-----------|------------|
|`ProfanityFilterMode`|Specifies how to handle profanity in recognition results
||**`Masked`** - Default. Replaces profanity with asterisks<br>`None` - Disables profanity filtering<br>`Removed` - Removes all profanity from the result<br>`Tags` - Adds profanity tags
|`PunctuationMode`|Specifies to handle punctuation in recognition results
||`Automatic` - The service inserts punctuation<br>`Dictated` - Dictated (spoken) punctuation<br>**`DictatedAndAutomatic`** - Default. Dictated and automatic punctuation<br>`None` - Disables punctuation
|`AddWordLevelTimestamps`|Specifies if word level timestamps should be added to the output
||`True` - Enables word level timestamps<br>**`False`** - Default. Disable word level timestamps
|`AddSentiment`|Specifies if sentiment analysis is added to the utterance
||`True` - Enables sentiment per utterance<br>**`False`** - Default. Disable sentiment
|`AddDiarization`|Specifies if diarization analysis is carried out. If `true`, the input is expected to be mono channel audio containing a maximum of two voices. `AddWordLevelTimestamps` needs to be set to `true`
||`True` - Enables diarization<br>**`False`** - Default. Disable diarization
|`TranscriptionResultsContainerUrl`|Optional SAS token to a writeable container in Azure. The result will be stored in this container

### Storage

Batch transcription supports [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) for reading audio and writing transcriptions to storage.

## The batch transcription result

For mono input audio, one transcription result file is being created. For stereo input audio, two transcription result files are being created. Each has this structure:

```json
{
  "AudioFileResults":[ 
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                     'for each individual segment'
        {
          "RecognitionStatus": Success | Failure
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                     'null if no diarization
                                                            or stereo input file, the
                                                            speakerId as a string if
                                                            diarization requested for
                                                            mono audio file'
          "Offset": number                                'time in milliseconds'
          "Duration": number                              'time in milliseconds'
          "OffsetInSeconds" : number                      'Real number. Two decimal places'
          "DurationInSeconds" : number                    'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                        'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                          'this is omitted if sentiment is
                                                            not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                         'time in milliseconds'
                  "Duration": number                       'time in milliseconds'
                  "OffsetInSeconds": number                'Real number. Two decimal places'
                  "DurationInSeconds": number              'Real number. Two decimal places'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

The result contains these forms:

|Form|Content|
|-|-|
|`Lexical`|The actual words recognized.
|`ITN`|Inverse-text-normalized form of the recognized text. Abbreviations ("doctor smith" to "dr smith"), phone numbers, and other transformations are applied.
|`MaskedITN`|The ITN form with profanity masking applied.
|`Display`|The display form of the recognized text. This includes added punctuation and capitalization.

## Speaker separation (Diarization)

Diarization is the process of separating speakers in a piece of audio. Our Batch pipeline supports diarization and is capable of recognizing two speakers on mono channel recordings. The feature is not available on stereo recordings.

All transcription output contains a `SpeakerId`. If diarization is not used, it will show `"SpeakerId": null` in the JSON output. For diarization we support two voices, so the speakers will be identified as `"1"` or `"2"`.

To request diarization, you simply have to add the relevant parameter in the HTTP request as shown below.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Word-level timestamps would also have to be 'turned on' as the parameters in the above request indicate.

## Sentiment analysis

The sentiment feature estimates the sentiment expressed in the audio. The sentiment is expressed by a value between 0 and 1 for `Negative`, `Neutral`, and `Positive` sentiment. For example, sentiment analysis can be used in call center scenarios:

- Get insight on customer satisfaction
- Get insight on the performance of the agents (team taking the calls)
- Find the exact point in time when a call took a turn in a negative direction
- What went well when turning a negative call into a positive direction
- Identify what customers like and what they dislike about a product or a service

Sentiment is scored per audio segment based on the lexical form. The entire text within that audio segment is used to calculate sentiment. No aggregate sentiment is being calculated for the entire transcription.

A JSON output sample looks like below:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## Best practices

The transcription service can handle large number of submitted transcriptions. You can query the status of your transcriptions through a `GET` on the [transcriptions method](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Keep the information returned to a reasonable size by specifying the `take` parameter (a few hundred). [Delete transcriptions](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regularly from the service once you retrieved the results. This will guarantee quick replies from the transcription management calls.

## Sample code

Complete samples are available in the [GitHub sample repository](https://aka.ms/csspeech/samples) inside the `samples/batch` subdirectory.

You have to customize the sample code with your subscription information, the service region, the SAS URI pointing to the audio file to transcribe, and model IDs in case you want to use a custom acoustic or language model.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

The sample code will set up the client and submit the transcription request. It will then poll for status information and print details about the transcription progress.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

For full details about the preceding calls, see our [Swagger document](https://westus.cris.ai/swagger/ui/index). For the full sample shown here, go to [GitHub](https://aka.ms/csspeech/samples) in the `samples/batch` subdirectory.

Take note of the asynchronous setup for posting audio and receiving transcription status. The client that you create is a .NET HTTP client. There's a `PostTranscriptions` method for sending the audio file details and a `GetTranscriptions` method for receiving the results. `PostTranscriptions` returns a handle, and `GetTranscriptions` uses it to create a handle to get the transcription status.

The current sample code doesn't specify a custom model. The service uses the baseline models for transcribing the file or files. To specify the models, you can pass on the same method as the model IDs for the acoustic and the language model.

> [!NOTE]
> For baseline transcriptions, you don't need to declare the ID for the baseline models. If you only specify a language model ID (and no acoustic model ID), a matching acoustic model is automatically selected. If you only specify an acoustic model ID, a matching language model is automatically selected.

## Download the sample

You can find the sample in the `samples/batch` directory in the [GitHub sample repository](https://aka.ms/csspeech/samples).

## Next steps

* [Get your Speech trial subscription](https://azure.microsoft.com/try/cognitive-services/)
