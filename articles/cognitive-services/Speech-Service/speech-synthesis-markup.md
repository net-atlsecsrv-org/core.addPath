---
title: Speech Synthesis Markup Language (SSML) - Speech service
titleSuffix: Azure Cognitive Services
description: Using the Speech Synthesis Markup Language to control pronunciation and prosody in text-to-speech.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
---

# Improve synthesis with Speech Synthesis Markup Language (SSML)

Speech Synthesis Markup Language (SSML) is an XML-based markup language that lets developers specify how input text is converted into synthesized speech using the text-to-speech service. Compared to plain text, SSML allows developers to fine-tune the pitch, pronunciation, speaking rate, volume, and more of the text-to-speech output. Normal punctuation, such as pausing after a period, or using the correct intonation when a sentence ends with a question mark are automatically handled.

The Speech service implementation of SSML is based on World Wide Web Consortium's [Speech Synthesis Markup Language Version 1.0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Chinese, Japanese, and Korean characters count as two characters for billing. For more information, see [Pricing](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## Standard, neural, and custom voices

Choose from standard and neural voices, or create your own custom voice unique to your product or brand. 75+ standard voices are available in more than 45 languages and locales, and 5 neural voices are available in four languages and locales. For a complete list of supported languages, locales, and voices (neural and standard), see [language support](language-support.md).

To learn more about standard, neural, and custom voices, see [Text-to-speech overview](text-to-speech.md).

## Special characters

