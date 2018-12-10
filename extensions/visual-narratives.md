[![Readium Logo](https://readium.org/assets/logos/readium-logo.png)](https://readium.org)

<style>
.rfc {
    color: #d55;
    font-variant: small-caps;
    font-style: normal;
}
</style>

# Visual Narrative Profile

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
    "numberOfPages": 62
    "belongsTo": [
      "series": {
        "name": "Les Aventures de Tintin",
        "position": 16
      }
    ]
  },

  "links": [
    {"rel": "self", "href": "http://example.org/manifest.json", "type": "application/visual-narrative+json"}
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

## Introduction

The goal of this document is to provide a profile dedicated to visual narratives for the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) that will cover the following requirements:

- list the different components of a visual narrative
- support multiple resolutions, image formats and/or languages for each visual element of a narrative
- provide guided navigation (also called panel by panel navigation in the case of comics)

While the Visual Narrative Manifest is technically a profile of the Readium Web Publication Manifest, it has its own media type in order to maximize compatibilty with dedicated apps: `application/visual-narrative+json`.


## Listing Visual Narrative Resources

A visual narratived is divided into one or more images, which are all listed in the `readingOrder` of the manifest.

In addition to the normal requirements of a `readingOrder`, all Link Objects have the following additional requirements:
 
 - they <span class="rfc">must</span> point strictly to bitmap images

In addition, all Link Objects <span class="rfc">should</span> include `width` and `height` to indicate the dimensions of each resource

## Alternate Visual Resources

In order to provide multiple variants of the same resource, Link Objects in the `readingOrder` <span class="rfc">may</span> rely on the `alternate` key.

All Link Objects present in the `alternate` array:

- <span class="rfc">must</span> indicate their media-type using `type`
- <span class="rfc">should</span> indicate their dimensions using `height` and `width`
- <span class="rfc">may</span> target a different language using `hreflang`
- <span class="rfc">must not</span> reference a URI template

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
  "hreflang": "fr"
  "alternate": [
    {
      "href": "http://example.org/page1-jp.jpeg", 
      "type": "image/jpeg",
      "hreflang": "jp"
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

## Package

In order to facilitate distribution, both manifest and images can also be distributed using a package based on [the requirements expressed for the Readium Web Publication Manifest](https://readium.org/webpub-manifest#8-package).

To maximize compatibility with comics apps, the package for this profile has its own file extension and media-type:

- its file extension <span class="rfc">must</span> be `.visupub`
- its media type <span class="rfc">must</span> be `application/visual-narrative+zip`

As an alternative, the manifest can also be added to a CBZ file at the same well-known location.