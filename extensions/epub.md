# EPUB Extension

While EPUB 2.x and 3.x can mostly be mapped directly to the Readium Web Publication Manifest, a number of metadata, collection roles and properties are still very specific to EPUB and not fully covered by the core specification.

Thanks to the various extension points in place, this document defines a number of new properties that are for the most part exclusive to EPUB.

## Metadata

[An additional JSON-LD context](../contexts/epub) document is available to cover EPUB specific requirements.

This document is registered in the [Context Documents registry](../contexts), in compliance with the extensibility rules for metadata.

## Collection Roles

> **Note**: Do we document various EPUB extensions and associated roles? This would have to be extended to index and dictionaries if we're only considering specs that were officially adopted.

| Role  | Semantics | Compact? | Required? |
| ----- | --------- | -------- | --------- |
| landmarks  | Identifies the collection that contains a list of points of interest.  | Yes  | No  |
| loa  | Identifies the collection that contains a list of audio resources.  | Yes  | No  |
| loi  | Identifies the collection that contains a list of illustrations.  | Yes  | No  |
| lot  | Identifies the collection that contains a list of tables.  | Yes  | No  |
| lov  | Identifies the collection that contains a list of videos.  | Yes  | No  |
| page-list  | Identifies the collection that contains a list of pages.  | Yes  | No  |

## Extension to the Link Object

Specific collection roles in this EPUB extension require the Link Object to be extended to support nesting.

For this purpose, this extension introduces the `children` element:

| Name      | Semantics     | Format        |
| --------- | ------------- | ------------- |
| children  | Contains one or more Link Objects that are direct descendents of the linked resource.    | Link Object   |

This new element is meant primarly for the `toc` collection role but can be useful for other extensions of the Readium Web Publication Manifest.

*Example of a table of contents expressed with nested Link Objects*

```json
"toc": [
  {
    "href": "part1.xhtml",
    "title": "Part I",
    "children": [
      {
        "href": "chapter1.xhtml",
        "title": "Chapter 1"
      },
      {
        "href": "chapter2.xhtml",
        "title": "Chapter 2"
      }
    ]
  }
]
```


## Properties

| Key   | Semantics | Type     | Values    | 
| ----- | --------- | -------- | --------- | 
| [contains](#contains)  | Identifies content contained in the linked resource, that cannot be strictly identified using a media type.  | Array  | `mathml`, `onix`, `remote-resources`, `js`, `svg` or `xmp`  | 
| [encrypted](#encrypted)  | Indicates that a resource is encrypted/obfuscated and provides relevant information for decryption.  | [Encryption Object](#encrypted)  | See the definition for the [Encryption Object](#encrypted) | 
| [layout](#layout)  | Hint about the nature of the layout for the linked resources.  | String  | `fixed` or `reflowable`  | 
| [media-overlay](#media-overlay)  | Location of a media-overlay for the resource referenced in the Link Object.  | URI  | Any valid relative or absolute URI  | 
| [overflow](#overflow)  | Suggested method for handling overflow while displaying the linked resource.  | String  | `auto`, `paginated`, `scrolled` or `scrolled-continuous`  | 
| [spread](#spread)  | Indicates the condition to be met for the linked resource to be rendered within a synthetic spread. | String  | `auto`, `both`, `none` or `landscape`  | 

### contains

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

### encrypted

The `encrypted` key contains an Encryption Object that indicates how a given resource is encrypted/obfuscated.

The Encryption Object has the following keys:

| Key   | Semantics | Type     | Required? |
| ----- | --------- | -------- | --------- |
| [algorithm](#algorithm)  | Identifies the algorithm used to encrypt the resource.  | URI  | Yes |
| [compression](#compression)  | Compression method used on the resource.  | String  | No |
| [original-length](#original-length)  | Original length of the resource in bytes before compression and/or encryption. | Integer  | No |
| [profile](#profile)  | Identifies the encryption profile used to encrypt the resource.  | URI  | No |
| [scheme](#scheme)  | Identifies the encryption scheme used to encrypt the resource.  | URI  | No |

*Example for an obfuscated font*

```json
{
  "href": "fonts/sandome.obf.ttf",
  "type": "application/vnd.ms-opentype",
  "properties": {
    "encrypted": {
      "algorithm": "http://www.idpf.org/2008/embedding"
    }
  }
}
```

*Example for a resource encrypted using LCP*

```json
{
  "href": "chapter_001.xhtml",
  "type": "application/xhtml+xml",
  "properties": {
    "encrypted": {
      "scheme": "http://readium.org/2014/01/lcp",
      "profile": "http://readium.org/lcp/basic-profile",
      "algorithm": "http://www.w3.org/2001/04/xmlenc#aes256-cbc",
      "compression": "deflate",
      "original-length": 13810
    }
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

### media-overlay

The `media-overlay` property indicates that the linked resource has a media-overlay available and points to the location of that media-overlay using a URI.

This specification does not define the expected format for that media-overlay.

```
{
  "href": "chapter1.html", "type": "text/html",
  "properties": {
    "media-overlay": "media-overlay/chapter1.json"
  }
}
```


### overflow

```
{
  "href": "endnotes.html", "type": "text/html",
  "properties": {
    "overflow": "scrolled"
  }
}
```


### spread

The `spread` property is meant to indicate to the reading system the condition for displaying the linked resource in a synthetic spread.

This only applies to fixed layout resources and defaults to `auto`.


```
[
  {
    "href": "page1.html", "type": "text/html",
    "properties": {
      "layout": "fixed",
      "spread": "both",
      "page": "left"
    }
  },
  {
    "href": "page2.html", "type": "text/html",
    "properties": {
      "layout": "fixed",
      "spread": "both",
      "page": "right"
    }
  }
]
```

