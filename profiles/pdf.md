# PDF Profile

**Editors:**

* Michael Benowitz ([NYPL](https://www.nypl.org))
* Laurent Le Meur ([EDRLab](https://www.edrlab.org))

**Participate:**

* [GitHub readium/webpub-manifest](https://github.com/readium/webpub-manifest)
* [File an issue](https://github.com/readium/webpub-manifest/issues)

## Example

```json
{
  "@context": "http://readium.org/webpub-manifest/context.jsonld",
  
  "metadata": {
    "@type": "http://schema.org/Book",
    "conformsTo": "https://readium.org/webpub-manifest/profiles/pdf",
    "title": "Moby-Dick",
    "author": "Herman Melville",
    "language": "en"
  },

  "links": [
    {"rel": "self", "href": "http://example.org/manifest.json", "type": "application/webpub+json"},
  ],

  "readingOrder": [
    {
      "href": "http://example.org/mobydick-part1.pdf", 
      "type": "application/pdf", 
    },
    {
      "href": "http://example.org/mobydick-part2.pdf", 
      "type": "application/pdf", 
    }
  ],
  
  "resources": [
    {"rel": "cover", "href": "http://example.org/cover.jpeg", "type": "image/jpeg", "height": 600, "width": 300}
  ]
}
```

## Introduction

The goal of this specification is to provide a profile of the [Readium Web Publication Manifest](https://readium.org/webpub-manifest) dedicated to PDF documents.

It is mostly used as a basis for the specification of a method for protecting PDF files using LCP, i.e. [LCP for PDF](https://readium.org/lcp-specs/notes/lcp-for-pdf.html). 

This profile relies on:

* a declaration of [conformance with this Profile](#1-declaring-conformance-with-the-pdf-profile),
* a [restriction on the resources of the reading order](#2-restrictions-on-the-resources-of-the-reading-order),

## 1. Declaring conformance to the PDF Profile

To declare that it conforms to the PDF Profile, a Readium Web Publication Manifest <strong class="rfc">must</strong> include a `conformsTo` key in its `metadata` section, with `https://readium.org/webpub-manifest/profiles/pdf` as one of its value.

## 2. Listing resources

In addition to the normal requirements of a `readingOrder`, all Link Objects <strong class="rfc">must</strong> point strictly to PDF resources, with no fragment identifier. 
