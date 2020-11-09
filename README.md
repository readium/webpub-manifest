
# Readium Web Publication Manifest

The Readium Web Publication Manifest is a JSON-based document meant to represent and distribute publications over HTTPS.

It is the primary exchange format used in the [Readium Architecture](https://readium.org/architecture) and serves as the main building block for [OPDS 2.0](https://drafts.opds.io/opds-2.0). 

**Editors:**

* Hadrien Gardeur ([De Marque](http://www.demarque.com))
* Laurent Le Meur ([EDRLab](http://www.edrlab.org))

**Participate:**

* [GitHub readium/webpub-manifest](https://github.com/readium/webpub-manifest)
* [File an issue](https://github.com/readium/webpub-manifest/issues)


## Table of Contents

- [Initial Example](#first-example)
- [1. Introduction](#1-introduction)
- [2. Abstract Model](#2-abstract-model)
  - [Collection](#21-collection)
  - [Metadata](#22-metadata)
  - [Link Array](#23-link-array)
  - [Link Object](#24-link-object)
- [3. Readium Web Publication Manifest](#3-readium-web-publication-manifest)
  - [Metadata](#31-metadata)
  - [Links](#32-links)
  - [Reading Order and Resources](#33-reading-order-and-resources)
  - [Table of Contents](#34-table-of-contents)
  - [Cover](#35-cover)
- [5. Profiles, modules](#5-profiles-modules)
  - [EPUB Profile](profiles/epub.md)
  - [Audiobook Profile](profiles/audiobook.md)
  - [Digital Visual Narratives Profile](profiles/divina.md)
- [6. Media Type](#6-media-type)
- [Appendix A. JSON Schema](appendix-a-json-schema)
- [Appendix B. Relationship with W3C Web Publications](appendix-b-relationship-with-w3C-web-publications)


## Initial Example

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
    {"rel": "search", "href": "https://example.com/{pubid}/search{?query}", "type": "text/html", "templated": true}
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

While the Web is the largest collection of interlinked documents ever created, it lacks a mechanism for expressing how a collection of resources, when grouped together, represent a *publication*.

Packaged publication formats such as EPUB group together these resources, along with associated metadata, in a container file which makes them easy to transmit or archive as a whole. But they also break an important promise of the Web, as the individual resources of a publication are not easily available to client applications through HTTP.  

The [Readium Architecture](https://readium.org/architecture) is based on the use of a browser engine for displaying HTML documents and associated multimedia resources. Each Readium toolkit contains a client module (the Navigator) which drives the browser engine, and a server module (the Streamer, or the Publication Server in the case of Readium Web) which dynamically extracts resources from packaged publications. Navigator and Streamer exchange a format optimized for Web interactions, a format we name *Readium Web Publication*. 

A Readium Web Publication is a collection of one or more constituent resources, organized together in a uniquely identifiable grouping, associated with descriptive metadata, and available using standard Open Web Platform technologies. 

A Readium Web Publication Manifest (in short Manifest, acronym *rwpm*) is a JSON structure which can be easily fetched by a Web application (in our case a Navigator) from a Web Server (in our case a Streamer or Publication Server). Such structure centralizes the identifier of the publication, its associated metadata, and the set of URLs used for fetching the different resources of the publication. It can also contain a table of contents and a set of useful links, especially the URL of a cover image.

An important goal of this work is to be able to represent in a common way differents types of content, i.e. ebooks, audiobooks, digital visual narratives (comics, manga and bandes dessinées), including ebooks with synchronized media (Media Overlays in EPUB speak), and this independently of their source packaged format. 

### 1.2. References

The Readium Web Publication Manifest is based on a hypermedia model inspired, among others, by:

- [Atom](https://tools.ietf.org/html/rfc4287), 
- [HAL](http://stateless.co/hal_specification.html), 
- [Siren](https://github.com/kevinswiber/siren) 
- [Collection+JSON](http://amundsen.com/media-types/collection/format/) 

## 2. Abstract Model

### 2.1. Collection

A Collection is a grouping of [Metadata](#32-metadata), a set of [Link Arrays](#33-link-array) and a set of Sub-Collections. Each of these constituants is optional. 

A Sub-Collection is a Collection embedded in a parent Collection.

### 2.2. Metadata

The `metadata` object groups together useful meta-information about the Collection; such metadata rely on JSON-LD to express their semantics.

[JSON-LD](https://www.w3.org/TR/json-ld11/) is a lightweight Linked Data format based on JSON, and it relies on a notion of "context". In consequence a Collection that contains a `metadata` key <strong class="rfc">must</strong> set a `@context` property and every metadata property <strong class="rfc">should</strong> be defined in this context. 

Several context values <strong class="rfc">may</strong> be used simultaneously in the same Collection: in such a case the `@context` property <strong class="rfc">must</strong> be a JSON array and additional context URIs added to this array. 

While JSON-LD is very flexible and allows the context to be defined either in-line (as a local context) or by reference (via URIs), this specification strictly restricts its definition to references (URIs).

A [registry of context documents](contexts/) lists all contexts defined by this specification. 

### 2.3. Link Array

A Link Array is a unsorted set of [Link Objects](#24-link-object). 

### 2.4. Link Object

A Link Object represents a link to a resource of a Collection, along with a set of properties of that resource.

This specification defines the following keys for this JSON object:

| Key  | Definition | Format | Required? |
| ---- | -----------| -------| ----------|
| `href`  | URI or URI template of the linked resource  | URI or URI template | Yes  |
| `templated`  | Indicates that `href` is a URI template  | Boolean, defaults to `false`  | No |
| `type`  | Media type of the linked resource  | MIME Media Type  | No  |
| `title`  | Title of the linked resource  | String  | No  |
| `rel`  | Relation between the linked resource and the parent collection of the link object  | One or more [Link Relations](relationships.md) or author defined URIs | No |
| `properties`  | Properties of the linked resource  | [Properties Object](properties.md)  | No |
| `height`  | Height of the linked resource in pixels | Integer  | No  |
| `width`  | Width of the linked resource in pixels | Integer | No  |
| `duration`  | Duration of the linked resource in seconds | Float| No  |
| `bitrate`  | Bit rate of the linked resource in kilobits per second | Float| No  |
| `language`  | Expected language of the linked resource | One or more [BCP 47 Language Tag](https://tools.ietf.org/html/bcp47) | No  |
| `alternate`  | Alternate resources for the linked resource | One or more Link Objects | No |
| `children`  | Resources that are children of the linked resource in the context of a given Collection | One or more Link Objects | No |

#### 2.4.1. Properties of Linked Resources

Different sets of properties of linked resources are attached to different [profiles](profiles/).

The complete set of properties defined by this specification is listed in the [registry of resource properties](properties.md). 

## 3. Readium Web Publication Manifest

A Readium Web Publication Manifest is a [Collection](#21-collection) that contains:

- a set of [metadata](#31-metadata)
- an array of [links](#32-links)
- a [reading order](#33-reading-order-and-resources)

It <strong class="rfc">may</strong> also contain:
- a set of additional [resources](#33-reading-order-and-resources)
- a [table of contents](#34-table-of-contents)

It may also be extended by one or more supplemental [Link Arrays](#23-link-array) and [Sub-Collections](#21-collection). These <strong class="rfc">should</strong> be identified by a role defined in the [Collection Roles registry](roles.md). Extensions that are not registered in the registry <strong class="rfc">must</strong> use a URI for identifying their role.

### 3.1. Metadata

A standard [set of descriptive properties](contexts/default/) is defined by this specification. 

Metadata defined in this "default context" are common to different types of publications and they are all mapped from [schema.org](https://schema.org/) properties.

The JSON-LD "default context" is identified by the URI `https://readium.org/webpub-manifest/context.jsonld`. 

*Example 1: Context key*

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld"
}
```

This specification has a single requirement in terms of metadata: a Readium Web Publication Manifest <strong class="rfc">must</strong> include a [title](contexts/default/#title).

In addition, a Readium Web Publication Manifest <strong class="rfc">should</strong> include both a `@type` and a `conformsTo` property.

The `@type` property defines the type of the JSON-LD graph node. Its value <strong class="rfc">must</strong> be the URL of a [schema.org](https://schema.org/) Type of [CreativeWork](https://schema.org/CreativeWork) and <strong class="rfc">should</strong> be consistent with the [profile](profiles/) of the publication. Recommended value(s) are indicated in each profile page. 

The `conformsTo` property specifies the profile associated with the publication and is also specified in the corresponding profile page. 

*Example 2: Minimal metadata*

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  "metadata": {
    "title": "Test Publication",
    "@type": "http://schema.org/Book",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/epub"
  }
}
```

### 3.2. Links

Links which are deemed useful to the processing of a publication are expressed using the `links` array, which contains one or more [Link Objects](#24-link-object).

If a Readium Web Publication is provided to a client application via a Web server, it <strong class="rfc">should</strong> contain a link with the `self` relationship, `href` being an absolute URI to the canonical location of the manifest.

*Example 3: Link to the canonical location of a manifest*

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  "metadata": {
    "title": "Test Publication"
  },
  "links": [
    {
      "rel": "self",
      "href": "http://example.org/manifest.json",
      "type": "application/webpub+json"
    }
  ]
}
```
A manifest <strong class="rfc">may</strong> also contain other links, such as a `alternate` link to e.g. an EPUB 3 version of the Web publication.

Standard link relationships are listed in the [Link Relations registry](relationships.md). 

In addition, authors may use relationships defined by the [IANA link registry](https://www.iana.org/assignments/link-relations/link-relations.xhtml); they are free to extend this set, using URIs as property values.

### 3.3. Reading Order and Resources

The `readingOrder` of a Readium Web Publication Manifest is an array of references to text, audio, image or video resources that can be displayed or played in sequence by a client module.

Resources that are required to render the publication but have no place is the reading order <strong class="rfc">should</strong> be listed in the `resources` array.

Each item of the reading order is a [Link Object](#24-link-object). The media type of the linked resource <strong class="rfc">must</strong> be indicated using the `type` key.  

*Example 4: Reading order and list of resources*

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  "metadata": {
    "title": "Test Publication"
  },
  "readingOrder": [
    {"href": "/chapter1", "type": "text/html"},
    {"href": "/chapter2", "type": "text/html"}
  ],
  "resources": [
    {"href": "/style.css", "type": "text/css"},
    {"href": "/image.jpg", "type": "image/jpeg"}
  ]
}
```

### 3.4. Table of Contents

A Readium Web Publication Manifest <strong class="rfc">may</strong> contain a table of contents, i.e. a [Link Array](#23-link-array) identified by the `toc` role.

*Example 6: Partial TOC of an audiobook*

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  "metadata": {
    "title": "Test Audiobook"
  },
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
}
```

As a fallback mechanism, a Readium Web Publication Manifest <strong class="rfc">may</strong> identify an HTML or XHTML resource found in the `readingOrder` or `resources` sub-collection as a table of contents. Such reference is identified by a `contents` value of the link relation.

*Example 7: Reference to an HTML resource containing a TOC*

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  "metadata": {
    "title": "Test Audiobook"
  },
  "readingOrder": [
    {
      "rel": "contents", 
      "href": "contents.html", 
      "type": "text/html"
    }
  ]
}
```

A User Agent <strong class="rfc">should</strong> also use as a fallback the `title` key of each Link Object of the `readingOrder` to extract a minimal table of contents.

[The EPUB profile](profiles/epub.md) defines [additional collection roles](profiles/epub.md#collection-roles) which provide EPUB specific navigation affordances.

### 3.5. Cover

A Readium Web Publication Manifest <strong class="rfc">may</strong> contain a reference to a cover.

Link Objects in `readingOrder`, `resources` or `links` can be identified as such using the `cover` link relation.

All Link Objects containing the `cover` link relation <strong class="rfc">must</strong> reference an image directly. They <strong class="rfc">should</strong> include a `height` and `width` to facilitate how they are processed by User Agents.

This specification recommends using one of the following media types: `image/jpeg`, `image/png`, `image/gif`, `image/webp` or `image/svg+xml`. 

*Example 8: Reference to a cover as a resource*

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  "metadata": {
    "title": "Test Publication"
  },
  "resource": [
    {
      "rel": "cover", 
      "href": "cover.jpg", 
      "type": "image/jpeg", 
      "height": 600, 
      "width": 400
    }
  ]
}
```

## 5. Profiles, modules

The Manifest generated out of an EPUB ebook, a W3C Audiobook or a Divina visual narrative is based on the same [Core Model](#3-core-model), but each type of publication presents some specificities. Some of these specificities are shared between profiles, which leads to the creation of shared modules. 

As a consequence, this specification defines different profiles corresponding to each type of publication supported by the Readium toolkits. Each profile includes the modules, metadata, collections and resource properties specific to a given nature of publication.

| Profile  |  Description |
| -------- | ------------ |
| [EPUB Profile](profiles/epub.md) | Defines the specificities associated with EPUB publications. |
| [Audiobook Profile](profiles/audiobook.md) | Defines the specificities associated with audiobooks. |
| [Digital Visual Narratives Profile](profiles/divina.md) | Defines the specificities associated with digital visual narratives. |

These different profiles are referenced in the [profile registry](profiles/) and all available modules are referenced in the [module registry](modules/).

## 6. Media Type

This specification introduces a dedicated media type value which identifies a Readium Web Publication Manifest: `application/webpub+json`.

All HTTP responses having such manifest as a payload <strong class="rfc">must</strong> indicate this media type in their headers.

## Appendix A. JSON Schema

For the purpose of validating a Readium Web Publication Manifest, use the following JSON Schema resource: [https://readium.org/webpub-manifest/schema/publication.schema.json](https://readium.org/webpub-manifest/schema/publication.schema.json).

This JSON Schema and all sub-Schemas are available under version control at [https://github.com/readium/webpub-manifest/tree/master/schema](https://github.com/readium/webpub-manifest/tree/master/schema)

## Appendix B. Relationship with W3C Web Publications

W3C members of the W3C Publishing Working Group have defined from 2017 to 2019 the W3C Web Publications format. EDRLab and Feedbooks, both core member of the Readium Foundation, have been actively participating to this standardization initiative and provided as an input document the early work done on Readium Web Publications (as listed in the [WG charter](https://www.w3.org/2017/04/publ-wg-charter/)).

The [W3C Web Publications](https://www.w3.org/TR/wpub/) specification was published as a W3C Note (which is different from a W3C Recommendation): extracted from the spec itself it was "due to the lack of practical business cases for Web Publications, and the consequent lack of commitment to implement the technology". 

The [W3C Audiobooks](https://www.w3.org/TR/audiobooks/) specification is the result of work focusing on the audiobook use case, where business cases are stronger; in Q4 2020, it is reaching the status of W3C Recommendation. It's companion packaging specification, [Lighweight Packaging Format (LPF)](https://www.w3.org/TR/lpf/) is kept as a W3C Note. 

There are in practice many similarities between W3C Web Publications and Readium Web Publications, but there are notable differences between the two: 

- The goal of W3C Web Publications is to allow PWAs (Progressive Web Apps) fetching Web publications from Web Servers in a standard way. An HTML entry page is mandatory, the Table of Content must also be an HTML document and DRM is out of scope.
- A Readium Web Publication is the pivot format used in Readium software, and a Readium Web Publication Manifest is the structure exchanged between a Readium client module and a Readium server module. The Table of Contents is part of the manifest and it supports information related to [Readium LCP](https://www.edrlab.org/readium-lcp/) resource encryption.