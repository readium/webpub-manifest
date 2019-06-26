[![Readium Logo](https://readium.org/assets/logos/readium-logo.png)](https://readium.org)

# Digital Visual Narratives Profile

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
      "properties": { "page": "right" }
    }, 
    {
      "href": "http://example.org/page2.jpg", 
      "type": "image/jpeg",
      "properties": { "page": "left" }
    }, 
    {
      "href": "http://example.org/page3.jpg", 
      "type": "image/jpeg",
      "properties": { "page": "right" }
    }
  ]
}
```

## 1. Introduction

The goal of this document is to provide a profile dedicated to visual narratives for the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) that will cover the following requirements:

- list the different components of a visual narrative
- support multiple resolutions, image formats and/or languages for each visual element of a narrative
- provide guided navigation (also called panel by panel navigation in the case of comics)

While the Digital Visual Narrative Manifest is technically a profile of the Readium Web Publication Manifest, it has its own media type in order to maximize compatibilty with dedicated apps: `application/divina+json`.


## 2. Listing Resources

A visual narratived is divided into one or more images, which are all listed in the `readingOrder` of the manifest.

In addition to the normal requirements of a `readingOrder`, all Link Objects have the following additional requirements:
 
 - they <span class="rfc">must</span> point strictly to bitmap images

In addition, all Link Objects <span class="rfc">should</span> include `width` and `height` to indicate the dimensions of each resource

## 3. Alternate Resources

In order to provide multiple variants of the same resource, Link Objects in the `readingOrder` <span class="rfc">may</span> rely on the `alternate` key.

All Link Objects present in the `alternate` array:

- <span class="rfc">must</span> indicate their media-type using `type`
- <span class="rfc">should</span> indicate their dimensions using `height` and `width`
- <span class="rfc">may</span> target a different language using `language`

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

In addition to having [a table of contents](https://readium.org/webpub-manifest/#5-table-of-contents), a visual narrative <span class="rfc">may</span> also provide guided navigation where each reference is either:

- pointing directly to a resource (`image1.jpg`)
- or to a fragment of a resource using [Media Fragments](https://www.w3.org/TR/media-frags) (`image1.jpg#xywh=160,120,320,240`)

This document introduces a new collection role to fulfill that goal:

| Role  | Definition | Compact Collection? | Required? |
| ----- | ---------- | ------------------- | --------- |
| `guided` | Identifies a collection containing guided navigation into a publication. | Yes  | No  |

To avoid duplicating content between `readingOrder` and `guided`, Link Objects referenced in `guided` <span class="rfc">should</span> only contain `href`, `title` and `children`.

This current draft does not cover guided navigation over alternate versions of each image resource.

*Example 4: Guided navigation in a single page*

```
"guided": [
{
  "href": "http://example.org/page1.jpeg",
  "title": "Page 1",
  "children": [
    {
      "href": "http://example.org/page1.jpeg#xywh=0,0,300,200",
      "title": "Panel 1"
    },
    {
      "href": "http://example.org/page1.jpeg#xywh=300,200,310,200",
      "title": "Panel 2"
    }
  ]
}
]
```

## 5. Package

In order to facilitate distribution, both manifest and images can also be distributed using a package based on [the requirements expressed for the Readium Web Publication Manifest](https://readium.org/webpub-manifest#8-package).

To maximize compatibility with comics apps, the package for this profile has its own file extension and media-type:

- its file extension <span class="rfc">must</span> be `.divina`
- its media type <span class="rfc">must</span> be `application/divina+zip`

As an alternative, the manifest can also be added to a CBZ file at the same well-known location.

## Appendix A. Compliance Levels

### Level 0

* Support for the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) with bitmap images in `readingOrder`
* Support for [alternate resources](#3-alternate-resources)
* Support for the rendition extension (TBD)

### Level 1

* Support for [guided navigation](#4-guided-navigation)

### Level 2

* TBD

## Appendix B. Webtoons

*This section is non-normative.*

> **Note:** We're currently missing the ability to indicate how an image should fit on the screen. The current default is to fit both dimensions on screen, which would not work well with webtoons. In this non-normative appendix, we're currently using `fit` for this use case.

Webtoons are probably the most successful form of digital native visual narrative. Originally from South Korea, they're now becoming popular in Japan and France as well and should be covered by this profile.

We could either consider that a webtoon is:

- a single scrollable image, that should fit the width of the viewport
- multiple images that can be continuously scrolled

With the first approach, the `readingOrder` contains a single image:

```
"readingOrder": [
  {
    "href": "long-image.jpg",
    "type": "image/jpeg",
    "properties": {
      "fit": "width",
      "overflow": "scrolled"
    }
  }
]
```

A second approach is based on using multiple images, which could be better for performance on large webtoons.

In this case, a webtoon becomes a list of images that should be presented in a continuous scroll (with no gap or margin between images):

```
"metadata": {
  "rendition": {
    "overflow": "scrolled-continuous",
    "fit": "width"
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

<style>
.rfc {
    color: #d55;
    font-variant: small-caps;
    font-style: normal;
    font-weight: normal;
}
</style>