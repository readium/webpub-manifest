# EPUB Module

While EPUB 2.x and 3.x can mostly be mapped directly to the Readium Web Publication Manifest, a number of metadata, collection roles and properties are still very specific to EPUB and not fully covered by the core specification.

Thanks to the various extension points in place, this document defines a number of new collection roles and properties that are for the most part exclusive to EPUB.

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


## Properties

| Key   | Semantics | Type     | Values    | 
| ----- | --------- | -------- | --------- | 
| [contains](#contains)  | Identifies content contained in the linked resource, that cannot be strictly identified using a media type.  | Array  | `mathml`, `onix`, `remote-resources`, `js`, `svg` or `xmp`  | 
| [encrypted](#encrypted)  | Indicates that a resource is encrypted/obfuscated and provides relevant information for decryption.  | [Encryption Object](#encrypted)  | See the definition for the [Encryption Object](#encrypted) | 
| [layout](#layout)  | Hint about the nature of the layout for the linked resources.  | String  | `fixed` or `reflowable`  | 

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
| [originalLength](#originalLength)  | Original length of the resource in bytes before compression and/or encryption. | Integer  | No |
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
      "originalLength": 13810
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