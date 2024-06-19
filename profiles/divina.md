# Digital Visual Narratives Profile

**Editors:**

* Hadrien Gardeur
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

While the Divina Manifest is technically a profile of the Readium Web Publication Manifest, it has its own media type in order to maximize compatibilty with dedicated apps: `application/divina+json`.


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

## 4. Packaging

A Divina publication may be distributed unpackaged on the Web, but it may also be packaged for easy distribution as a single file. To achieve this goal, this specification relies on the [Readium Packaging Format](./packaging.md).

To maximize compatibility with dedicated apps, such a package has its own file extension and media-type:

- its file extension <strong class="rfc">must</strong> be `.divina`
- its media type <strong class="rfc">must</strong> be `application/divina+zip`

As an alternative, the manifest may also be included in:

- an EPUB 3 publication, as specified in the [Readium Packaging Format](./packaging.md#6-hybrid-epub-3--rpf-packages) specification
- or dedicated formats for comics such as CBZ/CBR


## Appendix A. Examples

*Example 5: A manga is a Divina where images are presented sequentially from right-to-left*


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

*Example 6: A continuously scrolled publication is a Divina where images are displayed in a single continuous strip of content*


```json
{
  "metadata": {
    "title": "Webtoon",
    "identifier": "https://example.com/webtoon",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/divina",
    "presentation": {
      "layout": "scrolled"
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
