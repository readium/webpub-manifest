# Audiobook Extension

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
    {"rel": "cover", "href": "http://example.org/cover.jpeg", "type": "image/jpeg", "height": 300, "width": 300},
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
  ]
}
```


## Introduction

The goal of this document is to provide an audiobook profile for the [Readium Web Publication Manifest](https://github.com/readium/webpub-manifest) that will cover the following requirements:

- provide metadata
- list the different components of an audiobook
- support multiple audio formats and means of accessing an audiobook (streaming or downloads)

While the Audiobook Manifest is technically a profile of the Readium Web Publication Manifest, it has its own media type and file extension in order to maximize compatibilty with audio apps:

- its media type is `application/audiobook+json`
- its file extension is `.audiobook-manifest`

## Metadata

The core metadata for the audiobook manifest are based on [the default context for the Readium Web Publication Manifest](https://github.com/readium/webpub-manifest/tree/master/contexts/default) with the following additional requirements:

- it must include a `@type` element that identifies the manifest as an audiobook: `http://schema.org/Audiobook`
- it must include a `duration` element that provides the total duration of the audiobook


## Listing Audio Files

An audiobook is divided into one or more audio files, which are all listed in the `readingOrder` of the manifest, in reading order.

In addition to the normal requirements of a `readingOrder`, all link objects have the following additional requirements:
 
 - all link objects must point strictly to audio files
 - every link object must include a `duration` that provides the duration of each individual audio file

In addition, all link objects should also include the `bitrate` whenever possible.

## Package

In order to facilitate distribution, both manifest and audio files can also be distributed using a package.

The container also has the following properties:

- its file extension must be `.audiobook`
- its media type must be `application/audiobook+zip`

When an audiobook is distributed in a container (instead of simply as a manifest), the manifest has the following additional restrictions:

- the `readingOrder` must strictly reference audio files that are present in the container
- to reference such files, all URIs in the `readingOrder` must be relative to the manifest (at the root of the container)
