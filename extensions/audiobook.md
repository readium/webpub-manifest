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

While the Audiobook Manifest is technically a profile of the Readium Web Publication Manifest, it has its own media type in order to maximize compatibilty with audio apps: `application/audiobook+json`.

## 1. Metadata

The core metadata for the audiobook manifest are based on [the default context for the Readium Web Publication Manifest](https://readium.org/webpub-manifest/contexts/default/) with the following additional requirements:

- it <strong class="rfc">must</strong> include a `@type` element that identifies the manifest as an audiobook: `http://schema.org/Audiobook`
- it <strong class="rfc">must</strong> include a `duration` element that provides the total duration of the audiobook in seconds

The `duration` of an audiobook as expressed in `metadata` is purely a hint and <strong class="rfc">must not</strong> be used by the User Agent for anything else than informing the user.

## 2. Listing Audio Resources

An audiobook is divided into one or more audio resources, which are all listed in the `readingOrder` of the manifest.

In addition to the normal requirements of a `readingOrder`, all Link Objects have the following additional requirements:
 
 - they <strong class="rfc">must</strong> point strictly to audio resources
 - they <strong class="rfc">must</strong> include a `duration` term that provides the duration in seconds of each individual audio resource

In addition, all Link Objects <strong class="rfc">should</strong> also include the `bitrate` whenever possible.

## 3. Alternate Audio Resources

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

## 4. Package

In order to facilitate distribution, both manifest and audio files can also be distributed using a package based on [the requirements expressed for the Readium Web Publication Manifest](https://readium.org/webpub-manifest#9-package).

To maximize compatibility with audio-only apps, the package for an audiobook profile has its own file extension and media-type:

- its file extension <strong class="rfc">must</strong> be `.audiobook`
- its media type <strong class="rfc">must</strong> be `application/audiobook+zip`

## Appendix A - Examples

A full example based on [the LibriVox edition of Flatland](https://librivox.org/flatland-a-romance-of-many-dimensions-by-edwin-abbott-abbott/) is available at: [https://readium.org/webpub-manifest/examples/Flatland/manifest.json](https://readium.org/webpub-manifest/examples/Flatland/manifest.json)

Over 10,000+ audiobooks are also available in this format through [the Internet Archive OPDS Catalog](https://bookserver.archive.org/).

## Appendix B - Demo

[A demo of the Flatland example is also available](https://player.cantookaudio.com/aHR0cHM6Ly9yZWFkaXVtLm9yZy93ZWJwdWItbWFuaWZlc3QvZXhhbXBsZXMvRmxhdGxhbmQvbWFuaWZlc3QuanNvbg==) through a Web App developed by [De Marque](https://www.demarque.com/). 
