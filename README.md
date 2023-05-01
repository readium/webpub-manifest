# Readium Web Publication Manifest

The Readium Web Publication Manifest is a JSON-based document meant to represent and distribute publications over HTTPS.

It is the primary exchange format used in the [Readium Architecture](https://readium.org/architecture) and serves as the main building block for [OPDS 2.0](https://drafts.opds.io/opds-2.0). 

**Editors:**

* Hadrien Gardeur ([De Marque](http://www.demarque.com))

**Participate:**

* [GitHub readium/webpub-manifest](https://github.com/readium/webpub-manifest)
* [File an issue](https://github.com/readium/webpub-manifest/issues)

## Table of Contents

* [Example](#example)
* [1. Introduction](#1-introduction)
  + [1.1. Goals](#11-goals)
  + [1.2. Terminology](#12-terminology)
  + [1.3. Abstract Model](#13-abstract-model)
* [2. Syntax](#2-syntax)
  + [2.1. Sub-Collections](#21-sub-collections)
  + [2.2. Metadata](#22-metadata)
  + [2.3. Links](#23-links)
  + [2.4. The Link Object](#24-the-link-object)
* [3. Resources in the Reading Order](#3-resources-in-the-reading-order)
* [4. Media Type](#4-media-type)
* [5. Discovering a Manifest](#5-discovering-a-manifest)
* [6. Table of Contents](#6-table-of-contents)
* [7. Cover](#7-cover)
* [8. Extensibility](#8-extensibility)
* [9. Packaging a Readium Web Publication](#9-packaging)
* [Appendix A. JSON Schema](#appendix-a-json-schema)

## Example

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  
  "metadata": {
    "@type": "http://schema.org/Book",
    "title": "Moby-Dick",
    "author": "Herman Melville",
    "identifier": "urn:isbn:978031600000X",
    "language": "en",
    "modified": "2015-09-29T17:00:00Z"
  },

  "links": [
    {"rel": "self", "href": "https://example.com/manifest.json", "type": "application/webpub+json"},
    {"rel": "alternate", "href": "https://example.com/publication.epub", "type": "application/epub+zip"},
    {"rel": "search", "href": "https://example.com/search{?query}", "type": "text/html", "templated": true}
  ],
  
  "readingOrder": [
    {"href": "https://example.com/c001.html", "type": "text/html", "title": "Chapter 1"}, 
    {"href": "https://example.com/c002.html", "type": "text/html", "title": "Chapter 2"}
  ],

  "resources": [
    {"rel": "cover", "href": "https://example.com/cover.jpg", "type": "image/jpeg", "height": 600, "width": 400},
    {"href": "https://example.com/style.css", "type": "text/css"}, 
    {"href": "https://example.com/whale.jpg", "type": "image/jpeg"}, 
    {"href": "https://example.com/boat.svg", "type": "image/svg+xml"}, 
    {"href": "https://example.com/notes.html", "type": "text/html"}
  ]
}
```

## 1. Introduction

### 1.1. Goals

While the Web is the largest collection of interlinked documents ever created, it lacks a mechanism for expressing how a collection of resources, when grouped together can represent a publication.

Publication formats such as EPUB or CBZ/CBR group these documents together using a container format, making them easier to archive or transmit as a whole. But they also break an important promise of the Web: the resources of a publication are not available through HTTP to any client that would like to access them.

W3C has recently provided a definition for a [Web Publication](https://w3c.github.io/dpub-pwp-ucr/):

> A **Web Publication (WP)** is a collection of one or more constituent resources, organized together in a uniquely identifiable grouping, and presented using standard Open Web Platform technologies.

It also provides a definition for a manifest in the context of a Web Publication:
> [...] **manifest** refers to an abstract means to contain information necessary to the proper management, rendering, and so on, of a publication. This is opposed to metadata that contains information on the content of the publication like author, publication date, and so on. The precise format of how such a manifest is stored is not considered in this document.

The Readium Web Publication Manifest is an attempt to standardize a JSON based manifest format that follows both definitions.

To facilitate the interoperability between EPUB and Web Publications, this document also defines a number of extension points to fully support EPUB specific features.

### 1.2. Terminology

<dl>
 <dt id="collection">Collection</dt>
 <dd>A grouping of some variable number of data items. In the context of this specification, a collection is defined as a grouping of metadata, links and sub-collections.</dd>
 <dt>Full Collection</dt>
 <dd>A collection that contains at least two or more data items among metadata, links and sub-collections.</dd>
 <dt>Compact Collection</dt>
 <dd>A collection that contains one or more links, but doesn't contain any metadata or sub-collections.</dd>
 <dt>Manifest</dt>
 <dd>A manifest is a full collection that represents structured information about a publication.</dd>
</dl>

### 1.3. Abstract Model

The Readium Web Publication Manifest is based on a hypermedia model inspired by [Atom](https://tools.ietf.org/html/rfc4287), [HAL](http://stateless.co/hal_specification.html), [Siren](https://github.com/kevinswiber/siren) and [Collection+JSON](http://amundsen.com/media-types/collection/format/) among others.

Every Readium Web Publication Manifest is a [collection](#collection) that <strong class="rfc">must</strong> contain:

- [metadata](#22-metadata)
- [links](#23-links)
- a [reading order](#21-sub-collections)


## 2. Syntax

### 2.1. Sub-Collections

This specification defines two collection roles that are the building blocks of any manifest:

| Role  | Definition | Compact Collection? | Required? |
| ----- | ---------- | ------------------- | --------- |
| `readingOrder`  | Identifies a list of resources in reading order for the publication.  | Yes  | Yes  |
| `resources`  | Identifies resources that are necessary for rendering the publication.  | Yes  | No  |

Both collections are compact collections, which means that they contain one or more [Link Objects](#24-the-link-object). 

All additional collection roles are defined in the [Collection Roles registry](roles.md).

Extensions that are not registered in the registry <strong class="rfc">must</strong> use a URI for their role.

A manifest <strong class="rfc">must</strong> contain a `readingOrder` sub-collection.

Other resources that are required to render the publication <strong class="rfc">should</strong> be listed in `resources`.

All resources listed in `readingOrder` and `resources` <strong class="rfc">must</strong> indicate their media type using `type`.

*Example 1: Reading order and list of resources*

```json
{
  "readingOrder": [
    {"href": "/chapter1", "type": "text/html"},
    {"href": "/chapter2", "type": "text/html"}
  ],
  "resources": [
    {"href": "/style.css", "type": "text/css"},
    {"href": "/image1.jpg", "type": "image/jpeg"}
  ]
}
```



### 2.2. Metadata

JSON-LD provides an easy and standard way to extend existing JSON document: through the addition of a context, we can associate keys in a document to Linked Data elements from various vocabularies.

The Web Publication Manifest relies on JSON-LD to provide a context for the `metadata` object of the manifest.

While JSON-LD is very flexible and allows the context to be defined in-line (local context) or referenced (URI), the Web Publication Manifest restricts context definition strictly to references (URIs) at the top-level of the document.

The Web Publication Manifest defines an initial registry of well-known context documents, which currently includes the following references:

| Name  | URI | Description | Required? |
| ---- | ----------- | ------------- | --------- |
[Default Context](contexts/default/) | [https://readium.org/webpub-manifest/context.jsonld](https://readium.org/webpub-manifest/context.jsonld)  | Default context definition used in every Web Publication Manifest. | Yes |

Context documents are all defined and listed in the [Context Documents registry](contexts/).

The Readium Web Publication Manifest has a single requirement in terms of metadata: all publications <strong class="rfc">must</strong> include a [title](contexts/default/#title).

In addition all publications <strong class="rfc">should</strong> include a `@type` key to describe the nature of the publication.

*Example 2: Minimal metadata*

```json
"metadata": {
  "@type": "http://schema.org/Book",
  "title": "Test Publication"
}
```

### 2.3. Links

Links are expressed using the `links` key that contains one or more [Link Objects](#24-the-link-object).

A manifest <strong class="rfc">must</strong> contain at least one link using the `self` relationship where `href` is an absolute URI to the canonical location of the manifest.

*Example 3: Link to the canonical location of a manifest*

```json
"links": [
  {
    "rel": "self",
    "href": "http://example.org/manifest.json",
    "type": "application/webpub+json"
  }
]
```
A manifest <strong class="rfc">may</strong> also contain other links, such as a `alternate` link to an EPUB 3.2 version of the publication for example.

Link relations that are currently used in this specification and its extensions are listed in the [Link Relations registry](relationships.md).

### 2.4. The Link Object

The Link Object is a core component of the Readium Web Publication Manifest JSON syntax.

It represents a link to a resource along with a set of metadata associated with that resource.

This specification defines the following keys for this JSON object:

| Key  | Definition | Format | Required? |
| ---- | -----------| -------| ----------|
| `href`  | URI or URI template of the linked resource  | URI or URI template | Yes  |
| `templated`  | Indicates that `href` is a URI template  | Boolean, defaults to `false`  | Only when `href` is a URI template  |
| `type`  | Media type of the linked resource  | MIME Media Type  | No  |
| `title`  | Title of the linked resource  | String  | No  |
| `rel`  | Relation between the resource and its containing collection  | One or more [Link Relations](relationships.md) | No  |
| `properties`  | Properties associated to the linked resource  | [Properties Object](properties.md)  | No |
| `height`  | Height of the linked resource in pixels | Integer  | No  |
| `width`  | Width of the linked resource in pixels | Integer | No  |
| `duration`  | Duration of the linked resource in seconds | Float| No  |
| `bitrate`  | Bit rate of the linked resource in kilobits per second | Float| No  |
| `language`  | Expected language of the linked resource | One or more [BCP 47 Language Tag](https://tools.ietf.org/html/bcp47) | No  |
| `alternate`  | Alternate resources for the linked resource | One or more [Link Objects](#24-the-link-object) | No |
| `children`  | Resources that are children of the linked resource, in the context of a given collection role | One or more [Link Objects](#24-the-link-object) | No |

## 3. Resources in the Reading Order

The `readingOrder` of a manifest <strong class="rfc">may</strong> contain references to any text, image, video or audio resource that can be opened in a Web browser.

## 4. Media Type

This specification introduces a dedicated media type value to identify the Readium Web Publication Manifest: `application/webpub+json`.

All HTTP responses for the manifest <strong class="rfc">must</strong> indicate this media type in their headers.

## 5. Discovering a Manifest

The Readium Web Publication Manifest <strong class="rfc">may</strong> be referenced by resources included in its `readingOrder` or `resources` using a link.

Such links <strong class="rfc">must</strong> include:

- `application/webpub+json` as the media type of the manifest
- `manifest` as the relation of the link

*Example 4: Link in HTML to a manifest*

```html
<link href="manifest.json" rel="manifest" type="application/webpub+json">
```

*Example 5: Link in HTTP headers to a manifest*

```
Link: <http://example.org/manifest.json>; rel="manifest";
         type="application/webpub+json"
```

## 6. Table of Contents

A Readium Web Publication Manifest <strong class="rfc">may</strong> contain a reference to a table of contents.

In order to represent a table of contents in the manifest, this specification introduces an additional collection role:

| Role  | Definition | Compact Collection? | Required? |
| ----- | ---------- | ------------------- | --------- |
| `toc`  | Identifies the collection that contains a table of contents. | Yes  | No  |

*Example 6: Partial TOC for an audiobook*

```json
"toc": [
  {
    "href": "track1.mp3#t=71",
    "title": "Part 1 - This World",
    "children": [
      {
        "href": "track1.mp3#t=80",
        "title": "Section 1 - Of the Nature of Flatland"
      },
      {
        "href": "track1.mp3#t=415",
        "title": "Section 2 - Of the Climate and Houses in Flatland"
      },
      {
        "href": "track1.mp3#t=789",
        "title": "Section 3 - Concerning the Inhabitants of Flatland"
      }
    ]
  }
]
```


As a fallback mechanism, a Readium Web Publication Manifest <strong class="rfc">may</strong> identify an HTML or XHTML resource in `readingOrder` or `resources` as a table of contents using the `contents` link relation.

*Example 7: Reference to an HTML resource containing a TOC*

```json
{
  "rel": "contents", 
  "href": "contents.html", 
  "type": "text/html"
}
```

A User Agent <strong class="rfc">may</strong> also rely on the `title` key included in each Link Object of the `readingOrder` to extract a minimal table of contents.

[The EPUB profile](profiles/epub.md) also defines [additional collection roles](profiles/epub.md#collection-roles) for embedding navigation directly in the manifest.

## 7. Cover

A Readium Web Publication Manifest <strong class="rfc">may</strong> contain a reference to a cover.

Link Objects in `readingOrder`, `resources` or `links` can be identified as such using the `cover` link relation.

All Link Objects containing the `cover` link relation <strong class="rfc">must</strong> reference an image directly. They <strong class="rfc">should</strong> include a `height` and `width` to facilitate how they are processed by User Agents.

This specification recommends using one of the following media types: `image/jpeg`, `image/png`, `image/gif`, `image/webp` or `image/svg+xml`. 

*Example 8: Reference to a cover*

```json
{
  "rel": "cover", 
  "href": "cover.jpg", 
  "type": "image/jpeg", 
  "height": 600, 
  "width": 400
}
```

## 8. Extensibility

The manifest provides multiple extension points:

- additional collection roles using the [registry of roles](roles.md) or URIs
- additional metadata using schema.org, terms from the [registry of context documents](contexts/) or URIs (for individual terms)
- additional link relations from the [IANA link registry](https://www.iana.org/assignments/link-relations/link-relations.xhtml) or URIs
- additional properties using the [registry of properties](properties.md)

In addition to these extension points, this specification defines both a [profile registry](profiles/) and a [module registry](modules/) as well.

The initial registry, contains the following profiles:

| Name  |  Description |
| ----- | ------------ |
| [EPUB Profile](profiles/epub.md) | A profile for EPUB content transformed to Web Publications. |
| [Audiobook Profile](profiles/audiobook.md) |  A profile for Audiobooks. |
| [Divina Profile](profiles/divina.md) | A profile for Digital Visual Narrative publications (comics, manga and bandes dessinées). |
| [PDF Profile](profiles/pdf.md) | A profile for PDF documents integrated into Web Publications. |

## 9. Packaging

A Readium Web Publication may be distributed unpackaged on the Web, but it may also be packaged for easy distribution as a single file. To achieve this goal, this specification defines the [Readium Packaging Format (RPF)](./packaging.md).

## Appendix A. JSON Schema

A JSON Schema is available under version control at [https://github.com/readium/webpub-manifest/tree/master/schema](https://github.com/readium/webpub-manifest/tree/master/schema)

For the purpose of validating a Readium Web Publication Manifest, use the following JSON Schema resource: [https://readium.org/webpub-manifest/schema/publication.schema.json](https://readium.org/webpub-manifest/schema/publication.schema.json)
