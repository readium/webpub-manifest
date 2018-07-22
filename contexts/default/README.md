# Default Context

>**Note**: This proposal is still a work in progress. For the metadata the idea is to have properties that can either work as literals or objects. [Examples for both are available in a separate Gist] (https://gist.github.com/HadrienGardeur/03ab96f5770b0512233a).

The Readium Web Publication Manifest defines a shared external context document hosted by the Readium Foundation and based primarily on schema.org and its extensions.

This context is meant primarily to:

- align the Web Publication Manifest with the Web by adopting schema.org and its extensions
- provide compatibility with EPUB 3.1 by providing equivalent metadata elements based on schema.org


| Name  | URI | Description | Required? |
| ---- | ----------- | ------------- | --------- |
Default Context | http://readium.org/webpub/default.jsonld  | Default context definition used in every Readium Web Publication Manifest. | Yes |


## Identifier

A Web Publication Manifest should contain an identifier. The identifier must be a valid URI:

```json
"identifier": "http://example.com/publication"
```

Publications can be updated and to identify each specific version, the manifest should also contain a `modified` element containing the timestamp when the publication was last modified expressed as an ISO 8601 time and date:

```json
"modified": "2016-02-22T11:31:38Z"
```

## Title

A Web Publication Manifest must contain a single title using the `title` element:

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

In addition to the `title` element, the manifest may also contain an optional `subtitle` element with exactly the same syntax.

```json
"title": "Flatland",
"subtitle": "A Romance of Many Dimensions"
```

The manifest may also contain a `sort_as` element to provide a single sortable string, used by a client to organize a collection of publications:

```json
"title": "A Tale of Two Cities",
"sort_as": "Tale of Two Cities, A"
```

## Contributors

The default context for the Web Publication Manifest provides a number of elements to indicate the nature of a contributor: `author`, `translator`, `editor`, `artist`, `illustrator`, `letterer`, `penciler`, `colorist`, `inker` and `narrator`.

In addition to these elements, it also provides a generic term for contributors: `contributor`.

A Web Publication Manifest should contain one or more contributor.

The most straightforward expression of a contributor is through a simple string:

```json
"author": "James Joyce"
```

Each element can also contain multiple contributors using a simple array:

```json
"artist": ["Shawn McManus", "Colleen Doran", "Bryan Talbot"]
```

In addition to a simple string representation, each contributor can also be represented using an object using the following elements: `name`, `sort_as` and `identifier`.

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

The contributor object may also contain a `sort_as` element to provide a single sortable string, used by a client to organize a collection of publications:

```json
"author": {
  "name": "Marcel Proust",
  "sort_as": "Proust, Marcel"
}
```

Finally, the object may also contain an `identifier`. The `identifier` must be a URI.

ISNI (http://isni.org) is the preferred authority, but other sources may also be used:

```json
"author": {
  "name": "Jules Amédée Barbey d'Aurevilly",
  "sort_as": "Barbey d'Aurevilly, Jules Amédée",
  "identifier": "http://isni.org/isni/0000000121317806"
}
```
If none of the elements available are specific enough, a `contributor` element may be used instead. 

The `contributor` element should be used with an object that contains a `role`. 
All values for the `role` element should be based on [MARC relator codes](https://www.loc.gov/marc/relators/relaterm.html): 

```json
"contributor": {
  "name": "Lou Reed",
  "role": "sng"
}
```

## Language

In order to indicate its primary language, a Web Publication Manifest should use a `language` element. Its value must be a valid [BCP 47](https://tools.ietf.org/html/bcp47) language tag.

```json
"language": "en"
```

If a publication has more than one primary language (a bilingual edition for example), the `language` element may contain an array of BCP 47 language tags:

```json
"language": ["en", "fr", "ja"]
```

## Description

A Web Publication Manifest may contain a description of the publication in plain text using the `description` element:

```json
"description": "The story of two gnomes, discussing the meaning of life in a
Scandivanian garden."
```

## Publisher

A Web Publication Manifest may list one or more publishers using the `publisher` element.

To provide even more details, it's also possible to use the `imprint` element that behaves exactly like `publisher` but provides a complementary information.

The most straightforward expression is through a simple string:

```json
"publisher": "Literary Fiction Ltd.",
"imprint": "World Literature"
```

This element also allows a more complex representation using an object and the following elements: `name`, `sort_as`, `identifier`. The semantics and syntax are identical to contributors:

```json
"publisher": {
  "name": "The Science Fiction Company",
  "sort_as": "Science Fiction Company, The",
  "identifier": "http://example.com/publisher/TheScienceFictionCompany"
}
```

Multiple publishers can be listed in this element using the string or object representations.


## Publication Date

A Web Publication Manifest may contain a publication date using the `published` element. The publication date must be a valid ISO 8601 date.

```json
"published": "2016-09-02"
```

## Subjects

A Web Publication Manifest may also provide one or more subjects using the `subject` element:

```json
"subject": "Historical Fiction"
```

Multiple subjects are listed using an array:

```json
"subject": ["Science Fiction", "Fantasy"]
```

Subjects can also be expressed using an object with the following elements: `name`, `sort_as`, `code` and `scheme`.

`name` is meant to provide a human readable string for the subject, while `sort_as` is meant to provide a string that a machine can sort: 

```json
"subject": {
  "name": "Manga: Shonen",
  "sort_as": "Shonen"
}
```

In many fields and territories, a number of controlled vocabularies are in use to identify subjects. For example THEMA is used in the publishing industry to provide an international subject scheme.

To indicate that a subject belongs to a particular scheme, the `scheme` element is available. The `scheme` must be a URI.

The `code` element is available to provide the string that identifies the subject in a given scheme:

```json
"subject": {
  "name": "Manga: Shonen",
  "sort_as": "Shonen",
  "scheme": "http://www.editeur.org/151/Thema/",
  "code": "XAMG"
}
```

## Collections & Series

A Web Publication Manifest may indicate that it belongs to one or multiple collections/series.

`collection` and `series` behave the same way, the most straightforward way to indicate that a publication belongs to a collection/series is through a simple string:

```json
"belongs_to": {
  "collection": "Mysteries from Another Time",
  "series": "The Zombie Detective"
}
```

In order to provide more information about a specific collection/series, an object can also be used instead of a string.

To provide a name and a sortable string, `collection` and `series` support both `name` and `sort_as`:

```json
"belongs_to": {
  "series": {
    "name": "The Zombie Detective",
    "sort_as": "Zombie Detective, The"
  }
}
```

A collection/series can also have an identifier, provided using the `identifier`
element. The identifier must be a URI:

```json
"belongs_to": {
  "series": {
    "name": "The Zombie Detective",
    "sort_as": "Zombie Detective, The",
    "identifier": "http://www.example.com/series/TheZombieDetective"
  }
}
```

Finally, series/collection can be ordered. To provide the position of the current publication in a series/collection, the `position` element can be used.

A position can be either an integer or a float where the value is greater than zero.

```json
"belongs_to": {
  "series": {
    "name": "The Zombie Detective",
    "sort_as": "Zombie Detective, The",
    "identifier": "http://www.example.com/series/TheZombieDetective",
    "position": 4
  }
}
```

## Progression Direction

To properly browse through a publication, a User Agent needs to know its progression direction.

This is expressed in the manifest using `direction` which allows the following values: `ltr` (left to right), `rtl` (right to left) and `auto`.

This defaults to `auto` when no value is set.

## Duration and Number of Pages

To indicate the length of a publication, this context defines two different elements: `duration` and `numberOfPages`.

`duration` is expressed in seconds using an integer, while `numberOfPages` is an integer as well.

```
"duration": "5467",
"numberOfPages": "178"
```


## Examples

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

## Mapping to Schema.org & EPUB 3.1

| Key  | Schema.org | EPUB 3.1 |
| ---- | ---------- | -------- |
| [identifier](#identifier) | None, identifies the resource  | dc:identifier |
| [modified](#identifier) | http://schema.org/dateModified  | dcterms:modified |
| [title](#title) | http://schema.org/name  | dc:title |
| [subtitle](#title) | http://schema.org/alternativeHeadline | - |
| [sort_as](#title)  | http://schema.org/alternateName  | title@opf:file-as |
| [author](#contributors) | http://schema.org/author  | dc:creator |
| [translator](#contributors) | http://schema.org/translator  | dc:contributor@opf:role="trl" |
| [editor](#contributors) | http://schema.org/editor  | dc:contributor@opf:role="edt" |
| [illustrator](#contributors)| http://schema.org/illustrator  | dc:contributor@opf:role="ill" |
| [artist](#contributors)| http://schema.org/artist | dc:contributor@opf:role="art" |
| [colorist](#contributors)| http://schema.org/colorist | dc:contributor@opf:role="clr" |
| [inker](#contributors)| http://schema.org/inker | - |
| [penciler](#contributors)| http://schema.org/penciler | - |
| [letterer](#contributors)| http://schema.org/letterer | - |
| [narrator](#contributors) | http://schema.org/readBy | dc:contributor@opf:role="nrt" or meta@property="media:narrator"|
| [contributor](#contributors) | http://schema.org/contributor  | dc:contributor |
| [	language](#language)  | http://schema.org/inLanguage  | dc:language |
| [subject](#subjects)  | http://schema.org/about  | dc:subject |
| [	publisher](#publisher)  | http://schema.org/publisher  | dc:publisher |
| [	imprint](#publisher)  | http://schema.org/publisherImprint  | - |
| [	published](#publication-date)   | http://schema.org/datePublished  | dc:date |
| [	description](#description)  | http://schema.org/description  | dc:description |
| [belongs_to](#collections--series) | http://www.schema.org/isPartOf | - |
| [series](#collections--series) | http://www.schema.org/Series | - |
| [collection](#collections--series) | http://schema.org/Collection | - |
| [position](#collections--series) | http://www.schema.org/position | - |
| [direction](#progression-direction) | -  | spine@page-progression-direction |
| [numberOfPages](#duration-and-number-of-pages) | http://schema.org/numberOfPages  | schema:numberOfPages |
| [duration](#duration-and-number-of-pages) | http://schema.org/duration  | meta@property="media:duration"|
