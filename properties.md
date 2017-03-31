# The Properties Object

Each Link Object may contain a Properties Object, containing a number of relevant information.

This document is meant to provide an exhaustive list of properties that can be associated to a Link Object, along with their semantics and usage.

## Core Properties

| Key  | Semantics | Type | Values |
| ----- | --------- | -------- | --------- |
| [orientation](#orientation)  | Suggested orientation for the device when displaying the linked resource.  | String  | `auto`, `landscape` or `portrait`  |
| [page](#page)  | Indicates how the linked resource should be displayed in a reading environment that displays synthetic spreads.  | String  | `left`, `right` or `center`  |

## Extensions

| Key   | Semantics | Type     | Values    | Reference |
| ----- | --------- | -------- | --------- | --------- |
| [contains](/extensions/epub.md#contains)  | Indentifies content contained in the linked resource, that cannot be strictly identified using a media type.  | Array  | `mathml`, `onix`, `remote-resources`, `js`, `svg` or `xmp`  | [EPUB extension](/extensions/epub.md#properties) |
| [encrypted](/extensions/epub.md#encrypted)  | Indicates that a resource is encrypted/obfuscated and provides relevant information for decryption.  | [Encryption Object](/extensions/epub.md#encrypted)  | See the definition for the [Encryption Object](/extensions/epub.md#encrypted) | [EPUB extension](/extensions/epub.md#properties) |
| [layout](/extensions/epub.md#layout)  | Hint about the nature of the layout for the linked resources.  | String  | `fixed` or `reflowable`  | [EPUB extension](/extensions/epub.md#properties) |
| [media-overlay](/extensions/epub.md#media-overlay)  | Location of a media-overlay for the resource referenced in the Link Object.  | URI  | Any valid relative or absolute URI  | [EPUB extension](/extensions/epub.md#properties) |
| [overflow](/extensions/epub.md#overflow)  | Suggested method for handling overflow while displaying the linked resource.  | String  | `auto`, `paginated`, `scrolled` or `scrolled-continuous`  | [EPUB extension](/extensions/epub.md#properties) |
| [spread](/extensions/epub.md#spread)  | Indicates the condition to be met for the linked resource to be rendered within a synthetic spread. | String  | `auto`, `both`, `none` or `landscape`  | [EPUB extension](/extensions/epub.md#properties) |

## Definitions

### orientation

The `orientation` property defaults to `auto` and is mostly relevant for fixed layout resources, where the orientation has an actual impact on how the resource is displayed.

```
{
  "href": "page1.html", "type": "text/html",
  "properties": {
    "layout": "fixed",
    "orientation": "landscape"
  }
}
```

### page



```
[
  {
    "href": "page1.jpg", "type": "image/jpeg",
    "properties": {
      "page": "left"
    }
  },
  {
    "href": "page2.jpg", "type": "image/jpeg",
    "properties": {
      "page": "right"
    }
  }
]
```
