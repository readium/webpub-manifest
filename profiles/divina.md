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
    "title": "Objectif Lune",
    "identifier": "urn:isbn:9782203001152",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/divina",
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

## Introduction

The goal of this specification is to provide a profile dedicated to visual narratives for the [Readium Web Publication Manifest](https://readium.org/webpub-manifest).

This profile relies on:

* the use of [presentation hints](../modules/presentation.md) for specifying display constraints, 
* the definition of a new collection type for implementing [guided navigation](#4-guided-navigation),
* the [transitions module](../modules/transitions.md) to manage transitions between resources of the reading order.

While the Digital Visual Narrative Manifest is technically a profile of the Readium Web Publication Manifest, it has its own media type in order to maximize compatibilty with dedicated apps: `application/divina+json`.


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
  "language": "fr",
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

## 5. Packaging

A Divina publication may be distributed unpackaged on the Web, but it may also be packaged for easy distribution as a single file. To achieve this goal, this specification defines the [Readium Packaging Format (RPF)](./packaging.md).

To maximize compatibility with dedicated apps, such a package has its own file extension and media-type:

- its file extension <strong class="rfc">must</strong> be `.divina`
- its media type <strong class="rfc">must</strong> be `application/divina+zip`

As an alternative, the manifest may also be included into an EPUB 3 publication, an hybrid solution also specified in the [Readium Packaging Format (RPF)](./packaging.md) specification. This approach allows a publisher to create EPUB 3 fixed layout comics which are enriched by transitions, guided navigation, sounds etc. accessible via Divina compliant applications.  


## Appendix A. Compliance Levels

### Level 0

* Support for the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) with bitmap images in `readingOrder`
* Support for [presentation hints](presentation.md)
* Support for [alternate resources](#3-alternate-resources)


### Level 1

* Support for [guided navigation](#4-guided-navigation)
* Support for [transitions](../modules/transitions.md)

### Level 2

* TBD

## Appendix B. Examples

*Example 5: A manga is a DiViNa where images are presented sequentially from right-to-left with a discontinuity between images that are not in the same spread*


```json
{
  "metadata": {
    "title": "Manga",
    "identifier": "https://example.com/manga",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/divina",
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
}
```

*Example 6: A webtoon is a DiViNa where images are scrolled in a single continuous strip of content*


```json
{
  "metadata": {
    "title": "Webtoon",
    "identifier": "https://example.com/webtoon",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/divina",
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
}
```
