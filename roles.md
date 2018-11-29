# Collection Roles

The Readium Web Publication Manifest specification defines the following roles for collections in its main specification document and extensions:

## Core Roles

| Role  | Definition | Compact Collection? | Required? | Reference |
| ----- | ---------- | ------------------- | --------- | --------- |
| `readingOrder`  | Identifies a list of resources in reading order for the publication.  | Yes  | Yes  | [Main Specification](README.md#21-sub-collections) |
| `resources`  | Identifies resources that are necessary for rendering the publication.  | Yes  | No  | [Main Specification](README.md#21-sub-collections) |
| `toc`  | Identifies the collection that contains a table of contents. | Yes  | No  | [Main Specification](README.md#5-table-of-contents) |

## Extensions

| Role  | Definition | Compact Collection? | Required? | Reference |
| ----- | ---------- | ------------------- | --------- | --------- |
| `landmarks`  | Identifies the collection that contains a list of points of interest.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
| `loa`  | Identifies the collection that contains a list of audio resources.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
| `loi`  | Identifies the collection that contains a list of illustrations.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
| `lot`  | Identifies the collection that contains a list of tables.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
| `lov`  | Identifies the collection that contains a list of videos.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
| `page-list`  | Identifies the collection that contains a list of pages.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |


## OPDS 2.0

While OPDS itself is not an extension of the Readium Web Publication Manifest, it shares the same abstract model and syntax.

OPDS 2.0 catalogs can also contain Web Publications as defined by the Readium Web Publication Manifest.

For these reasons, it feels relevant to list the collection roles that are introduced in this related specification.

| Role  | Definition | Compact Collection? | Required? | Reference |
| ----- | ---------- | ------------------- | --------- | --------- |
| `navigation`  | An ordered list of links meant to browse a catalog in depth.  | Yes  | No  | [OPDS 2.0](https://drafts.opds.io/opds-2.0#11-navigation) |
| `publications`  | Contains a list of publications.  | No  | No  | [OPDS 2.0](https://drafts.opds.io/opds-2.0#12-publications) |
| `images`  | Visual representations for a publication.  | Yes  | No  | [OPDS 2.0](https://drafts.opds.io/opds-2.0#13-images) |
| `facets`   | Links meant to obtain a sub-set of the current list of publications, or the same list in a different order.  | No  | No  | [OPDS 2.0](https://drafts.opds.io/opds-2.0#14-facets) |
| `groups`   | Structural element in a catalog meant to contain `navigation` or `publications` collections.  | No  | No  | [OPDS 2.0](https://drafts.opds.io/opds-2.0#15-groups) |

