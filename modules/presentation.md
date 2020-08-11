# Presentation Hints

The Presentation Hints extension defines a number of hints for User Agents about the way content <strong class="rfc">should</strong> be presented to the user.

## 1. Presentation Hints in `metadata`

In order to provide publication-wide Presentation Hints, this extension introduces a new element in the `metadata` object of [the Readium Web Publication Manifest](https://readium.org/webpub-manifest):

| Key   | Semantics | Type     | 
| ----- | --------- | -------- |
| `presentation` | Publication-wide presentation hints. | Object |

The following elements <strong class="rfc">may</strong> be included in `presentation`:

- [`clipped`](#clipped)
- [`continuous`](#continuous)
- [`fit`](#fit)
- [`orientation`](#orientation)
- [`overflow`](#overflow)
- [`spread`](#spread)

## 2. Presentation Hints as Link Properties

In addition to publication-wide hints, this extension defines a number of Link Properties to provide resource-level hints in `readingOrder` or `resources`.

The following elements <strong class="rfc">may</strong> be included in `properties`:

- [`clipped`](#clipped)
- [`fit`](#fit)
- [`orientation`](#orientation)
- [`page`](#page)
- [`spread`](#spread)

## 3. Properties

### clipped

The `clipped` property is meant to adapt visual resources to any given viewport ratio. The clipped areas <strong class="rfc">must not</strong> contain information which are mandatory for the comprehension of the resource. 

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `clipped` | Specifies whether or not the parts of a linked resource that flow out of the viewport are clipped.  | Boolean  | `true` or `false` | `false` |

*In this example, resources are handled in a discontinuous way and each resource is scaled to fit the viewport height and clipped to fit different viewport widths. It behaves like turbomedia.*

```json
"metadata": {
  "readingProgression": "ttb",
  "presentation": {
    "continuous": false,
    "fit": "height",
    "clipped": true

  }
}
```

*In this example, a specific resource is scaled to fit the viewport width and clipped to fit different viewport heights.*

```json
"readingOrder": [
  {
    "href": "image1.webp",
    "type": "image/webp",
    "properties": {
      "fit": "width",
      "clipped": true
    }
  }
]
```

### continuous

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `continuous` | Indicates if consecutive linked resources from the `readingOrder` should be handled in a continuous or discontinuous way.  | Boolean  | `true` or `false`  | `true` |

*In this example, the user will not experience discontinuities between the different resources*

```json
"metadata": {
  "presentation": {
    "continuous": true
  }
}
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

*In this example, resources are handled in a continuous way, the content is scrollable on the vertical axis and each resource fits the viewport width. It behaves like a webtoon.*

```json
"metadata": {
  "readingProgression": "ttb",
  "presentation": {
    "continuous": true,
    "overflow": "scrolled",
    "fit": "width"
  }
}
```
*In this example, a specific resource is scaled to fit the viewport.*

```json
"readingOrder": [
  {
    "href": "image1.webp",
    "type": "image/webp",
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

*In this example, each resource should be displayed in portrait mode.*

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

### overflow

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `overflow` |  Indicates if the overflow of linked resources from the `readingOrder` or `resources` should be handled using dynamic pagination or scrolling.  | String  | `paginated`, `scrolled` or `auto` | `auto` |


| Value   | Definition |
| ------- | ---------- |
| `paginated` | Content overflow should be handled using dynamic pagination. |
| `scrolled` | Content overflow should be handled using scrolling. |
| `auto` | The User Agent can decide how overflow should be handled.  |

*Here is an example of a paginated mode requested by the author*

```json
"metadata": {
  "readingProgression": "ltr",
  "presentation": {
    "continuous": true,
    "overflow": "paginated"
  }
}
```

### page

The `page` property is meant to provide a hint to Use Agents that rely on synthetic spreads to display more than a single resource at once.

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `page` | Indicates how the linked resource should be displayed in a reading environment that displays synthetic spreads.  | String  | `left`, `right` or `center` | None |

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

### spread

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `spread` | Indicates the condition to be met for the linked resource to be rendered within a synthetic spread. | String  | `landscape`, `both`, `none` or `auto`  | `auto` |

| Value   | Definition |
| ------- | ---------- |
| `landscape` | The resource should be displayed in a spread only if the device is in landscape mode. |
| `both` | The resource should be displayed in a spread whatever the device orientation is. |
| `none` | The resource should never be displayed in a spread. |
| `auto` | The resource is left to the User Agent. |

*In this example, content should be displayed in a spread only if the device is in landscape mode.*

```json
"metadata": {
  "presentation": {
    "spread": "landscape",
    "fit": "contain"
  }
}
```
*In this example, content of these two pages should be displayed in a spread, one on the left, the other on the right, whatever the device orientation is.*

```json
"readingOrder": [
  {
    "href": "page1.jpg", 
    "type": "image/jpeg",
    "properties": {
      "spread": "both",
      "page": "left"
    }
  },
  {
    "href": "page2.jpg", 
    "type": "image/jpeg",
    "properties": {
      "spread": "both",
      "page": "right"
    }
  }
]
```

## Appendix A - JSON Schema

The following JSON Schemas for this module are available under version control: 

- Metadata: https://github.com/readium/webpub-manifest/blob/master/schema/extensions/presentation/metadata.schema.json
- Link Properties: https://github.com/readium/webpub-manifest/blob/master/schema/extensions/presentation/properties.schema.json

For the purpose of validating a Readium Web Publication Manifest, use the following JSON Schema resources: 

- https://readium.org/webpub-manifest/schema/extensions/presentation/metadata.schema.json
- https://readium.org/webpub-manifest/schema/extensions/presentation/properties.schema.json