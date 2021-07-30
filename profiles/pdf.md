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

