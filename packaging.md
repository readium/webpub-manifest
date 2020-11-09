
# Readium Packaging Format (RPF)

This specification defines a file format for packaging into a single-file container the set of related resources and associated metadata that comprise a Readium Web Publication.

**Editors:**

* Hadrien Gardeur ([De Marque](http://www.demarque.com))
* Laurent Le Meur ([EDRLab](http://www.edrlab.org))

**Participate:**

* [GitHub readium/webpub-manifest](https://github.com/readium/webpub-manifest)
* [File an issue](https://github.com/readium/webpub-manifest/issues)


## 1. Introduction

A Readium Web Publication if often distributed unpackaged on the Web, but it also may be packaged for easy distribution as a single file. 

Last but not least, a Readium Web Publication Manifest may be included in an EPUB 3 formatted publication and directly reference media resources in its reading order, something nearly impossible with EPUB 3. This method, used in particular for digital visual narratives (Divina), pave the way for hybrid publications, which are both valid EPUB 3 and contain advanced features - like guided navigation in the Divina use case - easily accessible to Readium based reading applications.  

## 2. Terminology 

<dl>
 <dt id="codec">Codec content types</dt>
 <dd>Content types that have intrinsic binary format qualities, such as video and audio media types which are already designed for optimum compression, or which provide optimized streaming capabilities.</dd>
 <dt id="non-codec">Non-Codec content types</dt>
 <dd>Content types that benefit from compression due to the nature of their internal data structure, such as file formats based on character strings (for example, HTML, CSS, etc.).</dd>
 <dt id="package">Package</dt>
 <dd>Single-file container for the set of constituent resources and associated metadata that comprise a digital publication.</dd>
 <dt id="root-directory">Root Directory</dt>
 <dd>Base directory of the Package file system.</dd>
</dl>

## 3. Packaging format

For packaging the set of constituent resources and associated metadata that comprise a digital publication, this specification uses the ZIP format as specified in [ISO/IEC 21320-1:2015](http://standards.iso.org/ittf/PubliclyAvailableStandards/c060101_ISO_IEC_21320-1_2015.zip) and [zip](https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT).

In the absence of values defined at the level of a Readium Web Publication [profile](profiles/):

- The media type <strong class="rfc">must</strong> be `application/webpub+zip`
- The file extension <strong class="rfc">must</strong> be `.webpub`

A publication where any resource is encrypted using a DRM <strong class="rfc">must</strong> use a different media type and file extension.

## 4. Compression of resources

When stored in a Package, resources with [Non-Codec content types](#non-codec) <strong class="rfc">should</strong> be compressed and the Deflate compression algorithm MUST be used. This practice ensures that file entries stored in the Package have a smaller size.

Resources with [Codec content types](#codec) <strong class="rfc">should</strong> be stored without compression. In such case, compression would introduce unnecessary processing overhead at production time (especially with large resource files) and would impact audio/video playback performance at consumption time.

## 5. File Structure

A [Package](#package) <strong class="rfc">must</strong> include in its [Root Directory](#root-directory) a file named `manifest.json`, which <strong class="rfc">must</strong> be in the format defined for [Readium Web Publication Manifests](README.md).

The contents of `manifest.json` <strong class="rfc">must</strong> not be encrypted.

A Package <strong class="rfc">must</strong> also include all resources within the bounds of the digital publication, i.e. the finite set of resources obtained from the union of resources listed in the `reading order` and `resources` Link Arrays of the Readium Web Publication Manifest.

These resource files <strong class="rfc">may</strong> be in any location descendant from the Root Directory, or in the Root Directory itself.

Contents within the Package <strong class="rfc">must</strong> reference these resources via [relative-URL strings](https://url.spec.whatwg.org/#relative-url-string).


## 7. Hybrid EPUB + RPF Packages

If a Readium Web Publication Manifest is included in an EPUB file, the following restriction apply:

- The OPF of the primary rendition <strong class="rfc">must</strong> include a link to the manifest where the link relation is set to `alternate`


*Example 1: Reference to a Manifest file in an EPUB 3 OPF structure*

```xml
<link rel="alternate" 
      href="manifest.json" 
      media-type="application/webpub+json" />
```
