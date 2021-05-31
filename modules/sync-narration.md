# Synchronized Narration Module

**Editors:**

* Laurent Le Meur ([EDRLab](https://www.edrlab.org)

## Introduction

The Synchronized Narration Module defines how the content of a given textual resource can be synchronized with the content of an alternative audio resource. 

While the main use of this Module is an audio narration synchonized with text highlights in a textual publication, it may also be used "in reverse mode" for enhancing an audiobook experience with textual subtitles.

## Status of this document

This is a draft document from the Readium community. It is currently implemented in Readium Desktop.

An useful evolution would be to extend this structure with a proper support for still and moving images. A textual publication could then be enhanced with the apparition of small illustrations or sign-language videos explaining words or utterances, upon manual selection of those textual segments. This feature would be very useful for cognitive disabled people, but also for children or people learning a new language. Such feature also has the advantage of being 100% descriptive (no javascript involved in the publication).


## Structure of a Synchronized Narration document

The Synchronized Narration object is defined as:

| Key  | Definition | Format | Required? |
| ---- | -----------| -------| ----------|
| `textRef`   |  The absolute of relative URL of the textual resource which participates to the synchronized narration | URL | At the top level only |
| `audioRef`  |  The absolute of relative URL of the audio resource which participates to the synchronized narration | URL | At the top level only |
| `role`      | Semantic information relative to the role of the object in the publication | "aside", "footnote" | No |
| `narration` | A recursive array of synchronization objects | Array of Synchronization Item or Synchronized Narration | Yes |

The Synchronization Item object is defined as:

| Key  | Definition | Format | Required? |
| ---- | -----------| -------| ----------|
| `text`  | The fragment identifier of the textual fragment which participates to the synchronized narration | URI Fragment Identifier | Yes |
| `audio` | The fragment identifier of the audio fragment which participates to the synchronized narration | Media Fragment URI | Yes |

**Notes:**

- The recursive structure offers a way to selectively filter out content based on semantics ("skippability" in the DAISY world) and to jump out of complex structures and back into the reading flow ("escapability" in the DAISY world).
- While the structure allows `textRef`and `audioRef` to be set at every level of a recursive tree of Synchronized Narration objects, these URL are usually defined only at the top level of the document, where they are mandatory. Nevertheless, this structure also allows the creation of a unique Synchronized Narration document structured as an array of Synchronized Narration objects (one per publication resource).

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
      "role": "footnote-ref",
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

When serialized as a file, Synchronized Narration document <strong class="rfc">must</strong> have the following extension: `.sync`. 

## Declaring a Synchronized Narration document in a Manifest

There is usually one Synchronized Narration document per resource in the reading order. 

Each Synchronized Narration document used in a publication <strong class="rfc">must</strong> be declared in the Readium Webpub Manifest as an `alternate` resource with the proper media type. 

**Example** 

```json
{
  "...": "...",  
  "readingOrder": [{
      "href": "/text/c001.html", 
      "type": "text/html", 
      "title": "Chapter 1",
      "alternate": {
        "href": "/sync/c001.sync",
        "type": "application/vnd.syncnarr+json"
      }
    }, 
    { 
      "href": "/text/c002.html", 
      "type": "text/html", 
      "title": "Chapter 2",
      "alternate": {
        "href": "/sync/c002.sync",
        "type": "application/vnd.syncnarr+json"
      }
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

## Processing a Synchronized Narration object

** non-normative **

If a Synchronized Narration object is detected in a Manifest, the reading system should present to the user a way to activate / deactivate the synchronized narration.

At the time a Readium Navigator is processing a Synchronized Narration object, a reading system should check that the `textRef` and `audioRef` properties reference existing resources in the manifest and that their media type is correct. 

In case of error, the Synchronized Narration object should be skipped. 

## References

### Normative References

- [URI fragment identfier](https://www.ietf.org/rfc/rfc3986.txt), IETF, 2005.
- [Media Fragments URI 1.0](https://www.w3.org/TR/media-frags/), W3C Recommendation, 2012.

### Non normative Reference

- [W3C Synchronized Narration](https://w3c.github.io/sync-media-pub/archived/synchronized-narration.html), W3C, archived, 2019. 

This specification was discussed in 2019 by the W3C Synchronized Media for Publications Community Group - which included EDRLab - and was used as an inspiration for our document; but it was then archived by the W3C community group and replaced by an XML based proposal which does not fit the needs of the Readium Architecture. 

