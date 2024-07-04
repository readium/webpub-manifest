# The Properties Object

Each Link Object may contain a Properties Object, containing a number of relevant information.

This document is meant to provide an exhaustive list of properties that can be associated to a Link Object, along with their semantics and usage.


## Core properties

| Key   | Semantics | Type     | Values    |
| ----- | --------- | -------- | --------- |
| [`page`](#page)  | Indicates how the linked resource should be displayed in a reading environment that displays synthetic spreads.  | String  | `left`, `right` or `center`  | 

### page

The `page` property is meant to provide a hint to reading systems that rely on synthetic spreads to display more than a single resource at once.


*In this example, the first page should be displayed of the left of a synthetic spread, the second page on the right.*

```json
"readingOrder": [
  {
    "href": "page1.jpg", 
    "type": "image/jpeg",
    "properties": {
      "page": "left"
    }
  },
  {
    "href": "page2.jpg", 
    "type": "image/jpeg",
    "properties": {
      "page": "right"
    }
  }
]
```


## Extensions

| Key   | Semantics | Type     | Values    | Reference |
| ----- | --------- | -------- | --------- | --------- |
| [`breakScrollBefore`](/profiles/divina.md#42-scrolled-publications) | Hint that the current resource should be presented in a spread to fully convey the meaning of its content. | Boolean  | Defaults to `false`  | [Divina Profile](/profiles/divina.md) |
| [`contains`](/profiles/epub.md#contains) | Indentifies content contained in the linked resource, that cannot be strictly identified using a media type.  | Array  | `mathml`, `onix`, `remote-resources`, `js`, `svg` or `xmp`  | [EPUB Profile](/profiles/epub.md#properties) |
| [`encrypted`](/modules/encryption.md)  | Indicates  how a given resource has been encrypted or obfuscated.  | [Encryption Object](/modules/encryption.md#encryption-object)  | See the definition of the Encryption Object | [Encryption Module](/modules/encryption.md) |
| [`layout`](/profiles/epub.md#layout)  | Hint about the nature of the layout for the linked resources.  | String  | `fixed` or `reflowable`  | [EPUB Profile](/profiles/epub.md#properties) |
| [`meaningfulSpread`](/profiles/divina.md#41-fixed-layout) | Specifies that an item in the reading order should break the current continuous scroll and start a new one. | Boolean  | Defaults to `false`  | [Divina Profile](/profiles/divina.md) |

## OPDS 2.0

While OPDS 2.0 itself is not an extension of the Readium Web Publication Manifest, it shares the same abstract model and syntax.

For this reason, it feels relevant to list the properties introduced by OPDS as well.

| Key   | Semantics | Reference |
| ----- | --------- | --------- |
| `indirectAcquisition`  | Provides the expected download format for a publication, after an acquisition through an intermediate resource. | [OPDS 2.0](https://drafts.opds.io/opds-2.0#43-acquisition-links) | 
| `numberOfItems`  | Hint about the number of items that are expected to be returned by the linked resource.  | [OPDS 2.0](https://drafts.opds.io/opds-2.0#14-facets) | 
| `price`  | Provides the acquisition price in a given currency.  | [OPDS 2.0](https://drafts.opds.io/opds-2.0#43-acquisition-links) | 