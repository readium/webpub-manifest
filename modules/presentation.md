# Advanced Divina Presentation Hints

The Presentation Hints extension defines a number of hints for User Agents about the way content <strong class="rfc">should</strong> be presented to the user.

## 1. Presentation Hints in `metadata`

In order to provide publication-wide Presentation Hints, this extension introduces a new element in the `metadata` object of [the Readium Web Publication Manifest](https://readium.org/webpub-manifest):

| Key   | Semantics | Type     | 
| ----- | --------- | -------- |
| `presentation` | Publication-wide presentation hints. | Object |

The following elements <strong class="rfc">may</strong> be included in `presentation`:

- [`clipped`](#clipped)
- [`fit`](#fit)
- [`orientation`](#orientation)

## 2. Presentation Hints as Link Properties

In addition to publication-wide hints, this extension defines a number of Link Properties to provide resource-level hints in `readingOrder` or `resources`.

The following elements <strong class="rfc">may</strong> be included in `properties`:

- [`clipped`](#clipped)
- [`fit`](#fit)
- [`orientation`](#orientation)

## 3. Properties

### clipped

The `clipped` property is meant to adapt visual resources to any given viewport ratio. The clipped areas <strong class="rfc">must not</strong> contain information which are mandatory for the comprehension of the resource. 

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `clipped` | Specifies whether or not the parts of a linked resource that flow out of the viewport are clipped.  | Boolean  | `true` or `false` | `false` |

*In this example, all resources are scaled to fit the viewport height and clipped to fit different viewport widths. It behaves like turbomedia.*

```json
"metadata": {
  "readingProgression": "ttb",
  "presentation": {
    "fit": "height",
    "clipped": true
  }
}
```

*In this example, a specific resource is scaled to fit the viewport width and clipped to fit different viewport heights.*

```json
"readingOrder": [
  {
    "href": "image1.avif",
    "type": "image/avif",
    "properties": {
      "fit": "width",
      "clipped": true
    }
  }
]
```

### fit

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `fit` | Specifies constraints for the presentation of a linked resource within the viewport.  | String  | `contain`, `cover`, `width` or `height` | `contain` |

| Value   | Definition |
| ------- | ---------- |
| `contain` | The content is centered and scaled to fit both dimensions into the viewport. |
| `cover`  |  The content is centered and scaled to fill the viewport. |
| `width`  |  The content is centered and scaled to fit the viewport width. |
| `height` |  The content is centered and scaled to fit the viewport height. |

*In this example, a specific resource is scaled to fit the viewport.*

```json
"readingOrder": [
  {
    "href": "image1.avif",
    "type": "image/avif",
    "properties": {
      "fit": "contain"
    }
  }
]
```

### orientation

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `orientation` | Suggested orientation for the device when displaying the linked resource.  | String  | `landscape`, `portrait` or `auto`  | `auto` |

The `orientation` property is mostly relevant for resources with fixed dimensions (images, videos), where the orientation has an actual impact on how the resource is displayed.

*In this example, all resources should be displayed in portrait mode.*

```json
"metadata": {
  "presentation": {
    "orientation": "portrait"
  }
}
```
*In this example, a specific resource should be displayed in landscape mode.*

```json
"readingOrder": [
  {
    "href": "page1.html", 
    "type": "text/html",
    "properties": {
      "orientation": "landscape"
    }
  }
]
```

## Appendix A - JSON Schema

The following JSON Schemas for this module are available under version control: 

- Metadata: <https://github.com/readium/webpub-manifest/blob/master/schema/extensions/presentation/metadata.schema.json>
- Link Properties: <https://github.com/readium/webpub-manifest/blob/master/schema/extensions/presentation/properties.schema.json>

For the purpose of validating a Readium Web Publication Manifest, use the following JSON Schema resources: 

- <https://readium.org/webpub-manifest/schema/extensions/presentation/metadata.schema.json>
- <https://readium.org/webpub-manifest/schema/extensions/presentation/properties.schema.json>