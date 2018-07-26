# Collection Roles

The Readium Web Publication Manifest specification defines the following roles for collections in its main specification document and extensions:

## Core Roles

| Role  | Semantics | Compact? | Required? | Reference |
| ----- | --------- | -------- | --------- | --------- |
| readingOrder  | Identifies a list of resources in reading order for the publication.  | Yes  | Yes  | [Main Specification](README.md) |
| resources  | Identifies resources that are necessary for rendering the publication.  | Yes  | No  | [Main Specification](README.md) |

## Extensions

| Role  | Semantics | Compact? | Required? | Reference |
| ----- | --------- | -------- | --------- | --------- |
| landmarks  | Identifies the collection that contains a list of points of interest.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
| loa  | Identifies the collection that contains a list of audio resources.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
| loi  | Identifies the collection that contains a list of illustrations.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
| lot  | Identifies the collection that contains a list of tables.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
| lov  | Identifies the collection that contains a list of videos.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
| page-list  | Identifies the collection that contains a list of pages.  | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
| toc  | Identifies the collection that contains a table of contents. | Yes  | No  | [EPUB Extension](extensions/epub.md#collection-roles) |
