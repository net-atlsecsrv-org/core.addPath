---
title: Azure Speaker Recognition service
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Speaker Recognition provides algorithms that verify and identify speakers by their unique voice characteristics. Speaker Recognition is used to answer the question “who is speaking?”.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: trbye
---

# What is the Azure Speaker Recognition service?

The Speaker Recognition service provides algorithms that verify and identify speakers by their unique voice characteristics. Speaker Recognition is used to answer the question “who is speaking?”. You provide audio training data for a single speaker, which creates an enrollment profile based on the unique characteristics of the speaker's voice. You can then cross-check audio voice samples against this profile to verify that the speaker is the same person (speaker verification), or cross-check audio voice samples against a *group* of enrolled speaker profiles, to see if it matches any profile in the group (speaker identification). In contrast, [Speaker Diarization](batch-transcription.md#speaker-separation-diarization) groups segments of audio by speaker in a batch operation.

## Speaker Verification

Speaker Verification streamlines the process of verifying an enrolled speaker identity with either passphrases or free-form voice input. It can be used to verify individuals for secure, frictionless customer engagements in a wide range of solutions, from customer identity verification in call centers to contact-less facility access.

### How does Speaker Verification work?

![How does speaker verification work](media/speaker-recognition/speaker-rec.png)

Speaker verification can be either text-dependent or text-independent. **Text-dependent** verification means speakers need to choose the same passphrase to use during both enrollment and verification phases. **Text-independent** verification means speakers can speak in everyday language in the enrollment and verification phrases.

For **text-dependent** verification, the speaker's voice is enrolled by saying a passphrase from a set of predefined phrases. Voice features are extracted from the audio recording to form a unique voice signature, while the chosen passphrase is also recognized. Together, the voice signature and the passphrase are used to verify the speaker. 

**Text-independent** verification has no restrictions on what the speaker says during enrollment or in the audio sample to be verified, as it only extracts voice features to score similarity. 

The APIs are not intended to determine whether the audio is from a live person or an imitation/recording of an enrolled speaker. 

## Speaker Identification

Speaker Identification is used to determine an unknown speaker’s identity within a group of enrolled speakers. Speaker Identification enables you to attribute speech to individual speakers, and unlock value from scenarios with multiple speakers, such as:

* Support solutions for remote meeting productivity 
* Build multi-user device personalization

### How does Speaker Identification work?

Enrollment for speaker identification is **text-independent**, which means that there are no restrictions on what the speaker says in the audio. Similar to Speaker Verification, in the enrollment phase the speaker's voice is recorded, and voice features are extracted to form a unique voice signature. In the identification phase, the input voice sample is compared to a specified list of enrolled voices (up to 50 in each request).

## Data security and privacy

Speaker enrollment data is stored in a secured system, including the speech audio for enrollment and the voice signature features. The speech audio for enrollment is only used when the algorithm is upgraded, and the features need to be extracted again. The service does not retain the speech recording or the extracted voice features that are sent to the service during the recognition phase. 

You control how long data should be retained. You can create, update, and delete enrollment data for individual speakers through API calls. When the subscription is deleted, all the speaker enrollment data associated with the subscription will also be deleted. 

As with all of the Cognitive Services resources, developers who use the Speaker Recognition service must be aware of Microsoft's policies on customer data. You should ensure that you have received the appropriate permissions from the users for Speaker Recognition. For more information, see the [Cognitive Services page](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) on the Microsoft Trust Center. 

## Next steps

> [!div class="nextstepaction"]
> * See the [video tutorial](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) for text-independent speaker verification.
