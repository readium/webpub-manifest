# Encryption 

The `encrypted` key is a Link Property.

The `encrypted` key contains an Encryption Object that indicates how a given resource has been encrypted or obfuscated, and provides relevant information for decryption.

## Encryption Object

The Encryption Object has the following keys:

| Key   | Semantics | Type     | Required? |
| ----- | --------- | -------- | --------- |
| [algorithm](#algorithm)  | Identifier of the algorithm used to encrypt the resource.  | URI  | Yes |
| [scheme](#scheme)  | Identifier of the encryption scheme used to encrypt the resource.  | URI  | No |
| [profile](#profile)  | Identifier of the encryption profile used to encrypt the resource.  | URI  | No |
| [compression](#compression)  | Compression method used on the resource.  | String | No |
| [originalLength](#originalLength)  | Original length of the resource in bytes before compression and/or encryption. | Integer  | No |


## LCP Encrypted Resource

Publication of any type can be protected by the [Readium LCP](https://readium.org/lcp-specs/releases/lcp/latest) DRM.

In such a case, allowed values for the properties of the Encryption Object are:

| Property    | Value | 
| ----------- | ----- | 
| scheme      | `http://readium.org/2014/01/lcp` |
| profile     | see the [LCP Encryption Profile Registry](https://readium.org/lcp-specs/registries/profiles) |
| algorithm   | `http://www.w3.org/2001/04/xmlenc#aes256-cbc` |
| compression | `deflate` |

> **Note**: AES256-CBC is currently the only algorithm supported by LCP profiles; this could evolve in the future with the addition of a CGM variant. 

*Example for a resource encrypted using LCP*

```json
{
  "href": "chapter_001.xhtml",
  "type": "application/xhtml+xml",
  "properties": {
    "encrypted": {
      "scheme": "http://readium.org/2014/01/lcp",
      "profile": "http://readium.org/lcp/basic-profile",
      "algorithm": "http://www.w3.org/2001/04/xmlenc#aes256-cbc",
      "compression": "deflate",
      "originalLength": 13810
    }
  }
}
```

## Font obfuscation

Font obfuscation is only used in EPUB publications (see the [EPUB Profile](../profiles/epub.md)). 

Font obfuscation is indicated by the `algorithm` property, which MUST take the value `http://www.idpf.org/2008/embedding`, as defined in [EPUB 3.2 - Specifying Obfuscated Resources](https://www.w3.org/publishing/epub3/epub-ocf.html#obfus-specifying)

*Example for an obfuscated font*

```json
{
  "href": "fonts/sandome.obf.ttf",
  "type": "application/vnd.ms-opentype",
  "properties": {
    "encrypted": {
      "algorithm": "http://www.idpf.org/2008/embedding"
    }
  }
}
```
