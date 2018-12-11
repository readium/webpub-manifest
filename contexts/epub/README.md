# EPUB Context

| Name  | URI | Description | Required? |
| ---- | ----------- | ------------- | --------- |
EPUB Context | https://readium.org/webpub-manifest/contexts/epub/context.jsonld  | EPUB specific metadata | No |

## Rendition Properties

All rendition specific properties must show up in a `rendition` object. This specification allows the following elements, all defined in the EPUB 3.1 specification:

| Key  | URI |
| ---- | --- |
| overflow  | http://www.idpf.org/vocab/rendition/#flow |
| layout  | http://www.idpf.org/vocab/rendition/#layout |
| orientation  | http://www.idpf.org/vocab/rendition/#orientation |
| spread  | http://www.idpf.org/vocab/rendition/#spread |

Here's an example of metadata for a fixed layout document:

```json
"rendition": {
  "overflow": "paginated",
  "layout": "fixed",
  "orientation": "landscape",
  "spread": "none"
}
```
