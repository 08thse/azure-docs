---
title: Multilingual and emoji support in Language Services
titleSuffix: Azure Cognitive Services
description: Learn about offsets caused by multilingual and emoji encodings in Language Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/20/2021
ms.author: aahi
---

# Multilingual and emoji support in Language Services

Multilingual and emoji support has led to Unicode encodings that use more than one [code point](https://wikipedia.org/wiki/Code_point) to represent a single displayed character, called a grapheme. For example, emojis like 🌷 and 👍 may use several characters to compose the shape with additional characters for visual attributes, such as skin tone. Similarly, the Hindi word `अनुच्छेद` is encoded as five letters and three combining marks.

Because of the different lengths of possible multilingual and emoji encodings, Language Services may return offsets in the response.

## Offsets in the API response. 

Whenever offsets are returned the API response, remember:

* Elements in the response may be specific to the endpoint that was called. 
* HTTP POST/GET payloads are encoded in [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), which may or may not be the default character encoding on your client-side compiler or operating system.
* Offsets refer to grapheme counts based on the [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) standard, not character counts.

## Extracting substrings from text with offsets

Offsets can cause problems when using character-based substring methods, for example the .NET [substring()](/dotnet/api/system.string.substring) method. One problem is that an offset may cause a substring method to end in the middle of a multi-character grapheme encoding instead of the end.

In .NET, consider using the [StringInfo](/dotnet/api/system.globalization.stringinfo) class, which enables you to work with a string as a series of textual elements, rather than individual character objects. You can also look for grapheme splitter libraries in your preferred software environment. 

The Language Services returns these textual elements as well, for convenience.

 endpoints that return an offset will support the `stringIndexType` parameter. This parameter adjusts the `offset` and `length` attributes in the API output to match the requested string iteration scheme. Currently, we support three types:

1. `textElement_v8` (default): iterates over graphemes as defined by the [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) standard
2. `unicodeCodePoint`: iterates over [Unicode Code Points](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), the default scheme for Python 3
3. `utf16CodeUnit`: iterates over [UTF-16 Code Units](https://unicode.org/faq/utf_bom.html#UTF16), the default scheme for JavaScript, Java, and .NET

If the `stringIndexType` requested matches the programming environment of choice, substring extraction can be done using standard substring or slice methods. 

## See also

* [Language Services overview](../overview.md)
