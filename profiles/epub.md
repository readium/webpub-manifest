# EPUB Profile

## Introduction

While EPUB 2.x and 3.x can mostly be mapped directly to the Readium Web Publication Manifest, a number of metadata, collection roles and properties are still very specific to EPUB and not fully covered by the core specification.

This profile relies on:

* a declaration of [conformance with this Profile](#1-declaring-conformance-with-the-epub-profile),
* some [restrictions on the resources of the readingOrder](#2-restrictions-on-the-resources-of-the readingorder),
* the definition of additional [collection roles](#3-collection-roles),
* the definition of additional [Link properties](#4-link-properties),
* the use of the [encryption module](../modules/encryption.md).

## 1. Declaring conformance with the EPUB Profile

In order to declare that it conforms to the EPUB Profile, a Readium Web Publication Manifest <strong class="rfc">must</strong> include a `conformsTo` key in its `metadata` section, with `https://readium.org/webpub-manifest/profiles/epub` as value.

## 2. Restrictions on the resources of the `readingOrder`

A Readium Web Publication Manifest that conforms to the EPUB Profile <strong class="rfc">must</strong> strictly reference XHTML documents (`application/xhtml+xml`) in its `readingOrder`.

## 3. Collection Roles

This profile defines additional collection roles: 

| Role  | Semantics | Compact? | Required? |
| ----- | --------- | -------- | --------- |
| landmarks  | Identifies the collection that contains a list of points of interest.  | Yes  | No  |
| loa  | Identifies the collection that contains a list of audio resources.  | Yes  | No  |
| loi  | Identifies the collection that contains a list of illustrations.  | Yes  | No  |
| lot  | Identifies the collection that contains a list of tables.  | Yes  | No  |
| lov  | Identifies the collection that contains a list of videos.  | Yes  | No  |
| pageList  | Identifies the collection that contains a list of pages.  | Yes  | No  |

> **Note**: Do we document various EPUB extensions and associated roles? This would have to be extended to index and dictionaries if we're only considering specs that were officially adopted.

## 4. Link Properties

This profile defines additional Link properties: 

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