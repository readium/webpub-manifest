# Readium Web Publication Manifest

## Example

```json
{
  "@context": "http://readium.org/webpub/default.jsonld",
  
  "metadata": {
    "@type": "http://schema.org/Book",
    "title": "Moby-Dick",
    "author": "Herman Melville",
    "identifier": "urn:isbn:978031600000X",
    "language": "en",
    "modified": "2015-09-29T17:00:00Z"
  },

  "links": [
    {"rel": "self", "href": "http://example.org/manifest.json", "type": "application/webpub+json"},
    {"rel": "alternate", "href": "http://example.org/publication.epub", "type": "application/epub+zip"},
    {"rel": "search", "href": "http://example.org/search{?query}", "type": "text/html", "templated": true}
  ],
  
  "spine": [
    {"href": "c001.html", "type": "text/html", "title": "Chapter 1"}, 
    {"href": "c002.html", "type": "text/html", "title": "Chapter 2"}
  ],

  "resources": [
    {"rel": "cover", "href": "cover.jpg", "type": "image/jpeg", "height": 600, "width": 400},
    {"href": "style.css", "type": "text/css"}, 
    {"href": "whale.jpg", "type": "image/jpeg"}, 
    {"href": "boat.svg", "type": "image/svg+xml"}, 
    {"href": "notes.html", "type": "text/html"}
  ]
}
```

## Introduction

While the Web is the largest collection of interlinked documents ever created, it lacks a mechanism for expressing how a collection of resources, when grouped together can represent a publication.

Publication formats such as EPUB or CBZ/CBR group these documents together using a container format, making them easier to archive or transmit as a whole. But they also break an important promise of the Web: the resources of a publication are not available through HTTP to any client that would like to access them.

W3C has recently provided a definition for a [Web Publication](https://w3c.github.io/dpub-pwp-ucr/):

> A **Web Publication (WP)** is a collection of one or more constituent resources, organized together in a uniquely identifiable grouping, and presented using standard Open Web Platform technologies.

It also provides a definition for a manifest in the context of a Web Publication:
> [...] **manifest** refers to an abstract means to contain information necessary to the proper management, rendering, and so on, of a publication. This is opposed to metadata that contains information on the content of the publication like author, publication date, and so on. The precise format of how such a manifest is stored is not considered in this document.

Readium Web Publication Manifest is an attempt to standardize a JSON based manifest format that follows both definitions.

To facilitate the interoperability between EPUB and Web Publications, this document also defines a number of extension points to fully support EPUB specific features.

## The Web Publication Manifest Document

### Data Model

The manifest itself is a collection.
A collection is a group of resources, with a role and optional metadata.

In our serialization, the key for a collection is the role of that collection:

```
role
  metadata
  links
  [sub-collections]
```

A manifest must have one `spine` collection where the components of the publications are listed in the linear reading order. 

Other resources that are required to interact with the publication are listed in a `resources` collection. 

Collections that do not contain any metadata or subcollections (also called "compact collections"), 
can use a more compact syntax where they simply contain an array of [Link Objects](#the-link-object). 

### Collection Roles

This specification defines two collection roles that are the building blocks of any manifest:

| Role  | Semantics | Compact? | Required? |
| ----- | --------- | -------- | --------- |
| spine  | Identifies a list of resources in reading order for the publication.  | Yes  | Yes  |
| resources  | Identifies resources that are necessary for rendering the publication.  | Yes  | No  |

Additional collection roles are defined in the [Collection Roles registry](roles.md).

Extensions to these collection roles must use a URI as their JSON key.

### Metadata

JSON-LD provides an easy and standard way to extend existing JSON document: through the addition of a context, we can associate keys in a document to Linked Data elements from various vocabularies.

The Web Publication Manifest relies on JSON-LD to provide a context for the `metadata` object of the manifest.

While JSON-LD is very flexible and allows the context to be defined in-line (local context) or referenced (URI), the Web Publication Manifest restricts context definition strictly to references.

The Web Publication Manifest defines an initial registry of well-known context documents, which currently includes the following references:

| Name  | URI | Description | Required? |
| ---- | ----------- | ------------- | --------- |
[Default Context](contexts/default/) | http://readium.org/webpub/default.jsonld  | Default context definition used in every Web Publication Manifest. | Yes |

Context documents are all defined and listed in the [Context Documents registry](contexts/).

The Readium Web Publication Manifest has a single requirement in terms of metadata: all publications must contain at least a `title` in its `metadata`.

```json
"metadata": {
  "title": "Test Publication"
}
```

In addition to `title`, this specification also recommends including `@type` to describe the nature of the publication described by the manifest.

```json
"metadata": {
  "@type": "http://schema.org/Book",
  "title": "Test Publication"
}
```

### The Link Object

The Link Object is used in `links` and in compact collections to list resources associated to a collection. 
It requires at least the presence of `href` and `type`:

| Name  | Value | Format | Required? |
| ------------- | ------------- | ------------- | ------------- |
| href  | Link location.  | URI  | Yes  |
| type  | MIME type of resource.  | MIME Media Type  | Yes  |
| title  | Title of the linked resource.  | String  | No  |
| rel  | Indicates the relationship between the resource and its containing collection.  | [Link Relationship](relationships.md) or URI for an extension  | No  |
| properties  | Properties associated to the linked resource.  | [Properties Object](properties.md)  | No  |
| height  | Indicates the height of the linked resource in pixels.  | Integer  | No  |
| width  | Indicates the width of the linked resource in pixels.  | Integer | No  |
| duration  | Indicates the length of the linked resource in seconds.  | Float| No  |
| templated  | Indicates that the linked resource is a URI template.  | Boolean, defaults to false  | No  |

### Links

A manifest must contain at least one link using the `self` relationship.
This link must point to the canonical location of the manifest using an absolute URI:

```json
{
  "rel": "self",
  "href": "http://example.org/manifest.json",
  "type": "application/webpub+json"
}
```
A manifest may also contain other links, such as a `alternate` link to an EPUB 3.1 version of the publication for example.

Link relationships that are currently used in this specification and its extensions are listed in the [Link Relationships registry](relationships.md).

## Content Documents

A Readium Web Publication Manifest can point to any text, image, video or audio format that can be used in a Web browser.

## Discovering a Manifest

To indicate that a content document is associated with a particular Web Publication Manifest, 
use a `link` element in the HTML `head`:

```html
<link href="manifest.json" rel="manifest" type="application/webpub+json">
```

The manifest can also be associated with any resources served over HTTP using the `Link` header:

```
Link: <http://example.org/manifest.json>; rel="manifest";
         type="application/webpub+json"
```

## Table of Contents

A Web Publication Manifest can indicate that a table of contents is available using the `contents` rel value in a Link Object listed in `spine` or `resources`:

```json
{
  "rel": "contents", 
  "href": "contents.html", 
  "type": "text/html"
}
```

The link must point to an HTML or XHTML document and may be a Navigation Document as defined in EPUB 3.1. 

A Web Publication Manifest client may also rely on the `title` key included in each Link Object of the `spine` to extract a minimal table of contents.

## Cover

A Web Publication Manifest can also provide a cover using the `cover` rel value in a Link Object listed in `spine`, `resources` or `links`:

```json
{
  "rel": "cover", 
  "href": "cover.jpg", 
  "type": "image/jpeg", 
  "height": 600, 
  "width": 400
}
```

The link must point to an image using one of the following media types: `image/jpeg`, `image/png`, `image/gif` or `image/svg+xml`. 

## Packaging a Web Publication

The Web Publication Manifest is primarily meant to be distributed unpackaged and exploded on the Web.

That said, a Web Publication Manifest may be included in a classic EPUB, but reading systems have no obligation to access the manifest.

If a Web Publication Manifest is included in an EPUB container, the following restrictions apply:

- the manifest document must be named `manifest.json` and must appear at the top level of the container
- the OPF of the primary rendition must include a link to the manifest where the relationship is set to `alternate`

```xml
<link rel="alternate" href="manifest.json" media-type="application/webpub+json" />
```

In addition to the EPUB format, a Web Publication can also be distributed as a separate package:

- the package itself must be a ZIP archive
- the manifest document must be named `manifest.json` and must appear at the top level of the package
- all resources referenced in `spine`, `resources` and `links` that are referenced using a relative URI, must be referenced relatively to the manifest
- its media type is `application/webpub+zip`
- its file extension is `.webpub`
- a publication where any resource is encrypted using a DRM must use a different media type and file extension
