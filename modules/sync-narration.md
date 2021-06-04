# Synchronized Narration Module

**Editors:**

* Laurent Le Meur ([EDRLab](https://www.edrlab.org))

## Introduction

The Synchronized Narration Module defines how the content of a given textual resource can be synchronized with the content of an alternative audio resource. 

While the main use of this Module is an audio narration synchonized with text highlights in a textual publication, it may also be used "in reverse mode" for enhancing an audiobook experience with textual subtitles.

## Status of this document

This is a draft document from the Readium community. It is currently implemented in Readium Desktop.

A useful evolution would be to extend this structure with a proper support for still and moving images. A textual publication could then be enhanced with the apparition of small illustrations or sign-language videos explaining words or utterances, upon manual selection of those textual segments. This feature would be very useful for cognitive disabled people, but also for children or people learning a new language. Such feature also has the advantage of being 100% descriptive (no javascript involved in the publication).


## Structure of a Synchronized Narration document

The Synchronized Narration object is defined as:

| Key  | Definition | Format | Required? |
| ---- | -----------| -------| ----------|
| `textRef`   |  The absolute of relative URL of the textual resource which participates to the synchronized narration | URL | At the top level only |
| `audioRef`  |  The absolute of relative URL of the audio resource which participates to the synchronized narration | URL | At the top level only |
| `narration` | A recursive array of synchronization objects | Array of Synchronization Item or Sub Narration | Yes |


The Sub Narration object is defined as:

| Key  | Definition | Format | Required? |
| ---- | -----------| -------| ----------|
| `role`      | Semantic information relative to the role of the object in the publication | [EPUB Structural Semantics](#references) | No |
| `narration` | A recursive array of synchronization objects | Array of Synchronization Item or Sub Narration | Yes |


The Synchronization Item object is defined as:

| Key  | Definition | Format | Required? |
| ---- | -----------| -------| ----------|
| `text`  | The fragment identifier of the textual fragment which participates to the synchronized narration | URI Fragment Identifier | Yes |
| `audio` | The fragment identifier of the audio fragment which participates to the synchronized narration | Media Fragment URI | Yes |

**Notes:**

Structural semantics offer a way to selectively filter out content based on semantics ("skippability" in the DAISY world)

The recursive structure offers a way to jump out of complex structures and back into the reading flow ("escapability" in the DAISY world).

In case we decide to extend the structure to image and video, using `image` and `video` would be consistent with the [latest work of the W3C CG](https://w3c.github.io/sync-media-pub/sync-media.html). 

## Example

```json
{
  "textRef": "/text/chapter1.html",
  "audioRef": "/audio/chapter1.mp3",
  "narration": [
    {
      "text": "#id1",
      "audio": "#t=0.0,1.2"
    },
    {
      "text": "#id2",
      "audio": "#t=1.2,3.4"
    },
    {
      "role": "footnote",
      "text": "#id3",
      "audio": "#t=3.4,5.6"
    },
    {
      "role": "aside",
      "narration": [
        {
          "text": "#id4",
          "audio": "#t=5.6,7.8"
        },
        {
          "text": "#id5",
          "audio": "#t=7.8,9.1"
        }
      ]
    },
    {
      "text": "#id6",
      "audio": "#t=9.1,10.2"
    }
  ]
}
```

## Mime Type and file extension

This specification introduces a dedicated media type value to identify a Synchronized Narration document: `application/vnd.syncnarr+json`.

When saved as a file, a Synchronized Narration document <strong class="rfc">should</strong> have the following extension: `.json`. 

## Declaring Synchronized Narration documents in a Manifest

Each Synchronized Narration document used in a publication <strong class="rfc">must</strong> be declared in the Readium Webpub Manifest as an `alternate` resource with the proper media type. 

The duration of the entire audio narration <strong class="rfc">may</strong> also be declared in the alternate item. 

It is not an error if only some items in the reading order have a Synchronized Narration document attached.

**Example** 

```json
{
  "...": "...",  
  "readingOrder": [{
      "href": "OPS/c001.xhtml", 
      "type": "application/xhtml+xml", 
      "title": "Chapter 1",
      "alternate": [{
          "href": "sync/c001.json",
          "type": "application/vnd.syncnarr+json",
          "duration": 850.5
        }
      ]
    }, 
    { 
      "href": "OPS/c002.xhtml", 
      "type": "application/xhtml+xml", 
      "title": "Chapter 2",
      "alternate": [{
          "href": "sync/c002.json",
          "type": "application/vnd.syncnarr+json",
          "duration": 1001
        }
      ]
    }
  ],
  "resources": [
    { 
      "href": "/audio/c001.mp3", 
      "type": "audio/mp3"
    },
    { 
      "href": "/audio/c002.mp3", 
      "type": "audio/mp3"
    }
  ]
}
```

## Associating Style Information

The manifest <strong class="rfc">may</strong> contain metadata indicating how textual content should be highlighted. 

A `media-overlays` property <strong class="rfc">may</strong> be added to the set of metadata included in the manifest. This object is defined as:

| Key  | Definition | Format | Required? |
| ---- | -----------| -------| ----------|
| `active-class`          |  The author-defined CSS class name to apply to the selected textual segment. | string | No |
| `playback-active-class` |  The author-defined CSS class name to apply to the selected textual segment when playback is active. | string | No |

**Example** 

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  "metadata": {
    "@type": "http://schema.org/Book",
    "duration": 1403.5,
    "media-overlay": {
      "active-class": "-epub-media-overlay-active"
    },
    "...":"..."
  }
}
```

## Processing a Synchronized Narration object

**Non-normative**

If one or more Synchronized Narration objects are detected in a Manifest, the reading system should present to the user a way to activate / deactivate the synchronized narration.

At the time it is processing a Synchronized Narration object, the reading system should check that the `textRef` and `audioRef` properties reference existing resources in the manifest and that their media type is correct. 

In case of error in the processing of a Synchronized Narration object, the object should be skipped. 

## References

### Normative References

- [URI fragment identfier](https://www.ietf.org/rfc/rfc3986.txt), IETF, 2005.
- [Media Fragments URI 1.0](https://www.w3.org/TR/media-frags/), W3C Recommendation, 2012.
- [EPUB 3 Structural Semantics Vocabulary](https://idpf.github.io/epub-vocabs/structure/), IDPF, 2019

### Non normative Reference

- [W3C Synchronized Narration](https://w3c.github.io/sync-media-pub/archived/synchronized-narration.html), W3C, archived, 2019. 

This specification was discussed in 2019 by the W3C Synchronized Media for Publications Community Group - which included EDRLab - and was used as an inspiration for our document; but it was then archived by the W3C community group and replaced by an XML based proposal which does not fit the needs of the Readium Architecture. 
