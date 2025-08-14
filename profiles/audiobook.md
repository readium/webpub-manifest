# Audiobook Profile

**Editors:**

* Hadrien Gardeur

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
    {"rel": "self", "href": "http://example.org/manifest.audiobook-manifest", "type": "application/audiobook+json"},
    {"rel": "alternate", "href": "http://example.org/audiobook.m3u", "type": "audio/mpegurl", "bitrate": 64}
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

The goal of this document is to provide an audiobook profile for the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) that will cover the following requirements:

- provide metadata
- list the different components of an audiobook
- support multiple audio formats and means of accessing an audiobook (streaming or downloads)

## 1. Declaring conformance to the Audiobook Profile

In order to declare that it conforms to the Audiobook Profile, a Readium Web Publication Manifest <strong class="rfc">must</strong>:

- include a `conformsTo` property in its `metadata` section, with `https://readium.org/webpub-manifest/profiles/audiobook` as one of its values
- use `application/audiobook+json` as its media type

While the Audiobook Manifest is technically a profile of the Readium Web Publication Manifest, the use of its dedicated media type is recommended to maximize compatibility with applications that may target audiobooks exclusively.

## 2. Listing resources

A visual narrative is divided into one or audio resources, which are all listed in the `readingOrder` of the manifest.

In addition to the normal requirements of a `readingOrder`, all Link Objects have the following additional requirements:
 
  - they <strong class="rfc">must</strong> point strictly to audio resources, with no fragment identifier in their URL
 - they <strong class="rfc">must</strong> include a `duration` that provides the duration in seconds of each individual audio resource

In addition, all Link Objects <strong class="rfc">should</strong> also include the `bitrate` (in `kbps`) whenever possible.

## 3. Metadata

The core metadata for the audiobook manifest are based on [the default context for the Readium Web Publication Manifest](https://readium.org/webpub-manifest/contexts/default/) with an additional requirement:

- it <strong class="rfc">must</strong> include a `duration` element that provides the total duration of the audiobook in seconds

The `duration` of an audiobook as expressed in `metadata` is purely a hint and <strong class="rfc">must not</strong> be used by the User Agent for anything else than informing the user.

In addition to its duration, an audiobook <strong class="rfc">may</strong> indicate that it's an abridged edition with the `abridged` element (a boolean that defaults to `false`).


## 4. Alternate resources

In order to support multiple variants of the same audiobook (using a different format or bitrate for instance), Link Objects in the `readingOrder` <strong class="rfc">may</strong> rely on the `alternate` key:

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
      "bitrate": 32
    }
  ]
}
```

All Link Objects present in the `alternate` array:

- <strong class="rfc">must</strong> indicate their media-type using `type`
- <strong class="rfc">should</strong> indicate their bitrate using `bitrate`
- <strong class="rfc">must</strong> reference audio resources of the same duration as the top-level Link Object
- <strong class="rfc">must not</strong> include the following keys: `title`, `duration` or `templated`

## 5. Packaging

An Audiobook publication may be distributed unpackaged on the Web, but it may also be packaged for easy distribution as a single file. To achieve this goal, this specification defines the [Readium Packaging Format (RPF)](https://readium.org/webpub-manifest/packaging.html).

To maximize compatibility with dedicated apps, such a package has its own file extension and media-type:

- its file extension <strong class="rfc">must</strong> be `.audiobook`
- its media type <strong class="rfc">must</strong> be `application/audiobook+zip`

## Appendix A - Example

A full example based on [the LibriVox edition of Flatland](https://librivox.org/flatland-a-romance-of-many-dimensions-by-edwin-abbott-abbott/) is available at: [https://readium.org/webpub-manifest/examples/Flatland/manifest.json](https://readium.org/webpub-manifest/examples/Flatland/manifest.json)

## Appendix B - Demo

[A demo of the Flatland example is also available](https://player.cantookaudio.com/aHR0cHM6Ly9yZWFkaXVtLm9yZy93ZWJwdWItbWFuaWZlc3QvZXhhbXBsZXMvRmxhdGxhbmQvbWFuaWZlc3QuanNvbg==) through a Web App developed by [De Marque](https://www.demarque.com/). 
