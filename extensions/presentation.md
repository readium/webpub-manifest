# Presentation Hints

The Presentation Hints extension defines a number of elements (meant to be expressed as metadata, link properties or both) meant to convey additional hints to User Agents about the way content <span class="rfc">should</span> be presented to the user.

## 1. Presentation Hints in `metadata`

The following elements <strong class="rfc">may</strong> be included in `presentation`:

- [`continuous`](#continuous)
- [`fit`](#fit)
- [`orientation`](#orientation)
- [`overflow`](#overflow)
- [`spread`](#spread)

## 2. Presentation Hints as Link Properties

The following elements <strong class="rfc">may</strong> be included in `properties` in a Link Object from the `readingOrder`:

- [`fit`](#fit)
- [`orientation`](#orientation)
- [`overflow`](#overflow)
- [`page`](#page)
- [`spread`](#spread)


## 3. Properties

### continuous

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `continuous` | Indicates how the progression between resources from the `readingOrder` should be handled.  | Boolean  | `true` or `false`  | `false` |

```
"metadata": {
  "presentation": {
    "overflow": "scrolled",
    "continuous": true
  }
}
```

### fit

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `fit` | Suggested method for constraining a resource inside the viewport.  | String  | `width`, `height`, `contain` or `cover` | `contain` |

```
"metadata": {
  "presentation": {
    "fit": "width",
    "overflow": "scrolled",
    "continuous": true
  }
}
```

### orientation

The `orientation` property defaults to `auto` and is mostly relevant for resources with fixed dimensions (images, videos), where the orientation has an actual impact on how the resource is displayed.

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `orientation` | Suggested orientation for the device when displaying the linked resource.  | String  | `auto`, `landscape` or `portrait`  | `auto` |


```
"metadata": {
  "presentation": {
    "orientation": "portrait",
    "overflow": "scrolled",
    "continuous": true
  }
}
```


```
{
  "href": "page1.html", "type": "text/html",
  "properties": {
    "layout": "fixed",
    "orientation": "landscape"
  }
}
```

### overflow

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `overflow` | Suggested method for handling overflow while displaying the linked resource.  | String  | `auto`, `clipped`, `paginated` or `scrolled` | `auto` |


```
"metadata": {
  "presentation": {
    "overflow": "paginated"
  }
}
```


```
{
  "href": "endnotes.html", "type": "text/html",
  "properties": {
    "overflow": "scrolled"
  }
}
```

### page

The `page` property is meant to provide a hint to reading systems that rely on synthetic spreads to display more than a single resource at once.

| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `page` | Indicates how the linked resource should be displayed in a reading environment that displays synthetic spreads.  | String  | `left`, `right` or `center` | None |

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

### spread

The `spread` property is meant to indicate to the reading system the condition for displaying the linked resource in a synthetic spread.


| Key   | Semantics | Type     | Values    | Default |
| ----- | --------- | -------- | --------- | ------- |
| `spread` | Indicates the condition to be met for the linked resource to be rendered within a synthetic spread. | String  | `auto`, `both`, `none` or `landscape` | `auto` |

```
"metadata": {
  "presentation": {
    "spread": "landscape",
    "fit": "contain"
  }
}
```


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