While using SSML, keep in mind that special characters, such as quotation marks, apostrophes, and brackets must be escaped. For more information, see [Extensible Markup Language (XML) 1.0: Appendix D](https://www.w3.org/TR/xml/#sec-entexpand).

## Supported SSML elements

Each SSML document is created with SSML elements (or tags). These elements are used to adjust pitch, prosody, volume, and more. The following sections detail how each element is used, and when an element is required or optional.  

> [!IMPORTANT]
> Don't forget to use double quotes around attribute values. Standards for well-formed, valid XML requires attribute values to be enclosed in double quotation marks. For example, `<prosody volume="90">` is a well-formed, valid element, but `<prosody volume=90>` is not. SSML may not recognize attribute values that are not in quotes.

## Create an SSML document

`speak` is the root element, and is **required** for all SSML documents. The `speak` element contains important information, such as version, language, and the markup vocabulary definition.

**Syntax**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attributes**

| Attribute | Description | Required / Optional |
|-----------|-------------|---------------------|
| `version` | Indicates the version of the SSML specification used to interpret the document markup. The current version is 1.0. | Required |
| `xml:lang` | Specifies the language of the root document. The value may contain a lowercase, two-letter language code (for example, `en`), or the language code and uppercase country/region (for example, `en-US`). | Required |
| `xmlns` | Specifies the URI to the document that defines the markup vocabulary (the element types and attribute names) of the SSML document. The current URI is http://www.w3.org/2001/10/synthesis. | Required |

## Choose a voice for text-to-speech

The `voice` element is required. It is used to specify the voice that is used for text-to-speech.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attributes**

| Attribute | Description | Required / Optional |
|-----------|-------------|---------------------|
| `name` | Identifies the voice used for text-to-speech output. For a complete list of supported voices, see [Language support](language-support.md#text-to-speech). | Required |

**Example**

> [!NOTE]
> This example uses the `en-US-AriaRUS` voice. For a complete list of supported voices, see [Language support](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## Use multiple voices

Within the `speak` element, you can specify multiple voices for text-to-speech output. These voices can be in different languages. For each voice, the text must be wrapped in a `voice` element. 

**Attributes**

| Attribute | Description | Required / Optional |
|-----------|-------------|---------------------|
| `name` | Identifies the voice used for text-to-speech output. For a complete list of supported voices, see [Language support](language-support.md#text-to-speech). | Required |

> [!IMPORTANT]
> Multiple voices are incompatible with the word boundary feature. The word boundary feature needs to be disabled in order to use multiple voices.

### Disable word boundary

Depending on the Speech SDK language, you'll set the `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` property to `false` on an instance of the `SpeechConfig` object.

# [C#](#tab/csharp)

For more information, see <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# [C++](#tab/cpp)

For more information, see <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# [Java](#tab/java)

For more information, see <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# [Python](#tab/python)

For more information, see <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# [JavaScript](#tab/javascript)

For more information, see <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# [Objective-C](#tab/objectivec)

For more information, see <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# [Swift](#tab/swift)

For more information, see <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Example**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## Adjust speaking styles

> [!IMPORTANT]
> The adjustment of speaking styles will only work with neural voices.

By default, the text-to-speech service synthesizes text using a neutral speaking style for both standard and neural voices. With neural voices, you can adjust the speaking style to express cheerfulness, empathy, or sentiment with the `<mstts:express-as>` element. This is an optional element unique to the Speech service.

Currently, speaking style adjustments are supported for these neural voices:
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`
* `pt-BR-FranciscaNeural`

Changes are applied at the sentence level, and style vary by voice. If a style isn't supported, the service will return speech in the default neutral speaking style.

**Syntax**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**Attributes**

| Attribute | Description | Required / Optional |
|-----------|-------------|---------------------|
| `style` | Specifies the speaking style. Currently, speaking styles are voice-specific. | Required if adjusting the speaking style for a neural voice. If using `mstts:express-as`, then style must be provided. If an invalid value is provided, this element will be ignored. |

Use this table to determine which speaking styles are supported for each neural voice.

| Voice | Style | Description |
|-------|------|-------------|
| `en-US-AriaNeural` | `style="newscast"` | Expresses a formal and professional tone for narrating news |
| | `style="customerservice"` | Expresses a friendly and helpful tone for customer support |
| | `style="chat"` | Expresses a casual and relaxed tone |
| | `style="cheerful"` | Expresses a positive and happy tone |
| | `style="empathetic"` | Expresses a sense of caring and understanding |
| `zh-CN-XiaoxiaoNeural` | `style="newscast"` | Expresses a formal and professional tone for narrating news |
| | `style="customerservice"` | Expresses a friendly and helpful tone for customer support |
| | `style="assistant"` | Expresses a warm and relaxed tone for digital assistants  |
| | `style="lyrical"` | Expresses emotions in a melodic and sentimental way |
| `pt-BR-FranciscaNeural` | `style="cheerful"` | Expresses a positive and happy tone |

**Example**

This SSML snippet illustrates how the `<mstts:express-as>` element is used to change the speaking style to `cheerful`.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## Add or remove a break/pause

Use the `break` element to insert pauses (or breaks) between words, or prevent pauses automatically added by the text-to-speech service.

> [!NOTE]
> Use this element to override the default behavior of text-to-speech (TTS) for a word or phrase if the synthesized speech for that word or phrase sounds unnatural. Set `strength` to `none` to prevent a prosodic break, which is automatically inserted by the text-to-speech service.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Attributes**

| Attribute | Description | Required / Optional |
|-----------|-------------|---------------------|
| `strength` | Specifies the relative duration of a pause using one of the following values:<ul><li>none</li><li>x-weak</li><li>weak</li><li>medium (default)</li><li>strong</li><li>x-strong</li></ul> | Optional |
| `time` | Specifies the absolute duration of a pause in seconds or milliseconds. Examples of valid values are `2s` and `500` | Optional |

| Strength | Description |
|----------|-------------|
| None, or if no value provided | 0 ms |
| x-weak | 250 ms |
| weak | 500 ms |
| medium | 750 ms |
| strong | 1000 ms |
| x-strong | 1250 ms |


**Example**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## Specify paragraphs and sentences

`p` and `s` elements are used to denote paragraphs and sentences, respectively. In the absence of these elements, the text-to-speech service automatically determines the structure of the SSML document.

The `p` element may contain text and the following elements: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`, and `s`.

The `s` element may contain text and the following elements: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`, and `sub`.

**Syntax**

```XML
<p></p>
<s></s>
```

**Example**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## Use phonemes to improve pronunciation

The `ph` element is used to for phonetic pronunciation in SSML documents. The `ph` element can only contain text, no other elements. Always provide human-readable speech as a fallback.

Phonetic alphabets are composed of phones, which are made up of letters, numbers, or characters, sometimes in combination. Each phone describes a unique sound of speech. This is in contrast to the Latin alphabet, where any letter may represent multiple spoken sounds. Consider the different pronunciations of the letter "c" in the words "candy" and "cease", or the different pronunciations of the letter combination "th" in the words "thing" and "those".

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attributes**

| Attribute | Description | Required / Optional |
|-----------|-------------|---------------------|
| `alphabet` | Specifies the phonetic alphabet to use when synthesizing the pronunciation of the string in the `ph` attribute. The string specifying the alphabet must be specified in lowercase letters. The following are the possible alphabets that you may specify.<ul><li>`ipa` &ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">International Phonetic Alphabet <span class="docon docon-navigate-external x-hidden-focus"></span></a></li><li>`sapi` &ndash; [Speech service phonetic alphabet](speech-ssml-phonetic-sets.md)</li><li>`ups` &ndash; Universal Phone Set</li></ul><br>The alphabet applies only to the `phoneme` in the element.. | Optional |
| `ph` | A string containing phones that specify the pronunciation of the word in the `phoneme` element. If the specified string contains unrecognized phones, the text-to-speech (TTS) service rejects the entire SSML document and produces none of the speech output specified in the document. | Required if using phonemes. |

**Examples**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## Use custom lexicon to improve pronunciation

Sometimes TTS cannot accurately pronounce a word, for example, a company or foreign name. Developers can define the reading of these entities in SSML using `phoneme` and `sub` tag, or define the reading of multiple entities by referring to a custom lexicon file using `lexicon` tag.

**Syntax**

```XML
<lexicon uri="string"/>
```

**Attributes**

| Attribute | Description | Required / Optional |
|-----------|-------------|---------------------|
| `uri` | The address of the external PLS document. | Required. |

**Usage**

Step 1: Define custom lexicon 

You can define the reading of entities by a list of custom lexicon items, stored as an .xml or .pls file.

**Example**

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

Each `lexeme` element is a lexicon item. `grapheme` contains text describing the orthograph of `lexeme`. Readout form can be provided as `alias`. Phone string could be provided in `phoneme` element.

The `lexicon` element contains at least one `lexeme` element. Each `lexeme` element contains at least one `grapheme` element and one or more `grapheme`, `alais`, and `phoneme` elements. The `grapheme` element contains text describing the <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthography <span class="docon docon-navigate-external x-hidden-focus"></span></a>. The `alias` elements are used to indicate the pronunciation of an acronym or an abbreviated term. The `phoneme` element provides text describing how the `lexeme` is pronounced.

For more information about custom lexicon file, see [Pronunciation Lexicon Specification (PLS) Version 1.0](https://www.w3.org/TR/pronunciation-lexicon/) on the W3C website.

Step 2: Upload custom lexicon file created in step 1 online, you could store it anywhere, and we suggest you to store it in Microsoft Azure, for example [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Step 3: Refer to custom lexicon file in SSML

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BTW" will be read as "By the way". "Benigni" will be read with provided IPA "bɛˈniːnji".  

**Limitation**
- File size: custom lexicon file size maximum limit is 100KB, if beyond this size, synthesis request will fail.
- Lexicon cache refresh: custom lexicon will be cached with URI as key on TTS Service when it's first loaded. Lexicon with same URI won't be reloaded within 15 mins, so custom lexicon change needs to wait at most 15 mins to take effect.

**Speech service phonetic sets**

In the sample above, we're using the International Phonetic Alphabet, also known as the IPA phone set. We suggest developers use the IPA, because it is the international standard. Considering that the IPA is not easy to remember, the Speech service defines a phonetic set for seven languages (`en-US`, `fr-FR`, `de-DE`, `es-ES`, `ja-JP`, `zh-CN`, and `zh-TW`).

You can use the `sapi` as the vale for the `alphabet` attribute with custom lexicons as demonstrated below:

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

For more information on the detailed Speech service phonetic alphabet, see the [Speech service phonetic sets](speech-ssml-phonetic-sets.md).

## Adjust prosody

The `prosody` element is used to specify changes to pitch, contour, range, rate, duration, and volume for the text-to-speech output. The `prosody` element may contain text and the following elements: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`, and `s`.

Because prosodic attribute values can vary over a wide range, the speech recognizer interprets the assigned values as a suggestion of what the actual prosodic values of the selected voice should be. The text-to-speech service limits or substitutes values that are not supported. Examples of unsupported values are a pitch of 1 MHz or a volume of 120.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attributes**

| Attribute | Description | Required / Optional |
|-----------|-------------|---------------------|
| `pitch` | Indicates the baseline pitch for the text. You may express the pitch as:<ul><li>An absolute value, expressed as a number followed by "Hz" (Hertz). For example, 600 Hz.</li><li>A relative value, expressed as a number preceded by "+" or "-" and followed by "Hz" or "st", that specifies an amount to change the pitch. For example: +80 Hz or -2st. The "st" indicates the change unit is semitone, which is half of a tone (a half step) on the standard diatonic scale.</li><li>A constant value:<ul><li>x-low</li><li>low</li><li>medium</li><li>high</li><li>x-high</li><li>default</li></ul></li></ul>. | Optional |
| `contour` | Contour isn't supported for neural voices. Contour represents changes in pitch. These changes are represented as an array of targets at specified time positions in the speech output. Each target is defined by sets of parameter pairs. For example: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>The first value in each set of parameters specifies the location of the pitch change as a percentage of the duration of the text. The second value specifies the amount to raise or lower the pitch, using a relative value or an enumeration value for pitch (see `pitch`). | Optional |
| `range` | A value that represents the range of pitch for the text. You may express `range` using the same absolute values, relative values, or enumeration values used to describe `pitch`. | Optional |
| `rate` | Indicates the speaking rate of the text. You may express `rate` as:<ul><li>A relative value, expressed as a number that acts as a multiplier of the default. For example, a value of *1* results in no change in the rate. A value of *0.5* results in a halving of the rate. A value of *3* results in a tripling of the rate.</li><li>A constant value:<ul><li>x-slow</li><li>slow</li><li>medium</li><li>fast</li><li>x-fast</li><li>default</li></ul></li></ul> | Optional |
| `duration` | The period of time that should elapse while the speech synthesis (TTS) service reads the text, in seconds or milliseconds. For example, *2s* or *1800ms*. | Optional |
| `volume` | Indicates the volume level of the speaking voice. You may express the volume as:<ul><li>An absolute value, expressed as a number in the range of 0.0 to 100.0, from *quietest* to *loudest*. For example, 75. The default is 100.0.</li><li>A relative value, expressed as a number preceded by "+" or "-" that specifies an amount to change the volume. For example, +10 or -5.5.</li><li>A constant value:<ul><li>silent</li><li>x-soft</li><li>soft</li><li>medium</li><li>loud</li><li>x-loud</li><li>default</li></ul></li></ul> | Optional |

### Change speaking rate

Speaking rate can be applied to standard voices at the word or sentence-level. Whereas speaking rate can only be applied to neural voices at the sentence level.

**Example**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### Change volume

Volume changes can be applied to standard voices at the word or sentence-level. Whereas volume changes can only be applied to neural voices at the sentence level.

**Example**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### Change pitch

Pitch changes can be applied to standard voices at the word or sentence-level. Whereas pitch changes can only be applied to neural voices at the sentence level.

**Example**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### Change pitch contour

> [!IMPORTANT]
> Pitch contour changes aren't supported with neural voices.

**Example**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## say-as element

`say-as` is an optional element that indicates the content type (such as number or date) of the element's text. This provides guidance to the speech synthesis engine about how to pronounce the text.

**Syntax**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attributes**

| Attribute | Description | Required / Optional |
|-----------|-------------|---------------------|
| `interpret-as` | Indicates the content type of element's text. For a list of types, see the table below. | Required |
| `format` | Provides additional information about the precise formatting of the element's text for content types that may have ambiguous formats. SSML defines formats for content types that use them (see table below). | Optional |
| `detail` | Indicates the level of detail to be spoken. For example, this attribute might request that the speech synthesis engine pronounce punctuation marks. There are no standard values defined for `detail`. | Optional |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

The following are the supported content types for the `interpret-as` and `format` attributes. Include the `format` attribute only if `interpret-as` is set to date and time.

| interpret-as | format | Interpretation |
|--------------|--------|----------------|
| `address` | | The text is spoken as an address. The speech synthesis engine pronounces:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />As "I'm at 150th court north east redmond washington." |
| `cardinal`, `number` | | The text is spoken as a cardinal number. The speech synthesis engine pronounces:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />As "There are three alternatives." |
| `characters`, `spell-out` | | The text is spoken as individual letters (spelled out). The speech synthesis engine pronounces:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />As "T E S T." |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | The text is spoken as a date. The `format` attribute specifies the date's format (*d=day, m=month, and y=year*). The speech synthesis engine pronounces:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />As "Today is October nineteenth two thousand sixteen." |
| `digits`, `number_digit` | | The text is spoken as a sequence of individual digits. The speech synthesis engine pronounces:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />As "1 2 3 4 5 6 7 8 9." |
| `fraction` | | The text is spoken as a fractional number. The speech synthesis engine pronounces:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />As "three eighths of an inch." |
| `ordinal` | | The text is spoken as an ordinal number. The speech synthesis engine pronounces:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />As "Select the third option". |
| `telephone` | | The text is spoken as a telephone number. The `format` attribute may contain digits that represent a country code. For example, "1" for the United States or "39" for Italy. The speech synthesis engine may use this information to guide its pronunciation of a phone number. The phone number may also include the country code, and if so, takes precedence over the country code in the `format`. The speech synthesis engine pronounces:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />As "My number is area code eight eight eight five five five one two one two." |
| `time` | hms12, hms24 | The text is spoken as a time. The `format` attribute specifies whether the time is specified using a 12-hour clock (hms12) or a 24-hour clock (hms24). Use a colon to separate numbers representing hours, minutes, and seconds. The following are valid time examples: 12:35, 1:14:32, 08:15, and 02:50:45. The speech synthesis engine pronounces:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />As "The train departs at four A M." |

**Usage**

The `say-as` element may contain only text.

**Example**

The speech synthesis engine speaks the following example as "Your first request was for one room on October nineteenth twenty ten with early arrival at twelve thirty five PM."
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## Add recorded audio

`audio` is an optional element that allows you to insert MP3 audio into an SSML document. The body of the audio element may contain plain text or SSML markup that's spoken if the audio file is unavailable or unplayable. Additionally, the `audio` element can contain text and the following elements: `audio`, `break`, `p`, `s`, `phoneme`, `prosody`, `say-as`, and `sub`.

Any audio included in the SSML document must meet these requirements:

* The MP3 must be hosted on an Internet-accessible HTTPS endpoint. HTTPS is required, and the domain hosting the MP3 file must present a valid, trusted TLS/SSL certificate.
* The MP3 must be a valid MP3 file (MPEG v2).
* The bit rate must be 48 kbps.
* The sample rate must be 16,000 Hz.
* The combined total time for all text and audio files in a single response cannot exceed ninety (90) seconds.
* The MP3 must not contain any customer-specific or other sensitive information.

**Syntax**

```xml
<audio src="string"/></audio>
```

**Attributes**

| Attribute | Description | Required / Optional |
|-----------|-------------|---------------------|
| `src` | Specifies the location/URL of the audio file. | Required if using the audio element in your SSML document. |

**Example**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## Add background audio

The `mstts:backgroundaudio` element allows you to add background audio to your SSML documents (or mix an audio file with text-to-speech). With `mstts:backgroundaudio` you can loop an audio file in the background, fade in at the beginning of text-to-speech, and fade out at the end of text-to-speech.

If the background audio provided is shorter than the text-to-speech or the fade out, it will loop. If it is longer than the text-to-speech, it will stop when the fade out has finished.

Only one background audio file is allowed per SSML document. However, you can intersperse `audio` tags within the `voice` element to add additional audio to your SSML document.

**Syntax**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attributes**

| Attribute | Description | Required / Optional |
|-----------|-------------|---------------------|
| `src` | Specifies the location/URL of the background audio file. | Required if using background audio in your SSML document. |
| `volume` | Specifies the volume of the background audio file. **Accepted values**: `0` to `100` inclusive. The default value is `1`. | Optional |
| `fadein` | Specifies the duration of the background audio "fade in" as milliseconds. The default value is `0`, which is the equivalent to no fade in. **Accepted values**: `0` to `10000` inclusive.  | Optional |
| `fadeout` | Specifies the duration of the background audio fade out in milliseconds. The default value is `0`, which is the equivalent to no fade out. **Accepted values**: `0` to `10000` inclusive.  | Optional |

**Example**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## Next steps

* [Language support: voices, locales, languages](language-support.md)
