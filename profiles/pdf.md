# PDF Profile

**Editors:**

* Michael Benowitz ([NYPL](https://www.nypl.org))

**Participate:**

* [GitHub readium/webpub-manifest](https://github.com/readium/webpub-manifest)
* [File an issue](https://github.com/readium/webpub-manifest/issues)

## Introduction

The display of static PDF files can be governed by the standard Webpub Manifest guidelines, as the format was specifically conceived of to provide a constent, portable format. However there are many optional features, mainly pertaining to accessibility and preservation, that can be communicated to users. These features may be used in a client context or used in other ways by consuming applications.

This profile is intended to ensure that full functionality of PDF resources is documented in Webpub Manifests, and conversely ensuring that PDFs with limited functionality are documented.

## 1. Declaring conformance with PDF Profile

In order for a Webpub Manifest to conform to this profile it <strong class="rfc">must</strong> include a `conformsTo` key in the `metadata` section, with `http://readium.org/webpub-manifest/profiles/pdf` as the value.

## 2. `readingOrder` resource restrictions

A conforming Webpub Manifsest <strong class="rfc">must</strong> only refererence PDF documenents with a media type of `application/pdf` in the `readingOrder` section.

## 3. PDF Encryption

If a Webpub Manifest contains encrypted resources, those resources <strong class="rfc">must</strong> include an [encryption object](https://github.com/readium/webpub-manifest/blob/master/modules/encryption.md#encryption-object) in the `properties` object attached to each `Link` object.


## 4. Link Parameters

A Manifest <strong class="rfc">may</strong> include the following parameters in the `href` attribute of elements in the `readingOrder` and `toc` sections.

| Key   | Semantics | Type     | Values    | 
| ----- | --------- | -------- | --------- | 
| [start](#start) | Specifies the initial page of the PDF to display when displaying this resource  | Integer  | 1 to (page count of current resource)  | 

### start

In some cases a system may wish to display a PDF but skip certain pages that appear at the beginning of the file, including:
- Skipping blank pages
- Skipping repetitive or unnecessary front matter
- Providing a direct link to a chapter, section or sub-section within a larger PDF resources

The `start` parameter conveys this information in a way that aligns with other standards for displaying and consuming PDF resources. This parameter is indexed from 1 to match the mental model for most users.
```
https://example.com/example.pdf?start=1 # First page, equivalent to no parameter specification
https://example.com/example.pdf?start=2 # Second page
https://example.com/example.pdf?start=10 # Arbitrary page position
```