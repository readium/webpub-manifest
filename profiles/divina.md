# Digital Visual Narratives Profile

**Editors:**

* Hadrien Gardeur ([EDRLab](https://www.edrlab.org))
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
    "belongsTo": {
      "series": {
        "name": "Les Aventures de Tintin",
        "position": 16
      }
    }
  },
  "links": [
    {
      "rel": "self", 
      "href": "http://example.org/manifest.json", 
      "type": "application/divina+json"
    }
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

The goal of this specification is to provide a profile dedicated to digital visual narratives (Divina) for the [Readium Web Publication Manifest](https://readium.org/webpub-manifest).

## 1. Declaring conformance to the Divina profile

In order to declare that it conforms to the Divina Profile, a Readium Web Publication Manifest <strong class="rfc">must</strong>:

- include a `conformsTo` property in its `metadata` section, with `https://readium.org/webpub-manifest/profiles/divina` as one of its values
- use `application/divina+json` as its media type

While the Divina Manifest is technically a profile of the Readium Web Publication Manifest, the use of its dedicated media type is recommended to maximize compatibility with applications that <strong class="rfc">must</strong> target comics/manga/webtoons exclusively.

## 2. Listing Resources

A visual narrative is divided into one or more images, which are all listed in the `readingOrder` of the manifest.

In addition to the normal requirements of a `readingOrder`, all Link Objects have the following additional requirements:
 
 - they <strong class="rfc">must</strong> point strictly to bitmap images

In addition, all Link Objects <strong class="rfc">should</strong> also include `width` and `height` to indicate the dimensions of each resource.

## 3. Alternate Resources

In order to provide multiple variants of the same resource, Link Objects in the `readingOrder` <strong class="rfc">may</strong> rely on the `alternate` key.

All Link Objects present in the `alternate` array:

- <strong class="rfc">must</strong> indicate their media-type using `type`
- <strong class="rfc">should</strong> indicate their dimensions using `height` and `width`
- <strong class="rfc">may</strong> target a different language using `language`

*Example 1: A resource available in JPEG and AVIF*

```json
{
  "href": "http://example.org/page1.jpeg", 
  "type": "image/jpeg", 
  "alternate": [
    {
      "href": "http://example.org/page1.avif", 
      "type": "image/avif"
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

## 4. Layout

### 4.1. Fixed layout

By default, each publication that conforms to the Divina profile is handled like a fixed layout publication, which means that:

- the publication is paginated by default, where each page is an image from the `readingOrder`
- images are usually displayed with both dimensions fully contained in the viewport by default
- reading systems can decide to display two images side by side in a spread, using the [`page`](../properties.md#page) property as a hint

Reading systems are strongly encouraged to let the user decide if they prefer reading the publication:

- paginated or scrolled
- with or without spreads (for example, with spreads in landscape mode but without spreads in portrait mode)

### 4.2. Scrolled publications

For publications where a single continuous scroll is required to properly display the publication (such as webtoons for example), content creators <strong class="rfc">must</strong> use the [`layout`](../contexts/default/README.md#layout-and-reading-progression) property with the `scrolled` value.

*Example 4: A scrolled publication that contains two episodes*

```json
{
  "metadata": {
    "title": "The Cat Collector",
    "identifier": "https://example.com/cat-collector",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/divina",
    "layout": "scrolled"
  },
  "readingOrder": [
    {
      "href": "episode1-image1.jpg",
      "type": "image/jpeg"
    },
    {
      "href": "episode1-image2.jpg",
      "type": "image/jpeg"
    },
    {
      "href": "episode1-image3.jpg",
      "type": "image/jpeg"
    },
    {
      "href": "episode2-image1.jpg",
      "type": "image/jpeg"
    },
    {
      "href": "episode2-image2.jpg",
      "type": "image/jpeg"
    },
    {
      "href": "episode2-image3.jpg",
      "type": "image/jpeg"
    }
  ],
  "toc": [
    {
      "title": "Episode 1",
      "href": "episode1-image1.jpg",
      "type": "image/jpeg",
      "rel": "episode"
    },
    {
      "title": "Episode 2",
      "href": "episode2-image1.jpg",
      "type": "image/jpeg",
      "rel": "episode"
    }
  ]
}
```

## 5. Structural semantics

This profile introduces new `rel` values in order to indicate how a publication is structured.

These values are meant to be used in a table of contents (`toc`) with a nested structure.

| Value  | Example | 
| ------ | ------- | 
| `chapter` | Manga are usually divided into chapters, published weekly or monthly in manga magazines. <br /> They're often collected into volumes (Tankōbon in Japanse) at a later point. |
| `episode` | Webtoons or scrolled comics are often distributed using individually episodes that may be grouped into seasons. |
| `issue` | US comics are usually distributed as single issue publications, that are collected into volume at a later point. |
| `part` | Graphic novels tend to follow more closely novels in how their content are subdivided, which means that a number of them are divided in parts in addition to chapters and volumes. |
| `season` | A season usually collects multiple episodes for webtoons. |
| `volume` | A volume usually collects multiple chapters, episodes or issues or a given series. |

*Example 5: A collected edition with multiple volumes*

```json
"toc": [
  {
    "title": "Cover",
    "href": "cover.jpg",
    "type": "image/jpeg",
    "rel": "cover"
  },
  {
    "title": "Volume 1 - The Tests of the Ninja",
    "href": "volume1.jpg",
    "type": "image/jpeg",
    "rel": "volume"
    "children": [
      {
        "title": "Chapter 1 - Uzumaki Naruto!",
        "href": "chapter1-page1.jpg",
        "type": "image/jpeg"
        "rel": "chapter"
      },
      {
        "title": "Chapter 2 - Konohamaru!",
        "href": "chapter2-page1.jpg",
        "type": "image/jpeg"
        "rel": "chapter"
      },
      {
        "title": "Chapter 3 - Enter Sasuke!",
        "href": "chapter3-page1.jpg",
        "type": "image/jpeg"
        "rel": "chapter"
      },
      {
        "title": "Chapter 4 - Hatake Kakashi!",
        "href": "chapter4-page1.jpg",
        "type": "image/jpeg"
        "rel": "chapter"
      },
      {
        "title": "Chapter 5 - Pride Goeth Before a Fall",
        "href": "chapter5-page1.jpg",
        "type": "image/jpeg"
        "rel": "chapter"
      },
      {
        "title": "Chapter 6 - Not Sasuke!",
        "href": "chapter6-page1.jpg",
        "type": "image/jpeg"
        "rel": "chapter"
      },
      {
        "title": "Chapter 7 - Kakashi's Decision",
        "href": "chapter7-page1.jpg",
        "type": "image/jpeg"
        "rel": "chapter"
      }
    ]
  },
  {
    "title": "Volume 2 - The Worst Client",
    "href": "volume2.jpg",
    "type": "image/jpeg",
    "rel": "volume"
  }
]
```


## 6. Packaging

A Divina publication may be distributed unpackaged on the Web, but it may also be packaged for easy distribution as a single file. To achieve this goal, this specification relies on the [Readium Packaging Format](./packaging.md).

To maximize compatibility with dedicated apps, such a package has its own file extension and media-type:

- its file extension <strong class="rfc">must</strong> be `.divina`
- its media type <strong class="rfc">must</strong> be `application/divina+zip`

As an alternative, the manifest may also be included in:

- an EPUB 3 publication, as specified in the [Readium Packaging Format](./packaging.md#6-hybrid-epub-3--rpf-packages) specification
- or dedicated formats for comics such as CBZ/CBR

## Appendix A. JSON Schema

The following JSON Schemas for this profile is available under version control: 

- Link Properties: <https://github.com/readium/webpub-manifest/blob/master/schema/extensions/divina/properties.schema.json>

For the purpose of validating a Readium Web Publication Manifest, use the following JSON Schema resource: 

- <https://readium.org/webpub-manifest/schema/extensions/divina/properties.schema.json>

## Appendix B. Examples

*Example 6: A manga is a Divina where the reading progression is right-to-left*


```json
{
  "metadata": {
    "title": "Manga",
    "identifier": "https://example.com/manga",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/divina",
    "readingProgression": "rtl"
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

*Example 7: A continuously scrolled publication (a "webtoon") is a Divina where images are displayed in a single continuous strip of content*


```json
{
  "metadata": {
    "title": "Webtoon",
    "identifier": "https://example.com/webtoon",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/divina",
    "layout": "scrolled"
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