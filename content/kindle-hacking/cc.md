---
title: cc.db
---

# cc.db

The file at `/var/local/cc.db` is used by [com.lab126.ccat][] and stores
information regarding the indexed books and collections.

It contains the following tables:

- `BACKFILLING`
- `Collation`
- `Collections`
- `CollectionsJournal`
- `DBOK`
- `Entries`
- `Locale`
- `Series`
- `SharedStates`
- `Versions`

## BACKFILLING

Unknown.

| Column | Type | Description |
| ------ | ---- | ----------- |
| x_version | BLOB (int) | |

## Collation

Described as "current collation locale (string)". Otherwise unknown.

| Column | Type | Description |
| ------ | ---- | ----------- |
| collation | BLOB | |

## Collections

Table where the collections (user-created "series" grouping). This is a N-to-N
mapping between a collection UUID and a collection member UUID.

The same data can potentially be found from the [Entries][] table, it is
unsure which one is the source of truth.
{ .note }

| Column | Type | Description |
| ------ | ---- | ----------- |
| i_collection_uuid | BLOB (UUID) | Collection side |
| i_member_uuid | BLOB (UUID) | Member side |
| i_order | BLOB (int) | Order of member within collection |
| i_member_cde_type | BLOB | cdeType of the member |
| i_member_cde_key | BLOB | cdeKey of the member |
| i_member_is_present | BLOB | member is present on device |
| i_is_sideloaded | BLOB | Item sideloaded/downloaded value |

There is a `UNIQUE (i_collection_uuid, i_member_uuid)` constraint on this
table.

## CollectionsJournal

Unknown.

| Column | Type | Description |
| ------ | ---- | ----------- |
| i_event_time | BLOB (datetime) | Event time |
| j_event | BLOB (json) | Contains the collections event |

## DBOK

Unknown.

| Column | Type | Description |
| ------ | ---- | ----------- |
| x_ok | BLOB | |

The `x_ok` column is defined as:<br/>
`PRIMARY KEY `<br/>
`NOT NULL `<br/>
`UNIQUE `<br/>
`CHECK (x_ok = 1) `<br/>
`CHECK (typeof(x_ok) = "integer")`
{ .note }

## Entries

Main table containing the whole catalog. Everything is here, including books
and collections.

