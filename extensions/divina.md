# Digital Visual Narratives Profile

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

While the Digital Visual Narrative Manifest is technically a profile of the Readium Web Publication Manifest, it has its own media type in order to maximize compatibilty with dedicated apps: `application/divina+json`.


## 2. Listing Resources

A visual narrative is divided into one or more images, which are all listed in the `readingOrder` of the manifest.

In addition to the normal requirements of a `readingOrder`, all Link Objects have the following additional requirements:
 
 - they <strong class="rfc">must</strong> point strictly to bitmap images

In addition, all Link Objects <strong class="rfc">should</strong> include `width` and `height` to indicate the dimensions of each resource

## 3. Alternate Resources

In order to provide multiple variants of the same resource, Link Objects in the `readingOrder` <strong class="rfc">may</strong> rely on the `alternate` key.

All Link Objects present in the `alternate` array:

- <strong class="rfc">must</strong> indicate their media-type using `type`
- <strong class="rfc">should</strong> indicate their dimensions using `height` and `width`
- <strong class="rfc">may</strong> target a different language using `language`

*Example 1: A resource available in JPEG and WebP*

```
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

```
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

```
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

```
"guided": [
  {
    "href": "http://example.org/page1.jpeg",
    "title": "Page 1
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

A transition disables any continuity between the current resource and the next, i.e. it locally applies a form of `continuous`=`false`. Therefore, a transition represents the only way of creating “breaks” or “pages” in a webtoon (i.e. when continuous=true). However, do note that:

* This can only be done with a `transitionForward` (if continuous=true, any `transitionBackward` will be discarded, except if it happens to coincide with a `transitionForward`, i.e. if both transitions are properties of the same Link Object).
* The elements making up a “page” in such a paginated webtoon will necessarily be stitched together in the direction of the `readingProgression` (for example, you can’t have 2 images stitched together vertically if `readingProgression` = `ltr`).


### transitionForward, transitionBackward

Keep in mind that a forward transition is placed on the TARGET resource of the transition.

### The Transition Object

This specification defines the following keys for this JSON object:

| Key  | Definition | Format | Required? |
| ---- | -----------| -------| ----------|
| [type](#transition-type)  | Type of transition  | `cut`, `dissolve`, `slide-in`, `slide-out`, `push`, `animation` | Yes |
| [direction](#transition-direction)  | Direction of a slide-in, slide-out or push transition  | `ltr`, `rtl`, `ttb`, `btt` | Yes if the type is slide-in, slide-out or push. |
| [sequence](#transition-sequence)  | Sequence of images which create an animation | Array of URIs | No |
| [file](#transition-file)  | Video file which creates an animation | URI | No |
| [duration](#transition-duration)  | The duration of the transition in milliseconds | Integer | No |


#### Transition Type

| Value  | Definition | 
| ---- | -----------| 
| `cut` | the new resource immediately replaces the current one; this transition does not need a duration and is only useful when continuous=true, as a way to specify an explicit cut (“page change”) between two resources. | 
| `dissolve` |  the new resource appears above the current one, with opacity increasing from 0 to 1. | 
| `slide-in` | the new resource appears above the current one, and moves into the viewport to cover it. | 
| `slide-out` | the next resource is placed below the current one, which moves out of the viewport. | 
| `push` |  the next resource moves into the viewport while the current one moves out of it, revealing the next one below. | 
| `animation` | the next resource appears after playing a sequence of images or a video. |

#### Transition Direction

| Value  | Definition | 
| ---- | -----------| 
| `ltr` | the new image comes from the left (whether the transition is forward or backward). | 
| `rtl` | the new image comes from the right. | 
| `ttb` | the new image comes from the top . | 
| `ltr` | the new image comes from the bottom. | 

Note: Usually, if the reading progression is ltr, forward transitions will be rtl. Also, the reverse of an rtl slide-in is an ltr slide-out.

#### Transition Sequence

When the type is `animation`, the value of the `sequence` property is an array of hrefs pointing to bitmap images displayed before the next resource appears.

Each image in the array is a frame displayed for a slice of `duration` divided by the number of images of the sequence.

In this case `overflow` is forced to `clipped` while `fit` is inherited from the parent resource (i.e. the target resource for a forward transition and the source for a backward one).

#### Transition File

When the type is `animation`, a video to be played before the next resource appears.

In this case `overflow` is forced to `clipped` while `fit` is inherited from the parent resource.

#### Transition Duration

The duration applies to any type of transition. 

In case the transition type is `animation`, a video `file` is defined but no duration is specified, then the video should play entirely (which goes further than saying that the transition duration should be that of the video: if the video lags, it should still reach its end before the next image appears). 

If a duration is specified which is shorter than the video duration, the video is cut before its end. If the duration is longer, the transition is ended after the video has been played entirely. 

Apart from the video case, if no `duration` is specified, the total duration of the transition is chosen by the client application. 


### Recommendations of implementation for transitions

Scrolling should be disabled by the reading application while a transition unfolds.

If a discontinuous gesture is made during a transition, then its effect depends on the relative directions of the two movements:

* If they have the same direction (forward and forward, or backward and backward), the transition should be forced to its end - but no addition page change should be performed.
* If they have contradictory directions, then the transition should be cancelled and the publication brought back to the transition’s start or end, with an additional page change in the direction of the gesture.
..* Example 1: if a forward transition is unfolding and the user taps to go backward, the story will move back to the previous page (possibly playing the corresponding backward transition if there is one for the previous page).
..* Example 2: if a backward snap point jump is unfolding and the user taps forward, the story will jump to the next start point and then either automatically jump to the next one (if there is one) or trigger a page change (if there is none).

*Example 5: Transitions*

This example features a slide-in btt from image1 to image2 with a slide-out ttb from image2 to image1; an image sequence from image2 to image3 with no backward transition; a video from image 3 to image 4 with no backward transition. 

```
{
  ...,
  “metadata”: {
    “readingProgression”: “ttb”,
    “presentation”: {
      "overflow": "scrolled",
      "continuous": "true",
      "fit": "width"
    }
  },
  “readingOrder”: [
    {
      "href": “./content/image1.png”,
      "type": "image/png",
    },
    {
      "href": “./content/image2.png”,
      "type": "image/png",
      “properties”: {
        “transitionForward”: {
          “type”: “slide-in”,
          “direction”: “btt”,
        },
        “transitionBackward”: {
          “type”: “slide-out”,
          “direction”: “ttb”,
        }
  	},
    {
      "href": “./content/image3.png”,
      "type": "image/png"
      “properties”: {
        “transitionForward”: {
          “type”: animation,
          “sequence”: [
              {
                "href": “./content/tr3-1.png”,
                "type": "image/png",
              },
              {
                "href": “./content/tr3-2.png”,
                "type": "image/png",
              },
              {
                "href": “./content/tr3-3.png”,
                "type": "image/png",
              },
              {
                "href": “./content/tr3-4.png”,
                "type": "image/png",
              },
              {
                "href": “./content/tr3-5.png”,
                "type": "image/png",
              }
          ],
          “duration”: 500
        }
      }
    },
    {
      "href": “./content/image4.png”,
      "type": "image/png"
      “properties”: {
        “transitionForward”: {
          “type”: "animation",
          "file": {
            "href": “./content/tr4.mp4”,
            "type": "video/mp4",
          },
          “duration”: 1000
        }
      }
    }
  ]
}
```


## 6. Packaging

In order to facilitate distribution, both manifest and images can also be distributed using a package based on [the requirements expressed for the Readium Web Publication Manifest](https://readium.org/webpub-manifest#9-package).

To maximize compatibility with dedicated apps, the package for this profile has its own file extension and media-type:

- its file extension <strong class="rfc">must</strong> be `.divina`
- its media type <strong class="rfc">must</strong> be `application/divina+zip`

As an alternative, the manifest can also be added to an EPUB ([as defined in the core specification](https://readium.org/webpub-manifest/#9-package)) or a CBZ file at the same well-known location (`manifest.json` at the root of the package).



## Appendix A. Compliance Levels

### Level 0

* Support for the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) with bitmap images in `readingOrder`
* Support for [alternate resources](#3-alternate-resources)
* Support for [presentation hints](presentation.md)

### Level 1

* Support for [guided navigation](#4-guided-navigation)
* Support for [transitions](#5-transitions)

### Level 2

* TBD

## Appendix B. Examples

*Example 6: A manga is a DiViNa where images are presented sequentially from right-to-left with a discontinuity between images that are not in the same spread*


```
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

*Example 7: A webtoon is a DiViNa where images are scrolled in a single continuous strip of content*


```
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