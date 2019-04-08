# EPUB Context

| Name  | URI | Description | Required? |
| ---- | ----------- | ------------- | --------- |
EPUB Context | [https://readium.org/webpub-manifest/contexts/epub/context.jsonld](https://readium.org/webpub-manifest/contexts/epub/context.jsonld)| EPUB specific metadata | No |

## Rendition Properties

All rendition specific properties must show up in a `rendition` object. This specification allows the following elements, all defined in the EPUB 3.2 specification:


| Key   | Semantics | Type     | Values    | URI |
| ----- | --------- | -------- | --------- | --- | 
| layout  | Hint about the nature of the layout.  | String  | `fixed` or `reflowable` | http://www.idpf.org/vocab/rendition/#layout |
| `orientation`  | Suggested orientation for the device when displaying the publication.  | String  | `auto`, `landscape` or `portrait`  | http://www.idpf.org/vocab/rendition/#orientation |
| overflow  | Suggested method for handling overflow while displaying the publication.  | String  | `auto`, `paginated`, `scrolled` or `scrolled-continuous`  | http://www.idpf.org/vocab/rendition/#flow |
| spread  | Indicates the condition to be met for the publication to be rendered within a synthetic spread. | String  | `auto`, `both`, `none` or `landscape` | http://www.idpf.org/vocab/rendition/#spread |

Here's an example of metadata for a fixed layout document:

```json
"rendition": {
  "overflow": "paginated",
  "layout": "fixed",
  "orientation": "landscape",
  "spread": "none"
}
```

## Appendix A - JSON Schema

The EPUB context is referenced in the core JSON Schema for the Readium Web Publication Manifest.

The JSON Schema is under version control at [https://github.com/readium/webpub-manifest/blob/master/schema/extensions/epub/metadata.schema.json](https://github.com/readium/webpub-manifest/blob/master/schema/extensions/epub/metadata.schema.json)