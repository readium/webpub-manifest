# EPUB Profile

**Editors:**

* Hadrien Gardeur ([De Marque](http://www.demarque.com))

**Participate:**

* [GitHub readium/webpub-manifest](https://github.com/readium/webpub-manifest)
* [File an issue](https://github.com/readium/webpub-manifest/issues)

## Example

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  
  "metadata": {
    "@type": "http://schema.org/Book",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/epub",
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

## Introduction

This profile is meant to facilitate backward compatibility with EPUB and ensure that elements specific to a EPUB file are not lost when converting to a Readium Web Publication Manifest.

This profile relies on:

* a declaration of [conformance with this Profile](#1-declaring-conformance-with-the-epub-profile),
* some [restrictions on the resources of the readingOrder](#2-restrictions-on-the-resources-of-the-reading-order),
* the definition of additional [collection roles](#3-collection-roles),
* the definition of additional [Link properties](#4-link-properties),

## 1. Declaring conformance with the EPUB Profile

To declare that it conforms to the EPUB Profile, a Readium Web Publication Manifest <strong class="rfc">must</strong> include a `conformsTo` key in its `metadata` section, with `https://readium.org/webpub-manifest/profiles/epub` as value.

## 2. Restrictions on resources in the reading order

A Readium Web Publication Manifest that conforms to the EPUB Profile <strong class="rfc">must</strong> strictly reference XHTML documents (`application/xhtml+xml`) in its `readingOrder`.

While EPUB itself allows SVG and other formats as long as an XHTML fallback is provided, this is not the case for this profile, which requires to reverse the fallback chain.

## 3. Collection Roles

This profile defines additional collection roles, which map EPUB specific properties: 

| Role  | Semantics | Compact? | Required? |
| ----- | --------- | -------- | --------- |
| landmarks  | Identifies the collection that contains a list of points of interest.  | Yes  | No  |
| loa  | Identifies the collection that contains a list of audio resources.  | Yes  | No  |
| loi  | Identifies the collection that contains a list of illustrations.  | Yes  | No  |
| lot  | Identifies the collection that contains a list of tables.  | Yes  | No  |
| lov  | Identifies the collection that contains a list of videos.  | Yes  | No  |
| pageList  | Identifies the collection that contains a list of pages.  | Yes  | No  |

## 4. Link Properties

This profile defines additional Link properties, which map EPUB specific properties: 

| Key   | Semantics | Type     | Values    | 
| ----- | --------- | -------- | --------- | 
| [contains](#contains)  | Identifies content contained in the linked resource, that cannot be strictly identified using a media type.  | Array  | `mathml`, `onix`, `remote-resources`, `js`, `svg` or `xmp`  | 
| [layout](#layout)  | Hint about the nature of the layout for the linked resources.  | String  | `fixed` or `reflowable`  | 

### contains

While the media type is the main way to identify the nature of a resource in a Link Object, in certain cases it isn't sufficient because:

* a number of metadata standards either rely on XML and JSON without defining a specific media type (ONIX, XMP);
* the media type doesn't indicate if an HTML/XHTML resource contains MathML, SVG or Javascript, or if some of its resources are not available in the package.

`contains` is meant to convey that information in the Properties Object using an array of string values.

```
{
  "href": "record.xml", "rel": "record", "type": "application/xml",
  "properties": {
    "contains": ["onix"]
  }
}
```

```
{
  "href": "chapter1.html", "type": "text/html",
  "properties": {
    "contains": ["svg", "remote-resources"]
  }
}
```

### layout

The `layout` property defaults to `reflowable` for text resources and `fixed` for images or videos.

Using `fixed` it can also indicate that an HTML document has a viewport with a fixed size.

```
{
  "href": "page1.html", "type": "text/html",
  "properties": {
    "layout": "fixed"
  }
}
```