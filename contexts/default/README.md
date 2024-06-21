# Default Context

The Readium Web Publication Manifest defines a shared external context document hosted by the Readium Foundation and based primarily on schema.org and its extensions.

This context is meant primarily to:

- align the Web Publication Manifest with the Web by adopting schema.org and its extensions
- provide compatibility with EPUB 3.2 by providing equivalent metadata elements based on schema.org


| Name  | URI | Description | Required? |
| ----- | --- | ----------- | --------- |
Default Context | [https://readium.org/webpub-manifest/context.jsonld](https://readium.org/webpub-manifest/context.jsonld) | Default context definition used in every Readium Web Publication Manifest. | Yes |


## Title

A Web Publication Manifest <strong class="rfc">must</strong> contain a single title using the `title` element:

```json
"title": "Moby-Dick"
```

In addition to a simple string representation, the `title` element also supports alternate representations of the same string in different scripts and languages.

To provide these alternate representations, an object may be used instead of a string, where each key identifies a language/script and must be a valid [BCP 47](https://tools.ietf.org/html/bcp47) language tag:

```json
"title": {
  "fr": "Vingt mille lieues sous les mers",
  "en": "Twenty Thousand Leagues Under the Sea",
  "ja": "海底二万里"
}
```

In addition to the `title` element, the manifest <strong class="rfc">may</strong> also contain an optional `subtitle` element with exactly the same syntax.

```json
"title": "Flatland"
"subtitle": "A Romance of Many Dimensions"
```

The manifest <strong class="rfc">may</strong> also contain a `sortAs` element to provide a single sortable string, used by a client to organize a collection of publications:

```json
"title": "A Tale of Two Cities"
"sortAs": "Tale of Two Cities, A"
```

## Identifier

A Web Publication Manifest <strong class="rfc">should</strong> contain an identifier. The identifier must be a valid URI:

```json
"identifier": "http://example.com/publication"
```

## Layout and reading progression

Publications come in all shapes and forms but when it comes to publications that are primarily made of text and visuals, we can organize them in three main categories:

- Reflowable publications, where users are free to change the text and layout however they prefer.
- Fixed layout publications, where each resource is a "page" and may be presented side by side with another resource in a "spread".
- Scrolled publications, where displaying the publication in a continuous scroll is meaningful and resources are usually fit to the width of the viewport

In order to convey this information, a Web Publication Manifest <strong class="rfc">may</strong> include a `layout` property with one of the following values:

| Value | Description | Formats |
| ----- | ----------- | ------- |
| `reflowable` | Reading systems are free to adapt text and layout entirely based on user preferences. | EPUB |
| `fixed` | Each resource is a "page" where both dimensions are usually contained in the device's viewport. Based on user preferences, the reading system may also display two resources side by side in a spread. | Divina, Fixed Layout EPUB or PDF
| `scrolled` | Resources are displayed in a continuous scroll by filling the width of the viewport, without any visible gap between between spine items. | Scrolled Divina |

To provide a number of affordances (taps, gestures, keyboard events), reading systems also need to know the reading progression.

For scrolled publication, the reading progression is always from top to bottom. 

For reflowable and fixed layout publications, a manifest <strong class="rfc">may</strong> express this information using `readingProgression`. 

It allows the following values: `ltr` (left to right, default value) and `rtl` (right to left).

## Contributors

The default context for the Web Publication Manifest provides a number of elements to indicate the nature of a contributor: `author`, `translator`, `editor`, `artist`, `illustrator`, `letterer`, `penciler`, `colorist`, `inker` and `narrator`.

In addition to these elements, it also provides a generic term for contributors: `contributor`.

A Web Publication Manifest <strong class="rfc">should</strong> contain one or more contributors.

The most straightforward expression of a contributor is through a simple string:

```json
"author": "James Joyce"
```

Each element can also contain multiple contributors using a simple array:

```json
"artist": ["Shawn McManus", "Colleen Doran", "Bryan Talbot"]
```

In addition to a simple string representation, each contributor can also be represented using an object using the following elements: `name`, `sortAs` and `identifier`.

When an object is used, it must contain at least `name`. 

It behaves like the `title` element and allows either a simple strings, or representations in multiple languages and scripts of a contributor's name:

```json
"author": {
  "name": {
    "ru": "Михаил Афанасьевич Булгаков",
    "en": "Mikhail Bulgakov",
    "fr": "Mikhaïl Boulgakov"
  }
}
```

The contributor object may also contain a `sortAs` element to provide a single sortable string, used by a client to organize a collection of publications:

```json
"author": {
  "name": "Marcel Proust",
  "sortAs": "Proust, Marcel"
}
```

Finally, the object may also contain an `identifier`. The `identifier` must be a URI.

ISNI (http://isni.org) is the preferred authority, but other sources may also be used:

```json
"author": {
  "name": "Jules Amédée Barbey d'Aurevilly",
  "sortAs": "Barbey d'Aurevilly, Jules Amédée",
  "identifier": "http://isni.org/isni/0000000121317806"
}
```

If none of the elements available are specific enough, a `contributor` element <strong class="rfc">may</strong> be used instead. 

The `contributor` element should be used with an object that contains a `role`. 
All values for the `role` element should be based on [MARC relator codes](https://www.loc.gov/marc/relators/relaterm.html): 

```json
"contributor": {
  "name": "Lou Reed",
  "role": "sng"
}
```

## Language

In order to indicate its primary language, a Web Publication Manifest <strong class="rfc">should</strong> use a `language` element. Its value must be a valid [BCP 47](https://tools.ietf.org/html/bcp47) language tag.

```json
"language": "en"
```

If a publication has more than one primary language (a bilingual edition for example), the `language` element <strong class="rfc">may</strong> contain an array of values:

```json
"language": ["en", "fr", "ja"]
```

## Description

A Web Publication Manifest <strong class="rfc">may</strong> contain a description of the publication in plain text using the `description` element:

```json
"description": "The story of two gnomes, discussing the meaning of life in a Scandivanian garden."
```

## Publisher

A Web Publication Manifest <strong class="rfc">may</strong> list one or more publishers using the `publisher` element.

To provide even more details, it's also possible to use the `imprint` element that behaves exactly like `publisher` but provides a complementary information.

The most straightforward expression is through a simple string:

```json
"publisher": "Literary Fiction Ltd."
"imprint": "World Literature"
```

This element also allows a more complex representation using an object and the following elements: `name`, `sortAs`, `identifier`. The semantics and syntax are identical to contributors:

```json
"publisher": {
  "name": "The Science Fiction Company",
  "sortAs": "Science Fiction Company, The",
  "identifier": "http://example.com/publisher/TheScienceFictionCompany"
}
```

Multiple publishers can be listed in this element using the string or object representations.


## Publication date

A Web Publication Manifest <strong class="rfc">may</strong> contain a publication date using the `published` element. The publication date must be a valid ISO 8601 date.

```json
"published": "2016-09-02"
```

## Modification date

Publications can be updated and to identify each specific version, the manifest <strong class="rfc">should</strong> also contain a `modified` element containing the timestamp when the publication was last modified expressed as an ISO 8601 time and date:

```json
"modified": "2016-02-22T11:31:38Z"
```

## Subjects

A Web Publication Manifest <strong class="rfc">may</strong> also provide one or more subjects using the `subject` element:

```json
"subject": "Historical Fiction"
```

Multiple subjects are listed using an array:

```json
"subject": ["Science Fiction", "Fantasy"]
```

Subjects can also be expressed using an object with the following elements: `name`, `sortAs`, `code` and `scheme`.

`name` is meant to provide a human readable string for the subject, while `sortAs` is meant to provide a string that a machine can sort: 

```json
"subject": {
  "name": "Manga: Shonen",
  "sortAs": "Shonen"
}
```

The `code` element is available to provide the string that identifies the subject in a given scheme:

```json
"subject": {
  "name": "Manga: Shonen",
  "sortAs": "Shonen",
  "scheme": "https://ns.editeur.org/thema/",
  "code": "XAMG"
}
```

To indicate that a subject belongs to a particular scheme, the `scheme` element is available. The `scheme` must be a URI.

This document identifies the following subject schemes along with a recommended URI value for them:

| Scheme | URI |
| ------- | --- |
| BIC | <https://bic.org.uk/> |
| BISAC | <https://www.bisg.org/#bisac> |
| CLIL | <http://clil.org/> |
| Thema | <https://ns.editeur.org/thema/> |


## Collections & series

A Web Publication Manifest <strong class="rfc">may</strong> indicate that it belongs to one or multiple collections/series.

`collection` and `series` behave the same way, the most straightforward way to indicate that a publication belongs to a collection/series is through a simple string:

```json
"belongsTo": {
  "collection": "Mysteries from Another Time",
  "series": "The Zombie Detective"
}
```

In order to provide more information about a specific collection/series, an object can also be used instead of a string.

To provide a name and a sortable string, `collection` and `series` support both `name` and `sortAs`:

```json
"belongsTo": {
  "series": {
    "name": "The Zombie Detective",
    "sortAs": "Zombie Detective, The"
  }
}
```

A collection/series can also have an identifier, provided using the `identifier`
element. The identifier must be a URI:

```json
"belongsTo": {
  "series": {
    "name": "The Zombie Detective",
    "sortAs": "Zombie Detective, The",
    "identifier": "http://www.example.com/series/TheZombieDetective"
  }
}
```

Finally, series/collection can be ordered. To provide the position of the current publication in a series/collection, the `position` element can be used.

A position can be either an integer or a float where the value is greater than zero.

```json
"belongsTo": {
  "series": {
    "name": "The Zombie Detective",
    "sortAs": "Zombie Detective, The",
    "identifier": "http://www.example.com/series/TheZombieDetective",
    "position": 4
  }
}
```

## Duration and number of pages

To indicate the length of a publication, a Web Publication Manifest <strong class="rfc">may</strong> include the `duration` and `numberOfPages` terms.

`duration` is expressed in seconds using a float (number in JSON), while `numberOfPages` is an integer.

```json
"duration": 5467
"numberOfPages": 178
```

In addition to these two properties, `abridged` is used to indicate an abridged edition of a publication. This is expressed using a boolean.

```json
"abridged": true
```

## Accessibility metadata

In order to document its accessibility metadata, a Web Publication Manifest <strong class="rfc">should</strong> include an `accessibility` object.

This `accessibility` object <strong class="rfc">may</strong> contain the following properties: `conformsTo`, `certification`, `accessMode`, `accessModeSufficient`, `feature`, `hazard` and `summary`.

### Conformance

`conformsTo` contains one or more URI, meant to convey that a publication conforms to a specific accessibility profile.

This specification identifies the following profiles:

| Profile | URI |
| ------- | --- |
| EPUB Accessibility 1.0 - WCAG 2.0 Level A | <http://www.idpf.org/epub/a11y/accessibility-20170105.html#wcag-a> |
| EPUB Accessibility 1.0 - WCAG 2.0 Level AA | <http://www.idpf.org/epub/a11y/accessibility-20170105.html#wcag-aa> |
| EPUB Accessibility 1.0 - WCAG 2.0 Level AAA | <http://www.idpf.org/epub/a11y/accessibility-20170105.html#wcag-aaa> |

### Certification

The `certification` object contains information about the certification process of a publication.

`certifiedBy` identifies the person or the organization that conducted the certification.

`credential` provides additional credentials regarding the ability of the certifier to conduct an accessibility report.

`report` points to one or more accessibility reports for the publication, using a list of URL.

```json
"certification": {
  "certifiedBy": "Tom Smith",
  "report": "https://example.com/report/a11y",
  "credential": "Certified by Benetech"
}
```

### accessMode and accessModeSufficient

`accessMode` and `accessModeSufficient` are meant to list the human sensory perceptual systems or cognitive faculties necessary to access a given publication.

While `accessMode` provides a complete list, `accessModeSufficient` is focused on list of single or combined accessModes that are sufficient to understand all the intellectual content of a resource.

Both properties are controlled by external vocabularies maintained by the W3C.

| Property | Controlled vocabulary |
| -------- | --------------------- |
| `accessMode` | <https://www.w3.org/2021/a11y-discov-vocab/latest/#accessMode-vocabulary> |
| `accessModeSufficient` | <https://www.w3.org/2021/a11y-discov-vocab/latest/#accessModeSufficient-vocabulary> |

```json
"accessibility": {
  "accessMode": ["textual", "visual"],
  "accessModeSufficient": [
    ["textual", "visual"],
    "textual"
  ],
  "feature": ["alternativeText"]
}
```


### Features and hazards

`feature` and `hazard` provide a list of potential accessibility features or hazards for a publication.

Both properties are controlled by external vocabularies maintained by the W3C.

| Property | Controlled vocabulary |
| -------- | --------------------- |
| `feature` | <https://www.w3.org/2021/a11y-discov-vocab/latest/#accessibilityFeature-vocabulary> |
| `hazard` | <https://www.w3.org/2021/a11y-discov-vocab/latest/#accessibilityHazard-vocabulary> |

```json
"accessibility": {
  "feature": [
    "alternativeText", 
    "displayTransformability", 
    "readingOrder",
    "structuralNavigation",
    "tableOfContents"
  ],
  "hazard": ["none"]
}
```

### Summary

`summary` is meant to convey additional information about the accessibility of a publication that cannot be expressed using `conformsTo`, `certification`, `accessMode`, `accessModeSufficient`, `feature` or `hazard`.

```json
"summary": "The publication is recorded by a professional narrator."
```

## Appendix A - JSON Schema

The default context is implemented in the core JSON Schema for the Readium Web Publication Manifest and should be used as a reference as well.

The JSON Schema for metadata is under version control at [https://github.com/readium/webpub-manifest/blob/master/schema/metadata.schema.json](https://github.com/readium/webpub-manifest/blob/master/schema/metadata.schema.json)

For the purpose of validating a Readium Web Publication Manifest, use the following JSON Schema resource: [https://readium.org/webpub-manifest/schema/publication.schema.json](https://readium.org/webpub-manifest/schema/publication.schema.json)

## Appendix B - Examples

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  
  "metadata": {
    "@type": "http://schema.org/Book",
    "identifier": "urn:isbn:9780000000001",
    "title": "Moby-Dick",
    "author": "Herman Melville",
    "language": "en",
    "publisher": "Whale Publishing Ltd.",
    "modified": "2016-02-18T10:32:18Z"
  },

  "links": [
    {"rel": "self", "href": "http://example.org/manifest.json", "type": "application/webpub+json"},
    {"rel": "alternate", "href": "http://example.org/publication.epub", "type": "application/epub+zip"}
  ],
  
  "readingOrder": [
    {"href": "cover.jpg", "type": "image/jpeg", "rel": "cover"}, 
    {"href": "map.svg", "type": "image/svg+xml", "title": "Map"}, 
    {"href": "c001.html", "type": "text/html", "title": "Chapter 1"}, 
    {"href": "c002.html", "type": "text/html", "title": "Chapter 2"}
  ],

  "resources": [
    {"href": "style.css", "type": "text/css"}, 
    {"href": "whale.jpg", "type": "image/jpeg"}, 
    {"href": "boat.svg", "type": "image/svg+xml"}, 
    {"href": "notes.html", "type": "text/html"}
  ]
}
```

If we use another example with more complex metadata expression and an extension:

```json
{
  "@context": "https://readium.org/webpub-manifest/context.jsonld",
  
  "metadata": {
    "@type": "http://schema.org/Book",
    "identifier": "urn:isbn:9780000000002",
    "title": {
      "en": "A Journey into the Center of the Earth",
      "fr": "Voyage au centre de la Terre"
    },
    "sortAs": "Journey into the Center of the Earth, A",
    "author": {
      "name": "Jules Verne",
      "identifier": "http://isni.org/isni/0000000121400562",
      "sortAs": "Verne, Jules"
    },
    "translator": "Frederick Amadeus Malleson",
    "language": ["en", "fr"],
    "publisher": "SciFi Publishing Inc.",
    "modified": "2016-02-22T11:31:38Z",
    "description": "The story involves German professor Otto Lidenbrock who believes there are volcanic tubes going toward the centre of the Earth. He, his nephew Axel, and their guide Hans descend into the Icelandic volcano Snæfellsjökull, encountering many adventures, including prehistoric animals and natural hazards, before eventually coming to the surface again in southern Italy, at the Stromboli volcano.",
    "schema:isFamilyFriendly": true,
    "belongsTo": {
      "series": {
        "name": "The Extraordinary Voyages",
        "position": 3
      },
      "collection": "SciFi Classics"
    }
  }
}
```

## Appendix C - Mapping to EPUB

In order to convert EPUB packages into a Readium Web Publication Manifest, we've documented how each metadata item listed in the default context is mapped to an equivalent in EPUB 2.x or 3.x.

This live document is available at: [https://readium.org/architecture/streamer/parser/metadata](https://readium.org/architecture/streamer/parser/metadata)
