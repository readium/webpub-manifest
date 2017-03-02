#Context Documents Registry

| Name  | URI | Description | Required? |
| ---- | ----------- | ------------- | --------- |
[Default Context](default/) | http://readium.org/webpub/default.jsonld  | Default context definition used in every Web Publication Manifest. | Yes |
[EPUB Context](epub/) | http://readium.org/webpub/epub.jsonld  | EPUB specific metadata. | No |


## Registering a Context Document

In order to register an additional context document for the Web Publication Manifest, it is required to:

- provide documentation in Markdown
- publish the context document on a domain that you control
- send a pull request on this folder that includes the documentation
