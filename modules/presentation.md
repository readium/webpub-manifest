# Presentation Hints

The Presentation Hints module defines a number of hints for User Agents about the way content <strong class="rfc">should</strong> be presented to the user.

## 1. Presentation Hints in `metadata`

In order to provide publication-wide Presentation Hints, this extension introduces a new element in the `metadata` object of [the Readium Web Publication Manifest](https://readium.org/webpub-manifest):

| Key   | Semantics | Type     | 
| ----- | --------- | -------- |
| `presentation` | Publication-wide presentation hints. | Object |

The following elements <strong class="rfc">may</strong> be included in the `presentation` object:

- [`viewportRatio`](#viewportratio)
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

### viewportRatio

By default, the viewport is the whole available screen space. The optional `viewportRatio` object constrains the viewport, possibly forcing the apparition of bar scopes.

This specification defines the following keys for this JSON object:

| Key  | Definition | Format | Default | Required? |
| ---- | -----------| -------| --------| ----------|
| `constraint`  | Type of constraint  | `exact`, `min`, `max` | `exact` | Yes |
| `aspectRatio` | Aspect ratio | `X:Y`, where X and Y are numbers (e.g. "16:9") | `exact` | Yes |

*In this example, resources fit a 16:9 viewport. The publication behaves like a turbomedia. Vertical or horizontal barscopes may appear in the screen ratio is not 16:9. *

```json
"metadata": {
  "readingProgression": "ltr",
  "presentation": {
    "continuous": false,
    "viewportRatio": {
      "constraint": "exact",
      "aspectRatio": "16:9"
    }
  }
}
```

*In this example, resources fit a viewport which fills the total height of the screen and has a maximum ratio of 1:2 (one unit / width for two units / height). The publication behaves like a webtoon. Vertical barscopes will appear if the screen ratio is larger than 1:2, which is especially the case on a desktop screen (where screen orientation has no meaning). Horizontal barscopes will never show.*

```json
"metadata": {
  "readingProgression": "ttb",
  "presentation": {
    "continuous": true,
    "orientation": "portrait",
    "overflow": "scrolled",
    "fit": "width",
    "viewportRatio": {
      "constraint": "max",
      "aspectRatio": "1:2"
    }
  }
}
```

*In this example, resources fit a viewport which fills the total width of the screen and has a minimum ratio of 2:1 (two units / width for one unit / height). The publication behaves like an infinite horizontal scroll. Horizontal barscopes will appear if the screen ratio is smaller than 2:1, which is especially the case on a tablet in portrait mode. Vertical barscopes will never show.*

```json
"metadata": {
  "readingProgression": "ltr",
  "presentation": {
    "continuous": true,
    "overflow": "scrolled",
    "fit": "width",
    "viewportRatio": {
      "constraint": "min",
      "aspectRatio": "2:1"
    }
  }
}
```

### clipped

The `clipped` property is meant to adapt visual resources to any given viewport ratio. The clipped areas <strong class="rfc">must not</strong> contain information which are mandatory for the comprehension of the resource. 

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `clipped` | Specifies whether or not the parts of a resource that flow out of the viewport are clipped.  | Boolean  | `true` or `false` | `false` |

*In this example, resources are handled in a discontinuous way and each resource is scaled to fit the viewport height and clipped to fit different viewport widths. The publication behaves like a turbomedia.*

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
| `continuous` | Indicates if consecutive resources from the `readingOrder` should be handled in a continuous or discontinuous way.  | Boolean  | `true` or `false`  | `true` |

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
| `fit` | Specifies constraints for the presentation of a resource within the viewport.  | String  | `contain`, `cover`, `width` or `height` | `contain` |

| Value   | Definition |
| ------- | ---------- |
| `contain` | The content is centered and scaled to fit both dimensions into the viewport. |
| `cover`  |  The content is centered and scaled to fill the viewport. |
| `width`  |  The content is centered and scaled to fit the viewport width. |
| `height` |  The content is centered and scaled to fit the viewport height. |

*In this example, resources are handled in a continuous way, the content is scrollable on the vertical axis and each resource fits the viewport width. The publication behaves like a webtoon.*

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
| `orientation` | Suggested orientation for the device when displaying the resource.  | String  | `landscape`, `portrait` or `auto`  | `auto` |

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
| `overflow` |  Indicates if the overflow of resources from the `readingOrder` or `resources` should be handled using dynamic pagination or scrolling.  | String  | `paginated`, `scrolled` or `auto` | `auto` |


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
| `page` | Indicates how the resource should be displayed in a reading environment that displays synthetic spreads.  | String  | `left`, `right` or `center` | None |

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
| `spread` | Indicates the condition to be met for the resource to be rendered within a synthetic spread. | String  | `landscape`, `both`, `none` or `auto`  | `auto` |

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

- Metadata: <https://github.com/readium/webpub-manifest/blob/master/schema/extensions/presentation/metadata.schema.json>
- Link Properties: <https://github.com/readium/webpub-manifest/blob/master/schema/extensions/presentation/properties.schema.json>

For the purpose of validating a Readium Web Publication Manifest, use the following JSON Schema resources: 

- <https://readium.org/webpub-manifest/schema/extensions/presentation/metadata.schema.json>
- <https://readium.org/webpub-manifest/schema/extensions/presentation/properties.schema.json>