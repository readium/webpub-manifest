# Encryption 

The Encryption Module defines how a given resource has been encrypted or obfuscated, and provides relevant information for decryption by a User Agent.

## Encrypted

The `encrypted` key is a Link Property and contains an Encryption Object.

## Encryption Object

The Encryption Object has the following keys:

| Key   | Semantics | Type     | Required? |
| ----- | --------- | -------- | --------- |
| [algorithm](#algorithm)  | Identifier of the algorithm used to encrypt the resource.  | URI  | Yes |
| [scheme](#scheme)  | Identifier of the encryption scheme used to encrypt the resource.  | URI  | No |
| [profile](#profile)  | Identifier of the encryption profile used to encrypt the resource.  | URI  | No |
| [compression](#compression)  | Compression method used on the resource before encryption. | String | No |
| [originalLength](#originalLength)  | Original length of the resource in bytes before compression and/or encryption. | Integer  | No |

### Compression 

The `compression` property <strong class="rfc">should</strong> only be present if the content has been compressed before encryption. The absence of this property, or the presence of an empty string as a value, indicate that the content was not compressed before encryption.  

The only allowed value for the compression property is currently:

| Value     | Semantics |
| --------- | --------- | 
| `deflate` | Deflate algorithm, as defined by the Zip specification |

### originalLength

The `originalLength` property <strong class="rfc">should</strong> only be present if the `compression` property is present and has a non-null value. 


## LCP Encrypted Resource

Any type of publication can be protected by the [Readium LCP](https://readium.org/lcp-specs/releases/lcp/latest) DRM. 

On each encrypted resource, `scheme`, `profile` and `algorithm` are required and their values are defined by the LCP specification and the definition of the LCP profile which is applied.

*Example of an XHTML resource encrypted using LCP with a basic profile*

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
*Example of a PDF resource encrypted using LCP with a 1.0 profile*

```json
{
  "href": "publication.pdf",
  "type": "application/pdf",
  "properties": {
    "encrypted": {
      "scheme": "http://readium.org/2014/01/lcp",
      "profile": "http://readium.org/lcp/profile-1.0",
      "algorithm": "http://www.w3.org/2001/04/xmlenc#aes256-cbc"
    }
  }
}
```

## Font obfuscation

Font obfuscation is only used in EPUB publications (see the [EPUB Profile](../profiles/epub.md)). 

Font obfuscation is indicated by the `algorithm` property, which MUST take the value `http://www.idpf.org/2008/embedding`, as defined in [EPUB 3.2 - Specifying Obfuscated Resources](https://www.w3.org/publishing/epub3/epub-ocf.html#obfus-specifying).

*Example of an obfuscated font*

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
