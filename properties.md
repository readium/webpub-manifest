# The Properties Object

Each Link Object may contain a Properties Object, containing a number of relevant information.

This document is meant to provide an exhaustive list of properties that can be associated to a Link Object, along with their semantics and usage.

## Extensions

| Key   | Semantics | Type     | Values    | Reference |
| ----- | --------- | -------- | --------- | --------- |
| [`contains`](/profiles/epub.md#contains)  | Indentifies content contained in the linked resource, that cannot be strictly identified using a media type.  | Array  | `mathml`, `onix`, `remote-resources`, `js`, `svg` or `xmp`  | [EPUB Profile](/profiles/epub.md#properties) |
| [`layout`](/profiles/epub.md#layout)  | Hint about the nature of the layout for the linked resources.  | String  | `fixed` or `reflowable`  | [EPUB Profile](/profiles/epub.md#properties) |
| [`orientation`](/modules/presentation.md#orientation)  | Suggested orientation for the device when displaying the linked resource.  | String  | `auto`, `landscape` or `portrait`  | [Presentation Hints](/modules/presentation.md) |
| [`overflow`](/modules/presentation.md#overflow)  | Suggested method for handling overflow while displaying the linked resource.  | String  | `auto`, `clipped`, `paginated` or `scrolled`  | [Presentation Hints](/modules/presentation.md) |
| [`page`](/modules/presentation.md#page)  | Indicates how the linked resource should be displayed in a reading environment that displays synthetic spreads.  | String  | `left`, `right` or `center`  |  [Presentation Hints](/modules/presentation.md) |
| [`spread`](/modules/presentation.md#spread)  | Indicates the condition to be met for the linked resource to be rendered within a synthetic spread. | String  | `auto`, `both`, `none` or `landscape`  | [Presentation Hints](/modules/epub.md#properties) |
| [`encrypted`](/modules/encryption.md)  | Indicates  how a given resource has been encrypted or obfuscated.  | [Encryption Object](/modules/encryption.md#encryption-object)  | See the definition of the Encryption Object | [Encryption Module](/modules/encryption.md) |

## OPDS 2.0

While OPDS 2.0 itself is not an extension of the Readium Web Publication Manifest, it shares the same abstract model and syntax.

For this reason, it feels relevant to list the properties introduced by OPDS as well.

| Key   | Semantics | Reference |
| ----- | --------- | --------- |
| `indirectAcquisition`  | Provides the expected download format for a publication, after an acquisition through an intermediate resource. | [OPDS 2.0](https://drafts.opds.io/opds-2.0#43-acquisition-links) | 
| `numberOfItems`  | Hint about the number of items that are expected to be returned by the linked resource.  | [OPDS 2.0](https://drafts.opds.io/opds-2.0#14-facets) | 
| `price`  | Provides the acquisition price in a given currency.  | [OPDS 2.0](https://drafts.opds.io/opds-2.0#43-acquisition-links) | 