| Column | Type | Description |
| ------ | ---- | ----------- |
| p_uuid | BLOB PRIMARY KEY NOT NULL (UUID) | ID of entry |
| p_type | BLOB (string) | App-defined type |
| p_location | BLOB (string) | Location (URI to content) |
| p_lastAccess | BLOB (datetime) | Last access |
| p_modificationTime | BLOB (datetime) | File modification |
| p_isArchived | BLOB (bool) | Is archived item? |
| p_titles_0_nominal | BLOB (string) | Pieces of first title (LString) |
| p_titles_0_collation | BLOB COLLATE icu (string) | Used only for filtering and sorting |
| p_titles_0_pronunciation | BLOB (string) | Return value is always j_titles |
| j_titles | BLOB (array of hashmap) | All titles (array of LString) |
| p_titleCount | BLOB (int) | How many titles (unsigned) |
| p_credits_0_name_collation | BLOB COLLATE icu (string) | Collation string for first credit |
| j_credits | BLOB (array of hashmap) | All credits |
| j_creditCount | BLOB (int) | How many credits (unsigned) |
| j_collections | BLOB (array of UUID) | This entry is a member of these collections |
| p_collectionCount | BLOB (int) | How many collections (used for filtering) |
| j_members | BLOB (array of UUID) | This collection has these members |
| p_memberCount | BLOB (int) | How many members (used for filtering) |
| p_lastAccessedPosition | BLOB | For books: LPR; MP3/Audio: last timestamp played |
| p_publicationDate | BLOB (UTC datetime) | Publication date (Whether this should be interpreted as a floating date or a timepoint depends on the content, and we have no reliable way of knowing which is which—currently, use type (EBook, Blog, Magazine, etc.) to decide |
| p_expirationDate | BLOB (UTC datetime) | Expiration date. (Time when this item should be purged. Typically used for periodicals and blogs. Will be null for books, etc. which should not be purged) |
| p_publisher | BLOB (string) | Publisher |
| p_isDRMProtected | BLOB (bool) | Has DRM? |
| p_isVisibleHome | BLOB (bool) | Should appear in Home? |
| p_isLatestItem | BLOB (bool) | Is this the latest item in a group such as a periodical? |
| p_isDownloading | BLOB (bool) | Is currently downloading? |
| p_isUpdateAvailable | BLOB (bool) | Is an update available, say to a KDK app? |
| p_virtualCollectionCount | BLOB (int) | Number of items in a virtual collection, such as Periodical Back Issues or Archived Items |
| p_languages_0 | BLOB (string) | Primary content language |
| j_languages | BLOB (array of IETF BCP 47) | All content languages |
| p_languageCount | BLOB (int) | How many languages (unsigned) |
| p_mimeType | BLOB (string) | Mime type |
| p_cover | BLOB (string) | Cover location (URI) |
| p_thumbnail | BLOB (string) | Thumbnail location (URI) |
| p_diskUsage | BLOB (int) | Bytes on disk |
| p_cdeGroup | BLOB (string) | CDE Grouping ID (to group periodicals, Retail ASIN) |
| p_cdeKey | BLOB (string) | CDE Key/ASIN |
| p_cdeType | BLOB (string) | CDE Type |
| p_version | BLOB (string) | Content version |
| p_guid | BLOB (string) | Content GUID |
| j_displayObjects | BLOB (array of hashmap) | What to display |
| j_displayTags | BLOB (array of string) | Tags to display (array of enum (string) values) |
| j_excludedTransports | BLOB (array of string) | Disallowed networks for download |
| p_isMultimediaEnabled | BLOB (bool) | For Luna books |
| p_watermark | BLOB (string) | Watermark |
| p_contentSize | BLOB (int) | Human-perceived length |
| p_percentFinished | BLOB (float) | What percentage of the total content length the lastAccessedPosition represents |
| p_isTestData | BLOB (bool) | Is this test data? |
| p_contentIndexedState | BLOB (int) | The state of indexing the content of this entry |
| p_metadataIndexedState | BLOB (int) | The state of indexing the metadata of this entry |
| p_noteIndexedState | BLOB (int) | The state of indexing the notes/annotations of this entry |
| p_credits_0_name_pronunciation | BLOB (string) | Authors pronunciation |
| p_metadataStemWords | BLOB | Metadata stem words (not used currently. Introduced in J3) |
| p_metadataStemLanguage | BLOB | Language of the metadata (not used currently. Introduced in J3) |
| p_ownershipType | BLOB (int) | Ownership type |
| p_shareType | BLOB | Support share feature in Discovery App |
| p_contentState | BLOB (int) | Downloaded / sideloaded value |
| p_metadataUnicodeWords | BLOB (string) | Searchable metadata for contents |
| p_homeMemberCount | BLOB (int) | Number of downloaded/sideloaded books inside a collection |
| j_collectionsSyncAttributes | BLOB | Collection sync attributes |
| p_collectionSyncCounter | BLOB (int) | Max whispersync counter for a collection |
| p_collectionDataSetName | BLOB (string) | Whispersync dataset name for collection |
| p_originType | BLOB (int) | Content Origin Type |
| p_pvcId | BLOB | PVC identifier |
| p_companionCdeKey | BLOB (string) | Companion CDE ASIN. For an audio book, we may have a companion ebook and vice-versa |
| p_seriesState | BLOB (int) | Series Identifier |
| p_totalContentSize | REAL (long) | Total size (including sidecars) occupied by the content on the device |
| p_visibilityState | BLOB (int) | Used to determine whether an entry should be visible or not |
| p_isProcessed | BLOB (int) | Flag to specify whether the entry should be processed or has already been processed |
| p_readState | BLOB (int) | Indicates the read state of an entry |
| p_subType | BLOB (int) | Integer to specify the sub type of the entry |

## Locale

Unknown.

| Column | Type | Description |
| ------ | ---- | ----------- |
| locale | BLOB (string) | |

## Series

Seems to be some kind of Amazon-exclusive collection, perhaps related to the
Amazon store?

| Column | Type | Description |
| ------ | ---- | ----------- |
| d_seriesId | BLOB (string) | Identifier of a series |
| d_itemCdeKey | BLOB (string) | cdeKey of the member |
| d_itemPosition | BLOB (double) | Position of a member in the series |
| d_itemPositionLabel | BLOB (string) | Display label of a member |
| d_itemType | BLOB (string) | Type of the member. Same as p_type in [Entries][] table |
| d_seriesOrderType | BLOB (string) | Order type of series |

There is a `UNIQUE (d_seriesId, d_itemCdeKey)` constraint on this table.

## Versions

Unknown.

| Column | Type | Description |
| ------ | ---- | ----------- |
| x_table | BLOB (string) PRIMARY KEY NOT NULL UNIQUE | |
| x_version | BLOB (int) | |



[com.lab126.ccat]: ../kindle-apps-and-services/com.lab126.ccat.html
[Entries]: #Entries
