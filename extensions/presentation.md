# Presentation Hints


### overflow

```
{
  "href": "endnotes.html", "type": "text/html",
  "properties": {
    "overflow": "scrolled"
  }
}
```

### orientation

The `orientation` property defaults to `auto` and is mostly relevant for resources with fixed dimensions (images, videos), where the orientation has an actual impact on how the resource is displayed.

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

The `page` property is meant to provide a hint to reading systems that rely on synthetic spreads to display more than a single resource at once.


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


