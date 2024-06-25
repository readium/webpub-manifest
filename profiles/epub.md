# EPUB Profile

**Editors:**

* Hadrien Gardeur

**Participate:**

* [GitHub readium/webpub-manifest](https://github.com/readium/webpub-manifest)
* [File an issue](https://github.com/readium/webpub-manifest/issues)

## Introduction

While EPUB publications can mostly be converted directly to the Readium Web Publication Manifest, a number of collection roles and properties remain specific to EPUB.

This profile is meant to facilitate backward compatibility with EPUB and ensure that these specialized elements are not lost when converting to the Readium Web Publication Manifest.


## 1. Declaring conformance with the EPUB Profile

In order to declare that it conforms to the EPUB Profile, a Readium Web Publication Manifest <strong class="rfc">must</strong> include a `conformsTo` element that contains `https://readium.org/webpub-manifest/profiles/epub` as one of its values.

## 2. Restrictions on resources in the `readingOrder`

A Readium Web Publication Manifest that conforms to the EPUB Profile <strong class="rfc">must</strong> strictly reference XHTML documents (`application/xhtml+xml`) in its `readingOrder`.

While EPUB itself allows SVG and other formats as long as an XHTML fallback is provided, this is not the case for this profile, which requires to reverse the fallback chain.

## 3. Layout

The [`layout`](../contexts/default/README.md#layout-and-reading-progression) of a publication that conforms to the EPUB profile defaults to `reflowable`.

This profile also supports fixed layout publications where the `layout` property is set to `fixed` for the entire publication.

This behaviour can be overriden by specific resources in the reading order using the `layout` property, [as defined in this document](#layout).

## 4. Collection Roles

| Role  | Semantics | Compact? | Required? |
| ----- | --------- | -------- | --------- |
| landmarks  | Identifies the collection that contains a list of points of interest.  | Yes  | No  |
| loa  | Identifies the collection that contains a list of audio resources.  | Yes  | No  |
| loi  | Identifies the collection that contains a list of illustrations.  | Yes  | No  |
| lot  | Identifies the collection that contains a list of tables.  | Yes  | No  |
| lov  | Identifies the collection that contains a list of videos.  | Yes  | No  |
| pageList  | Identifies the collection that contains a list of pages.  | Yes  | No  |

## 5. Metadata

| Key   | Semantics | Type     |
| ----- | --------- | -------- |
| [mediaOverlay](#mediaoverlay)  | Contains optional Media Overlay specific metadata. | Media Overlay Object |

### mediaOverlay

| Key   | Semantics | Type     |
| ----- | --------- | -------- |
| activeClass  | Author-defined CSS class name to apply to the currently-playing EPUB Content Document element. | CSS Class Name |
| playbackActiveClass  | Author-defined CSS class name to apply to the EPUB Content Document's document element when playback is active. | CSS Class Name |

The EPUB specification provides dedicated elements for Media Overlay with:

- the total duration of a Media Overlay publication
- the duration of specific SMIL files
- and an optional narrator

In the context of the Readium Web Publication Manifest, these dedicated elements are superseded by:

- the use of [`duration`](../contexts/default/#duration-and-number-of-pages) in `metadata` for the total duration of a publication
- the use of [`duration`](../#24-the-link-object) in Link Objects for the duration of specific resources (SMIL, SMIL-equivalent or media resources)
- and a dedicated element for [`narrator`](../contexts/default/#contributors)


## 6. Link Properties

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

By default, each item in the `readingOrder` follows the `layout` specified in `metadata` but the EPUB profile allow content creators to override this behaviour using the `layout` property on specific resources.


```
{
  "href": "page1.html", "type": "text/html",
  "properties": {
    "layout": "fixed"
  }
}
```

## Appendix A - JSON Schema

The following JSON Schema resources for this profile are available under version control: 

- Metadata: <https://github.com/readium/webpub-manifest/blob/master/schema/extensions/epub/metadata.schema.json>
- Collection roles: <https://github.com/readium/webpub-manifest/blob/master/schema/extensions/epub/subcollections.schema.json>
- Link properties: <https://github.com/readium/webpub-manifest/blob/master/schema/extensions/epub/properties.schema.json>


For the purpose of validating a Readium Web Publication Manifest, use the following JSON Schema resources: 

- <https://readium.org/webpub-manifest/schema/extensions/epub/metadata.schema.json>
- <https://readium.org/webpub-manifest/schema/extensions/epub/subcollections.schema.json>
- <https://readium.org/webpub-manifest/schema/extensions/epub/properties.schema.json>

## Appendix B - Deprecated properties

### `layout` in a `presentation` object

This specification was initially responsible for documenting the layout of an entire publication using either: `reflowable` or `fixed`.

This was handled using a `layout` property in a `presentation` object. 

```json
"metadata": {
  "title": "Bella the dragon",
  "presentation": {
    "layout": "fixed
  }
}
```

This is no longer supported by this profile because `layout` became a first-class metadata defined in the default context.

```json
"metadata": {
  "title": "Bella the dragon",
  "layout": "fixed
}
```

### Orientation

The EPUB specification allow content creators to specify the intended orientation of:

- [an entire publication](https://www.w3.org/TR/epub-33/#orientation)
- [or a specific resource](https://www.w3.org/TR/epub-33/#orientation-overrides)

This was originally supported in this EPUB profile through:

- an `orientation` property for the `presentation` object in `metadata` (for the entire publication)
- and an `orientation` property in the `properties` of a Link Object (for a specific resource)

This is no longer supported in Readium because forcing an orientation goes against the principles that we're trying to follow, where the user is always free to decide what feels best for them.

### Spreads

The EPUB specification allow content creators to specify when synthetic spreads should be presented to the user for a fixed layout document either for:

- [an entire publication](https://www.w3.org/TR/epub-33/#spread)
- [or a specific resource](https://www.w3.org/TR/epub-33/#spread-overrides)

This was originally supported in this EPUB profile through:

- a `spread` property for the `presentation` object in `metadata` (for the entire publication)
- and a `spread` property in the `properties` of a Link Object (for a specific resource)

This is no longer supported in Readium because forcing spreads goes against the principles that we're trying to follow, where the user is always free to decide what feels best for them.