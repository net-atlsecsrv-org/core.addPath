---
title: Text Moderation - Content Moderator
titleSuffix: Azure Cognitive Services
description: Use text moderation for possible unwanted text, personal data, and custom lists of terms.
services: cognitive-services
author: PatrickFarley
manager: nitinme

ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley

---

# Learn text moderation concepts

Use Content Moderator’s machine-assisted text moderation and [human review](Review-Tool-User-Guide/human-in-the-loop.md) capabilities to moderate text content.

You either block, approve or review the content based on your policies and thresholds. Use it to augment human moderation of environments where partners, employees and consumers generate text content. These include chat rooms, discussion boards, chatbots, e-commerce catalogs, and documents. 

The service response includes the following information:

- Profanity: term-based matching with built-in list of profane terms in various languages
- Classification: machine-assisted classification into three categories
- Personal data
- Auto-corrected text
- Original text
- Language

## Profanity

If the API detects any profane terms in any of the [supported languages](Text-Moderation-API-Languages.md), those terms are included in the response. The response also contains their location (`Index`) in the original text. The `ListId` in the following sample JSON refers to terms found in [custom term lists](try-terms-list-api.md) if available.

	"Terms": [
	{
		"Index": 118,
		"OriginalIndex": 118,
		"ListId": 0,
		"Term": "crap"
	}

> [!NOTE]
> For the **language** parameter, assign `eng` or leave it empty to see the machine-assisted **classification** response (preview feature). **This feature supports English only**.
>
> For **profanity terms** detection, use the [ISO 639-3 code](http://www-01.sil.org/iso639-3/codes.asp) of the supported languages listed in this article, or leave it empty.

## Classification

Content Moderator’s machine-assisted **text classification feature** supports **English only**, and helps detect potentially undesired content. The flagged content may be assessed as inappropriate depending on context. It conveys the likelihood of each category and may recommend a human review. The feature uses a trained model to identify possible abusive, derogatory or discriminatory language. This includes slang, abbreviated words, offensive, and intentionally misspelled words for review. 

The following extract in the JSON extract shows an example output:

	"Classification": {
    	"ReviewRecommended": true,
    	"Category1": {
      		"Score": 1.5113095059859916E-06
    		},
    	"Category2": {
      		"Score": 0.12747249007225037
    		},
    	"Category3": {
      		"Score": 0.98799997568130493
    	}
	}

### Explanation

- `Category1` refers to potential presence of language that may be considered sexually explicit or adult in certain situations.
- `Category2` refers to potential presence of language that may be considered sexually suggestive or mature in certain situations.
- `Category3` refers to potential presence of language that may be considered offensive in certain situations.
- `Score` is between 0 and 1. The higher the score, the higher the model is predicting that the category may be applicable. This feature relies on a statistical model rather than manually coded outcomes. We recommend testing with your own content to determine how each category aligns to your requirements.
- `ReviewRecommended` is either true or false depending on the internal score thresholds. Customers should assess whether to use this value or decide on custom thresholds based on their content policies.

## Personal data

The personal data feature detects the potential presence of this information:

- Email address
- US Mailing address
- IP address
- US Phone number
- UK Phone number
- Social Security Number (SSN)

The following example shows a sample response:

```json
"PII":{ 
  "Email":[ 
    { 
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[ 
    { 
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[ 
    { 
      "CountryCode":"US",
      "Text":"4255550111",
      "Index":56
    },
    { 
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":212
    },
    { 
      "CountryCode":"UK",
      "Text":"+123 456 7890",
      "Index":208
    },
    { 
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":228
    },
    { 
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":245
    }
  ],
  "Address":[ 
    { 
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
      "Index":89
    }
  ],
  "SSN":[ 
    { 
      "Text":"999999999",
      "Index":56
    },
    { 
      "Text":"999-99-9999",
      "Index":267
    }
  ]
}
```

## Auto-correction

Suppose the input text is (the ‘lzay’ and 'f0x' are intentional):

	The qu!ck brown f0x jumps over the lzay dog.

If you ask for auto-correction, the response contains the corrected version of the text:

	The quick brown fox jumps over the lazy dog.

## Creating and managing your custom lists of terms

While the default, global list of terms works great for most cases, you may want to screen against terms that are specific to your business needs. For example, you may want to filter out any competitive brand names from posts by users.

> [!NOTE]
> There is a maximum limit of **5 term lists** with each list to **not exceed 10,000 terms**.
>

The following example shows the matching List ID:

	"Terms": [
	{
		"Index": 118,
		"OriginalIndex": 118,
		"ListId": 231.
		"Term": "crap"
	}

The Content Moderator provides a [Term List API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) with operations for managing custom term lists. Start with the [Term Lists API Console](try-terms-list-api.md) and use the REST API code samples. Also check out the [Term Lists .NET quickstart](term-lists-quickstart-dotnet.md) if you are familiar with Visual Studio and C#.

## Next steps

Test drive the [Text moderation API console](try-text-api.md) and use the REST API code samples. Also check out the Text moderation section of the [.NET SDK quickstart](dotnet-sdk-quickstart.md) if you're familiar with Visual Studio and C#.
