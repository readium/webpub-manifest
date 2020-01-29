# Digital Visual Narratives Profile

**Editors:**

* Hadrien Gardeur ([De Marque](http://www.demarque.com))
* Laurent Le Meur ([EDRLab](https://www.edrlab.org))

**Participate:**

* [GitHub readium/webpub-manifest](https://github.com/readium/webpub-manifest)
* [File an issue](https://github.com/readium/webpub-manifest/issues)

## Example

```json
{
  "@context": "http://readium.org/webpub-manifest/context.jsonld",
  
  "metadata": {
    "identifier": "urn:isbn:9782203001152",
    "title": "Objectif Lune",
    "author": "Hergé",
    "language": "fr",
    "publisher": "Casterman",
    "published": "1953-12-30",
    "modified": "2018-12-10T18:21:18Z",
    "numberOfPages": 62,
    "readingProgression": "ltr",
    "belongsTo": {
      "series": {
        "name": "Les Aventures de Tintin",
        "position": 16
      }
    }
  },

  "links": [
    {"rel": "self", "href": "http://example.org/manifest.json", "type": "application/divina+json"}
  ],

  "readingOrder": [
    {
      "rel": "cover",
      "href": "http://example.org/cover.jpg", 
      "type": "image/jpeg",
      "properties": { "page": "center" }
    }, 
    {
      "href": "http://example.org/page1.jpg", 
      "type": "image/jpeg",
      "properties": { "page": "left" }
    }, 
    {
      "href": "http://example.org/page2.jpg", 
      "type": "image/jpeg",
      "properties": { "page": "right" }
    }
  ]
}
```

## 1. Introduction

The goal of this document is to provide a profile dedicated to visual narratives for the [Readium Web Publication Manifest](https://readium.org/webpub-manifest).

This profile relies on:

* the use of [presentation hints](./presentation.md) for specifying display constraints, 
* the definition of a new collection type for implementing [guided navigation](#4-guided-navigation),
* the definition of new properties of Link Objects for implementing [transitions](#5-transitions). 

While the Digital Visual Narrative Manifest is technically a profile of the Readium Web Publication Manifest, it has its own media type in order to maximize compatibilty with dedicated apps: `application/divina+json`.

See [DiViNa guidelines](../guidelines/divina-guidelines.md) for more details on how User Agents should implement this specification.


## 2. Listing Resources

A visual narrative is divided into one or more images, which are all listed in the `readingOrder` of the manifest.

In addition to the normal requirements of a `readingOrder`, all Link Objects have the following additional requirements:
 
 - they <strong class="rfc">must</strong> point strictly to bitmap images

In addition, all Link Objects <strong class="rfc">should</strong> include `width` and `height` to indicate the dimensions of each resource.

## 3. Alternate Resources

In order to provide multiple variants of the same resource, Link Objects in the `readingOrder` <strong class="rfc">may</strong> rely on the `alternate` key.

All Link Objects present in the `alternate` array:

- <strong class="rfc">must</strong> indicate their media-type using `type`
- <strong class="rfc">should</strong> indicate their dimensions using `height` and `width`
- <strong class="rfc">may</strong> target a different language using `language`

*Example 1: A resource available in JPEG and WebP*

```json
{
  "href": "http://example.org/page1.jpeg", 
  "type": "image/jpeg", 
  "alternate": [
    {
      "href": "http://example.org/page1.webp", 
      "type": "image/webp"
    }
  ]
}
```

*Example 2: A resource available in French and Japanese*

```json
{
  "href": "http://example.org/page1.jpeg", 
  "type": "image/jpeg",
  "language": "fr"
  "alternate": [
    {
      "href": "http://example.org/page1-jp.jpeg", 
      "type": "image/jpeg",
      "language": "jp"
    }
  ]
}
```

*Example 3: A resource available in two different resolutions*

```json
{
  "href": "http://example.org/page1.jpeg", 
  "type": "image/jpeg",
  "width": 546,
  "height": 760,
  "alternate": [
    {
      "href": "http://example.org/page1-high.jpeg", 
      "type": "image/jpeg",
      "width": 1092,
      "height": 1520
    }
  ]
}
```

## 4. Guided Navigation

In addition to having [a table of contents](https://readium.org/webpub-manifest/#5-table-of-contents), a visual narrative <strong class="rfc">may</strong> also provide guided navigation where each reference is either:

- pointing directly to a resource (`image1.jpg`)
- or to a fragment of a resource using [Media Fragments](https://www.w3.org/TR/media-frags) (`image1.jpg#xywh=160,120,320,240`)

This document introduces a new collection role to fulfill that goal:

| Role  | Definition | Compact Collection? | Required? |
| ----- | ---------- | ------------------- | --------- |
| `guided` | Identifies a collection containing guided navigation into a publication. | Yes  | No  |

To avoid duplicating content between `readingOrder` and `guided`, Link Objects referenced in `guided` <strong class="rfc">must</strong> only contain `href` and `title`.

This current draft does not cover guided navigation over alternate versions of each image resource.

*Example 4: Guided navigation with full page displayed before panels*

```json
"guided": [
  {
    "href": "http://example.org/page1.jpeg",
    "title": "Page 1"
  },
  {
    "href": "http://example.org/page1.jpeg#xywh=0,0,300,200",
    "title": "Panel 1"
  },
  {
    "href": "http://example.org/page1.jpeg#xywh=300,200,310,200",
    "title": "Panel 2"
  }
]
```

## 5. Transitions

In the reading order of a visual narrative, Link Objects <strong class="rfc">may</strong> contain the following additional properties: 

| Key   | Semantics | Type     | Values    | 
| ----- | --------- | -------- | --------- | 
| [transitionForward](#transitionForward-transitionBackward) | Describes the transition to be applied when moving FROM the previous resource TO current resource in reading order  | Transition Object | See [Transition Object](#the-transition-object)  | 
| [transitionBackward](#transitionForward-transitionBackward) | Describes the transition to be applied when moving FROM the current resource TO the previous resource in reading order  | Transition Object | See [Transition Object](#the-transition-object)  | 

### transitionForward, transitionBackward

Keep in mind that a forward transition is placed on the TARGET resource of the transition.

### The Transition Object

This specification defines the following keys for this JSON object:

| Key  | Definition | Format | Required? |
| ---- | -----------| -------| ----------|
| [type](#type)  | Type of transition  | `cut`, `dissolve`, `slide-in`, `slide-out`, `push`, `animation` | Yes |
| [direction](#direction)  | Direction of a slide-in, slide-out or push transition  | `ltr`, `rtl`, `ttb`, `btt` | Yes if the type is slide-in, slide-out or push. |
| [sequence](#sequence)  | Sequence of images which create an animation | Array of URIs | No |
| [file](#file)  | Video file which creates an animation | URI | No |
| [duration](#duration)  | The duration of the transition in milliseconds | Integer | No |


#### type

| Value  | Definition | 
| ---- | -----------| 
| `cut` | the new resource immediately replaces the current one; this transition does not need a duration and is only useful when continuous=true, as a way to specify an explicit cut (“page change”) between two resources. | 
| `dissolve` |  the new resource appears above the current one, with opacity increasing from 0 to 1. | 
| `slide-in` | the new resource appears above the current one, and moves into the viewport to cover it. | 
| `slide-out` | the next resource is placed below the current one, which moves out of the viewport. | 
| `push` |  the next resource moves into the viewport while the current one moves out of it, revealing the next one below. | 
| `animation` | the next resource appears after playing a sequence of images or a video. |

If the transition type is `animation`, either a `file` or a `sequence` property <strong class="rfc">must</strong> be specified.

#### direction

| Value  | Definition | 
| ---- | -----------| 
| `ltr` | the new image comes from the left (whether the transition is forward or backward). | 
| `rtl` | the new image comes from the right. | 
| `ttb` | the new image comes from the top . | 
| `ltr` | the new image comes from the bottom. | 

Note: Usually, if the reading progression is ltr, forward transitions will be rtl. Also, the reverse of an rtl slide-in is an ltr slide-out.

#### sequence

Only used when the type is `animation`, the value of the `sequence` property is an array of Link Objects pointing to bitmap images displayed before the next resource appears.

Each image in the array is a frame displayed for a slice of `duration` divided by the number of images of the sequence.

#### file

Only used when the type is `animation`, the value of the `file` property is a Link Object pointing to a video to be played before the next resource appears.

#### duration

Duration (in ms) can apply to any type of transition. 

## 6. Packaging

In order to facilitate distribution, both manifest and images can also be distributed using a package based on [the requirements expressed for the Readium Web Publication Manifest](https://readium.org/webpub-manifest#9-package).

To maximize compatibility with dedicated apps, the package for this profile has its own file extension and media-type:

- its file extension <strong class="rfc">must</strong> be `.divina`
- its media type <strong class="rfc">must</strong> be `application/divina+zip`

As an alternative, the manifest can also be added to an EPUB ([as defined in the core specification](https://readium.org/webpub-manifest/#9-package)) or a CBZ file at the same well-known location (`manifest.json` at the root of the package).



## Appendix A. Compliance Levels

### Level 0

* Support for the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) with bitmap images in `readingOrder`
* Support for [presentation hints](presentation.md)
* Support for [alternate resources](#3-alternate-resources)


### Level 1

* Support for [guided navigation](#4-guided-navigation)
* Support for [transitions](#5-transitions)

### Level 2

* TBD

## Appendix B. Examples

*Example 5: A manga is a DiViNa where images are presented sequentially from right-to-left with a discontinuity between images that are not in the same spread*


```json
"metadata": {
  "title": "Manga",
  "identifier": "https://example.com/manga",
  "readingProgression": "rtl",
  "presentation": {
    "fit": "contain",
    "spread": "landscape"
  }
},
"readingOrder": [
  {
    "rel": "cover",
    "href": "cover.jpg", 
    "type": "image/jpeg",
    "properties": { "page": "center" }
  }, 
  {
    "href": "page1.jpg", 
    "type": "image/jpeg",
    "properties": { "page": "right" }
  }, 
  {
    "href": "page2.jpg", 
    "type": "image/jpeg",
    "properties": { "page": "left" }
  }
]
```

*Example 6: A webtoon is a DiViNa where images are scrolled in a single continuous strip of content*


```json
"metadata": {
  "title": "Webtoon",
  "identifier": "https://example.com/webtoon",
  "readingProgression": "ttb",
  "presentation": {
    "overflow": "scrolled",
    "fit": "width",
    "continuous": true
  }
},
"readingOrder": [
  {
    "href": "image1.jpg",
    "type": "image/jpeg"
  },
  {
    "href": "image2.jpg",
    "type": "image/jpeg"
  },
  {
    "href": "image3.jpg",
    "type": "image/jpeg"
  }
]
```

*Example 7: This example features transitions. A slide-in btt from image1 to image2 with a backward slide-out ttb from image2 to image1; an image sequence from image2 to image3 with no backward transition; a video from image 3 to image 4 with no backward transition.*

```json
{
  ...,
  "metadata": {
    "readingProgression": "ttb",
    "presentation": {
      "overflow": "scrolled",
      "continuous": true,
      "fit": "width"
    }
  },
  "readingOrder": [
    {
      "href": "./content/image1.png",
      "type": "image/png",
    },
    {
      "href": "./content/image2.png",
      "type": "image/png",
      "properties": {
        "transitionForward": {
          "type": "slide-in",
          "direction": "btt"
        },
        "transitionBackward": {
          "type": "slide-out",
          "direction": "ttb"
        }
  	},
    {
      "href": "./content/image3.png",
      "type": "image/png"
      "properties": {
        "transitionForward": {
          "type": "animation",
          "sequence": [
              {
                "href": "./content/tr3-1.png",
                "type": "image/png",
              },
              {
                "href": "./content/tr3-2.png",
                "type": "image/png",
              },
              {
                "href": "./content/tr3-3.png",
                "type": "image/png",
              },
              {
                "href": "./content/tr3-4.png",
                "type": "image/png",
              },
              {
                "href": "./content/tr3-5.png",
                "type": "image/png",
              }
          ],
          "duration": 500
        }
      }
    },
    {
      "href": "./content/image4.png",
      "type": "image/png",
      "properties": {
        "transitionForward": {
          "type": "animation",
          "file": {
            "href": "./content/tr4.mp4",
            "type": "video/mp4",
          },
          "duration": 1000
        }
      }
    }
  ]
}
```