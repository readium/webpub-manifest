# Audiobook Profile

**Editors:**

* Hadrien Gardeur ([De Marque](http://www.demarque.com))

**Participate:**

* [GitHub readium/webpub-manifest](https://github.com/readium/webpub-manifest)
* [File an issue](https://github.com/readium/webpub-manifest/issues)

## Example

```json
{
  "@context": "http://readium.org/webpub-manifest/context.jsonld",
  
  "metadata": {
    "@type": "http://schema.org/Audiobook",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/audiobook",
    "identifier": "urn:isbn:9780000000001",
    "title": "Moby-Dick",
    "author": "Herman Melville",
    "narrator": ["Joe Speaker", "Lucy Narrator"],
    "language": "en",
    "publisher": "Whale Publishing Ltd.",
    "published": "2016-02-01",
    "modified": "2016-02-18T10:32:18Z",
    "duration": 4320
  },

  "links": [
    {"rel": "self", "href": "http://example.org/manifest.json", "type": "application/audiobook+json"},
  ],

  "readingOrder": [
    {
      "href": "http://example.org/part1.mp3", 
      "type": "audio/mpeg", 
      "bitrate": 128, 
      "duration": 1980, 
      "title": "Part 1"
    }, 
    {
      "href": "http://example.org/part2.mp3", 
      "type": "audio/mpeg", 
      "bitrate": 128, 
      "duration": 1200, 
      "title": "Part 2"
    }, 
    {
      "href": "http://example.org/part3.mp3", 
      "type": "audio/mpeg", 
      "bitrate": 128, 
      "duration": 1140, 
      "title": "Part 3"
    }
  ],
  
  "resources": [
    {"rel": "cover", "href": "http://example.org/cover.jpeg", "type": "image/jpeg", "height": 300, "width": 300}
  ]
}
```

## Introduction

The goal of this specification is to provide a profile of the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) specification dedicated to audiobooks.

This profile relies on:

* a specific [media type](#1-media-type)
* a declaration of [conformance with this Profile](#2-declaring-conformance-with-the-audiobook-profile),
* some [required metadata](#3-required-metadata),
* some [restrictions on the resources of the reading order](#4-restrictions-on-the-resources-of-the-reading-order),
* some [restrictions on the use of alternate resources](#5-alternate-resources)
* specific [details on packaging](#6-packaging)
* the use of [presentation hints](../modules/presentation.md) for specifying display constraints, 
* the use of [transitions](../modules/transitions.md) to manage transitions between resources of the reading order.

## 1. Media type

While the Audiobook Manifest is technically a profile of the Readium Web Publication Manifest, it has its own media type in order to maximize compatibilty with audio apps: `application/audiobook+json`.

## 2. Declaring conformance with the Audiobook Profile

To declare that it conforms to the Audiobook Profile, a Readium Web Publication Manifest <strong class="rfc">must</strong> include a `conformsTo` key in its `metadata` section, with `https://readium.org/webpub-manifest/profiles/audiobook` as value.

## 3. Required Metadata

An Audiobook Manifest <strong class="rfc">must</strong> include a `duration` element that provides the total duration of the audiobook. It is expressed as a number of seconds.

This informative duration is purely a hint and <strong class="rfc">must not</strong> be used by the User Agent for anything else than informing the user.

## 4. Restrictions on resources in the reading order

An audiobook is divided into one or more audio resources, which are all listed in the `readingOrder` of the manifest.

In addition to the normal requirements of a `readingOrder`, all Link Objects have the following additional requirements:
 
 - they <strong class="rfc">must</strong> point strictly to audio resources, with no fragment identifier. 
 - they <strong class="rfc">must</strong> include a `duration` term that provides the duration in seconds of each individual audio resource

In addition, all Link Objects <strong class="rfc">should</strong> also include the `bitrate` (in `kbps`) whenever possible.

## 5. Alternate Resources

To support multiple variants of the same audio content (using a different format or bitrate for instance), Link Objects in the `readingOrder` <strong class="rfc">may</strong> rely on the `alternate` key:

All Link Objects present in the `alternate` array:

- <strong class="rfc">must</strong> indicate their media-type using `type`
- <strong class="rfc">should</strong> indicate their bitrate using `bitrate`
- <strong class="rfc">must</strong> reference audio resources of the same duration as the top-level Link Object
- <strong class="rfc">must not</strong> include the following keys: `title`, `duration` or `templated`

*Example 1: A resource available in mp3 and opus*

```json
{
  "href": "http://example.org/part1.mp3", 
  "type": "audio/mpeg", 
  "bitrate": 128, 
  "duration": 1980, 
  "title": "Part 1",
  "alternate": [
    {
      "href": "http://example.org/part1.opus", 
      "type": "audio/ogg", 
      "bitrate": 64
    }
  ]
}
```

## 6. Packaging

An Audiobook publication may be distributed unpackaged on the Web, but it may also be packaged for easy distribution as a single file. To achieve this goal, this specification defines the [Readium Packaging Format (RPF)](../packaging.md).

To maximize compatibility with dedicated apps, such a package has its own file extension and media-type:

- its file extension <strong class="rfc">must</strong> be `.audiobook`
- its media type <strong class="rfc">must</strong> be `application/audiobook+zip`

## Appendix A - Examples

A full example based on [the LibriVox edition of Flatland](https://librivox.org/flatland-a-romance-of-many-dimensions-by-edwin-abbott-abbott/) is available at: [https://readium.org/webpub-manifest/examples/Flatland/manifest.json](https://readium.org/webpub-manifest/examples/Flatland/manifest.json)

Over 10,000+ audiobooks are also available in this format through [the Internet Archive OPDS Catalog](https://bookserver.archive.org/).

## Appendix B - Demo

[A demo of the Flatland example is also available](https://player.cantookaudio.com/aHR0cHM6Ly9yZWFkaXVtLm9yZy93ZWJwdWItbWFuaWZlc3QvZXhhbXBsZXMvRmxhdGxhbmQvbWFuaWZlc3QuanNvbg==) through a Web App developed by [De Marque](https://www.demarque.com/). 
