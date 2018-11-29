[![Readium Logo](https://readium.org/assets/logos/readium-logo.png)](https://readium.org)

<style>
.rfc {
    color: #d55;
    font-variant: small-caps;
    font-style: normal;
}
</style>

# Readium Web Publication Manifest

The Readium Web Publication Manifest is a JSON-based document meant to represent and distribute publications over HTTPS.

It is the primary exchange format used in the [Readium Architecture](https://readium.org/architecture) and serves as the main building block for [OPDS 2.0](https://drafts.opds.io/opds-2.0). 

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

Every Readium Web Publication Manifest is a [collection](#collection) that <span class="rfc">must</span> contain:

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

Both collections are full collections, which means that they contain one or more [Link Objects](#24-the-link-object). 

All additional collection roles are defined in the [Collection Roles registry](roles.md).

Extensions that are not registered in the registry <span class="rfc">must</span> use a URI for their role.

A manifest <span class="rfc">must</span> contain a `readingOrder` sub-collection.

Other resources that are required to render the publication <span class="rfc">should</span> be listed in `resources`.

All resources listed in `readingOrder` and `resources` <span class="rfc">must</span> indicate their media type using `type`.

*Example 1: Reading order and list of resources*

```json
"readingOrder": [
  {"href": "/chapter1", "type": "text/html"},
  {"href": "/chapter2", "type": "text/html"}
],

"resources": [
  {"href": "/style.css", "type": "text/css"},
  {"href": "/image1.jpg", "type": "image/jpeg"}
]
```



### 2.2. Metadata

JSON-LD provides an easy and standard way to extend existing JSON document: through the addition of a context, we can associate keys in a document to Linked Data elements from various vocabularies.

The Web Publication Manifest relies on JSON-LD to provide a context for the `metadata` object of the manifest.

While JSON-LD is very flexible and allows the context to be defined in-line (local context) or referenced (URI), the Web Publication Manifest restricts context definition strictly to references (URIs) at the top-level of the document.

The Web Publication Manifest defines an initial registry of well-known context documents, which currently includes the following references:

| Name  | URI | Description | Required? |
| ---- | ----------- | ------------- | --------- |
[Default Context](contexts/default/) | https://readium.org/webpub-manifest/context.jsonld  | Default context definition used in every Web Publication Manifest. | Yes |

Context documents are all defined and listed in the [Context Documents registry](contexts/).

The Readium Web Publication Manifest has a single requirement in terms of metadata: all publications <span class="rfc">must</span> include a [title](contexts/default/#title).

In addition all publications <span class="rfc">should</span> include a `@type` key to describe the nature of the publication.

*Example 2: Minimal metadata*

```json
"metadata": {
  "@type": "http://schema.org/Book",
  "title": "Test Publication"
}
```

### 2.3. Links

Links are expressed using the `links` key that contains one or more [Link Objects](#24-the-link-object).

A manifest <span class="rfc">must</span> contain at least one link using the `self` relationship where `href` is an absolute URI to the canonical location of the manifest.

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
A manifest <span class="rfc">may</span> also contain other links, such as a `alternate` link to an EPUB 3.1 version of the publication for example.

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
| `rel`  | Relation between the resource and its containing collection  | One or more [Link Relation](relationships.md) or URIs (extensions)  | No  |
| `properties`  | Properties associated to the linked resource  | [Properties Object](properties.md)  | No  |
| `height`  | Height of the linked resource in pixels  | Integer  | No  |
| `width`  | Width of the linked resource in pixels  | Integer | No  |
| `duration`  | Duration of the linked resource in seconds  | Float| No  |
| `bitrate`  | Bit rate of the linked resource in kilobits per second  | Float| No  |


## 3. Resources in the Reading Order

The `readingOrder` of a manifest <span class="rfc">may</span> contain references to any text, image, video or audio resource that can be opened in a Web browser.

## 4. Media Type

This specification introduces a dedicated media type value to identify the Readium Web Publication Manifest: `application/webpub+json`.

All HTTP responses for the manifest <span class="rfc">must</span> indicate this media type in their headers.

## 4. Discovering a Manifest

The Readium Web Publication Manifest <span class="rfc">may</span> be referenced by resources included in its `readingOrder` or `resources` using a link.

Such links <span class="rfc">must</span> include:

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

## 5. Table of Contents

A Readium Web Publication Manifest <span class="rfc">may</span> contain a reference to a table of contents.

In order to represent a table of contents in the manifest, this specification introduces an additional collection role:

| Role  | Semantics | Compact Collection? | Required? |
| ----- | --------- | ------------------- | --------- |
| `toc`  | Identifies the collection that contains a table of contents. | Yes  | No  |

As a fallback mechanism, a Readium Web Publication Manifest <span class="rfc">may</span> identify an HTML or XHTML resource in `readingOrder` or `resources` as a table of contents using the `contents` link relation.

*Example 6: Reference to an HTML resource containing a TOC*

```json
{
  "rel": "contents", 
  "href": "contents.html", 
  "type": "text/html"
}
```

A User Agent <span class="rfc">may</span> also rely on the `title` key included in each Link Object of the `readingOrder` to extract a minimal table of contents.

[The EPUB extension](/extensions/epub.md) also defines [additional collection roles](https://github.com/readium/webpub-manifest/blob/master/extensions/epub.md#collection-roles) for embedding navigation directly in the manifest.

## 6. Cover

A Readium Web Publication Manifest <span class="rfc">may</span> contain a reference to a cover.

Link Objects in `readingOrder`, `resources` or `links` can be identified as such using the `cover` link relation.

All Link Objects containing the `cover` link relation <span class="rfc">must</span> reference an image directly. They <span class="rfc">should</span> include a `height` and `width` to facilitate how they are processed by User Agents.

This specification recommends using one of the following media types: `image/jpeg`, `image/png`, `image/gif` or `image/svg+xml`. 

*Example 7: Reference to a cover*

```json
{
  "rel": "cover", 
  "href": "cover.jpg", 
  "type": "image/jpeg", 
  "height": 600, 
  "width": 400
}
```

## 7. Extensibility

The manifest provides multiple extension points:

- additional collection roles using the [registry of roles](roles.md) or URIs
- additional metadata using schema.org, a [registry of context documents](contexts/) or URIs (for individual terms)
- additional link relations using the [IANA link registry](https://www.iana.org/assignments/link-relations/link-relations.xhtml) or URIs
- additional properties using the [registry of properties](properties.md)

In addition to these extension points, this specification defines an [extension registry](extensions/) as well, to document specific profiles of the manifest.

The initial registry, contains the following extensions:

| Name  |  Description |
| ----- | ------------ |
| [EPUB Extension](extensions/epub.md) | Additional metadata and collection roles for representing EPUB publications. |
| [Audiobook Profile](extensions/audiobook.md) | Defines a dedicated profile for audiobooks. |

## 8. Package

The Readium Web Publication Manifest is primarily meant to be distributed unpackaged on the Web.

That said, a Readium Web Publication Manifest <span class="rfc">may</span> be included in an EPUB.

If a Readium Web Publication Manifest is included in an EPUB, the following restrictions apply:

- the manifest document <span class="rfc">must</span> be named `manifest.json` and <span class="rfc">must</span> appear at the top level of the container
- the OPF of the primary rendition <span class="rfc">must</span> include a link to the manifest where the link relation is set to `alternate`


*Example 8: Reference to a manifest in an OPF*

```xml
<link rel="alternate" 
      href="manifest.json" 
      media-type="application/webpub+json" />
```

In addition to the EPUB format, a Readium Web Publication <span class="rfc">may</span> also be distributed as a separate package where:

- its media type <span class="rfc">must</span> be `application/webpub+zip`
- its file extension <span class="rfc">must</span> be `.webpub`
- the package itself <span class="rfc">must</span> be a ZIP archive and follow the restrictions expressed in [ISO/IEC 21320-1:2015](http://standards.iso.org/ittf/PubliclyAvailableStandards/c060101_ISO_IEC_21320-1_2015.zip)
- the manifest <span class="rfc">must</span> be named `manifest.json` and <span class="rfc">must</span> appear at the top level of the package
- all resources in `readingOrder`, `resources` and `links` <span class="rfc">must</span> be referenced relatively to the manifest
- a publication where any resource is encrypted using a DRM <span class="rfc">must</span> use a different media type and file extension

## Appendix A. JSON Schema

A JSON Schema is available under version control at [https://github.com/readium/webpub-manifest/tree/master/schema](https://github.com/readium/webpub-manifest/tree/master/schema)

For the purpose of validating a Readium Web Publication Manifest, use the following JSON Schema resource: [https://readium.org/webpub-manifest/schema/publication.schema.json](https://readium.org/webpub-manifest/schema/publication.schema.json)
