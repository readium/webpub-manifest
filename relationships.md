# Link Relations

The Readium Web Publication Manifest specification and its extensions currently define and/or use the following link relations:

| Relation  | Definition    | Reference |
| -----------   | ------------- | --------- |
| `alternate`   | Designates a substitute for the link's context | [HTML5](https://www.w3.org/TR/html5/links.html) |
| `contents`   | Refers to a table of contents | [HTML4](https://www.w3.org/TR/html4/types.html#type-links) |
| `cover`   | Refers to a publication's cover | [Readium Web Publication Manifest](https://github.com/readium/webpub-manifest) |
| `manifest`   | Links to a manifest | [Web App Manifest](https://www.w3.org/TR/appmanifest/) |
| `search`   | Refers to a URI or templated URI that will perform a search | [HTML5](https://www.w3.org/TR/html5/links.html) |
| `self`   | Conveys an identifier for the link's context | [Atom Syndication Format](https://tools.ietf.org/html/rfc4287) |

In addition to these relations, any term define in the [IANA link registry](https://www.iana.org/assignments/link-relations/link-relations.xhtml) can also be used.

Extensions must either be registered on this repo or rely on URIs.

## OPDS 2.0

While OPDS 2.0 itself is not an extension of the Readium Web Publication Manifest, it shares the same abstract model and syntax.

For this reason, it feels relevant to list the link relations introduced by OPDS as well.

| Relation  | Definition | Reference |
| --------- | ---------- | --------- | 
| `http://opds-spec.org/acquisition`  | Fallback acquisition relation when no other relation is a good fit to express the nature of the transaction.  | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |
| `http://opds-spec.org/acquisition/open-access`  | Indicates that a publication is freely accessible without any requirement, including authentication.  | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |
| `http://opds-spec.org/acquisition/borrow`  | Indicates that a publication can be borrowed for a limited period of time.  | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |
| `http://opds-spec.org/acquisition/buy`  | Indicates that a publication can be purchased for a given price. | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |
| `http://opds-spec.org/acquisition/sample`  | Indicates that a sub-set of the full publication is freely accessible at a given URI, without any prior requirement. | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |
| `preview`  | Indicates that a sub-set of the full publication is freely accessible at a given URI, without any prior requirement. | [RFC 6903](https://tools.ietf.org/html/rfc6903#section-3) |
| `http://opds-spec.org/acquisition/subscribe`  | Indicates that a publication be subscribed to, usually as part of a purchase and for a limited period of time. | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |