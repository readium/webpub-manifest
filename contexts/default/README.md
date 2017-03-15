# Default Context for the Web Publication Manifest

>**Note**: This proposal is still a work in progress. For the metadata the idea is to have properties that can either work as literals or objects. [Examples for both are available in a separate Gist] (https://gist.github.com/HadrienGardeur/03ab96f5770b0512233a).

The Web Publication Manifest defines a shared external context document hosted by the IDPF and based primarily on schema.org and its extensions.

This context is meant primarily to:

- align the Web Publication Manifest with the Web by adopting schema.org and its extensions
- provide compatibility with EPUB 3.1 by providing equivalent metadata elements based on schema.org


| Name  | URI | Description | Required? |
| ---- | ----------- | ------------- | --------- |
Default Context | http://readium.org/webpub/default.jsonld  | Default context definition used in every Web Publication Manifest. | Yes |

## Core Metadata

| Key  | Schema.org | EPUB 3.1 |
| ---- | ---------- | -------- |
| [identifier](definitions.md#identifier) | None, identifies the resource  | dc:identifier |
| [title](definitions.md#title) | http://schema.org/name  | dc:title |
| [sort_as](definitions.md#title)  | http://schema.org/alternateName  | title@opf:file-as |
| [author](definitions.md#contributors) | http://schema.org/author  | dc:creator |
| [translator](definitions.md#contributors) | http://schema.org/translator  | dc:contributor@opf:role="trl" |
| [editor](definitions.md#contributors) | http://schema.org/editor  | dc:contributor@opf:role="edt" |
| [illustrator](definitions.md#contributors)| http://schema.org/illustrator  | dc:contributor@opf:role="ill" |
| [narrator](definitions.md#contributors) | http://bib.schema.org/readBy | dc:contributor@opf:role="nrt" |
| [contributor](definitions.md#contributors) | http://schema.org/contributor  | dc:contributor |
| [	language](definitions.md#language)  | http://schema.org/inLanguage  | dc:language |
| [subject](definitions.md#subjects)  | http://schema.org/keywords  | dc:subject |
| [	publisher](definitions.md#publisher)  | http://schema.org/publisher  | dc:publisher |
| [modified](definitions.md#identifier) | http://schema.org/dateModified  | dcterms:modified |
| [	published](definitions.md#publication-date)   | http://schema.org/datePublished  | dc:date |
| [	description](definitions.md#description)  | http://schema.org/description  | dc:description |
| numberOfPages  | http://schema.org/numberOfPages  | schema:numberOfPages |


## Collections & Series Properties

This context also allows metadata to express that a publication belongs to any number of collections or series.

The `belongs_to` object is used for that purpose and has no real equivalent in EPUB 3.1.

The following keys and their mappings to schema.org are used to specify collections/series:

| Key  | Schema.org |
| ---- | --- |
| [belongs_to](definitions.md#collections--series) | http://www.schema.org/isPartOf |
| [series](definitions.md#collections--series) | http://www.schema.org/Series |
| [collection](definitions.md#collections--series) | http://www.schema.org/Collection |
| [position](definitions.md#collections--series) | http://www.schema.org/position |

Here's an example of metadata for both collections and series:

```json
"belongs_to": {
  "collection": "Young Adult Classics",
  "series": {
    "name": "Harry Potter",
    "position": 4
  }
}
```

## Example

```json
{
  "@context": "http://readium.org/webpub/default.jsonld",
  
  "metadata": {
    "@type": "http://schema.org/EBook",
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
  
  "spine": [
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
  "@context": "http://readium.org/webpub/default.jsonld",
  
  "metadata": {
    "@type": "http://schema.org/EBook",
    "identifier": "urn:isbn:9780000000002",
    "title": {
      "en": "A Journey into the Center of the Earth",
      "fr": "Voyage au centre de la Terre"
    },
    "sort_as": "Journey into the Center of the Earth, A",
    "author": {
      "name": "Jules Verne",
      "identifier": "http://isni.org/isni/0000000121400562",
      "sort_as": "Verne, Jules"
    },
    "translator": "Frederick Amadeus Malleson",
    "language": ["en", "fr"],
    "publisher": "SciFi Publishing Inc.",
    "modified": "2016-02-22T11:31:38Z",
    "description": "The story involves German professor Otto Lidenbrock who believes there are volcanic tubes going toward the centre of the Earth. He, his nephew Axel, and their guide Hans descend into the Icelandic volcano Snæfellsjökull, encountering many adventures, including prehistoric animals and natural hazards, before eventually coming to the surface again in southern Italy, at the Stromboli volcano.",
    "schema:isFamilyFriendly": true,
    "belongs_to": {
      "series": {
        "name": "The Extraordinary Voyages",
        "position": 3
      },
      "collection": "SciFi Classics"
    }
  }
}
```
