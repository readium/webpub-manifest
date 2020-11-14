# EPUB Profile

## 1. Introduction

While EPUB 2.x and 3.x can mostly be mapped directly to the Readium Web Publication Manifest, a number of metadata, collection roles and properties are still very specific to EPUB and not fully covered by the core specification.

This profile relies on:

* the definition of new [collection roles](#2-collection-roles),
* the definition of new [resource properties](#3-resource-properties),
* the [encryption module](../modules/encryption.md).


## 2. Collection Roles

> **Note**: Do we document various EPUB extensions and associated roles? This would have to be extended to index and dictionaries if we're only considering specs that were officially adopted.

| Role  | Semantics | Compact? | Required? |
| ----- | --------- | -------- | --------- |
| landmarks  | Identifies the collection that contains a list of points of interest.  | Yes  | No  |
| loa  | Identifies the collection that contains a list of audio resources.  | Yes  | No  |
| loi  | Identifies the collection that contains a list of illustrations.  | Yes  | No  |
| lot  | Identifies the collection that contains a list of tables.  | Yes  | No  |
| lov  | Identifies the collection that contains a list of videos.  | Yes  | No  |
| pageList  | Identifies the collection that contains a list of pages.  | Yes  | No  |


## 3. Resource Properties

| Key   | Semantics | Type     | Values    | 
| ----- | --------- | -------- | --------- | 
| [contains](#contains)  | Identifies content contained in the linked resource, that cannot be strictly identified using a media type.  | Array  | `mathml`, `onix`, `remote-resources`, `js`, `svg` or `xmp`  | 
| [layout](#layout)  | Hint about the nature of the layout for the linked resources.  | String  | `fixed` or `reflowable`  | 

### 3.1. contains

While the media type is the main way to identify the nature of a resource in a Link Object, in certain cases it isn't sufficient enough:

* a number of metadata standards either rely on XML and JSON without defining a specific media type (ONIX, XMP)
* the media type doesn't indicate if an HTML/XHTML resource relies on MathML, SVG or Javascript, or if some of its resources are not available in the package 

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

### 3.2. layout

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