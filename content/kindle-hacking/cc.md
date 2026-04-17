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

Main table containing the whole catalog. Everything is here, including books,
collections, series containers, dictionaries, and system entries.

The `p_titles_0_collation` and `p_credits_0_name_collation` columns use
`COLLATE icu`, which requires the ICU extension. The Kindle's `com.lab126.ccat`
service has ICU built in, but the standalone `sqlite3` CLI on the device does
not. This means **INSERT operations on this table will fail** when using the
`sqlite3` CLI with `Error: no such collation sequence: icu`. Workarounds
include registering a stub collation in Python
(`conn.create_collation("icu", ...)`), or temporarily stripping `COLLATE icu`
from the schema via `PRAGMA writable_schema=ON`.
{ .note }

| Column | Type | Description |
| ------ | ---- | ----------- |
| p_uuid | BLOB PRIMARY KEY NOT NULL (UUID) | ID of entry |
| p_type | BLOB (string) | App-defined type. Known values: `Entry:Item` (books), `Entry:Item:Series` (series container), `Entry:Item:Dictionary`, `Entry:Item:Audible`, `Entry:Item:Feedback`, `Entry:Item:ADC`, `Entry:Item:HDC`, `Entry:Item:Images`, `Entry:Item:AKUGC`, `Entry:Item:HKUGC`, `Collection` |
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
| p_cdeType | BLOB (string) | CDE Type. Known values: `EBOK` (Amazon ebook or sideloaded book), `PDOC` (personal document), `AUDI` (Audible audiobook), `series` (series container entry) |
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
| p_contentState | BLOB (int) | Content acquisition state. Known values: `0` = sideloaded or cloud-only (not downloaded from Amazon), `1` = downloaded from Amazon or present on device. Sideloaded books default to `0`; setting to `1` alongside `p_originType = 0` makes the Kindle treat sideloaded content as owned purchased content |
| p_metadataUnicodeWords | BLOB (string) | Searchable metadata for contents. Terms are separated by U+FFFC (OBJECT REPLACEMENT CHARACTER). Typically contains the title and author name repeated: `title\ufffctitle\ufffcauthor\ufffcauthor` |
| p_homeMemberCount | BLOB (int) | Number of downloaded/sideloaded books inside a collection |
| j_collectionsSyncAttributes | BLOB | Collection sync attributes |
| p_collectionSyncCounter | BLOB (int) | Max whispersync counter for a collection |
| p_collectionDataSetName | BLOB (string) | Whispersync dataset name for collection |
| p_originType | BLOB (int) | Content origin type. Controls program badge display in the Kindle UI. Known values: `0` = purchased content (no program badges shown), `21` = Kindle Unlimited / subscription content (shows KU badge), `-1` = used for `Entry:Item:Series` container rows, `null`/empty = sideloaded with no origin info (Kindle may show KU and Prime Reading badges if the `p_cdeKey` matches a real Amazon ASIN) |
| p_pvcId | BLOB | PVC identifier |
| p_companionCdeKey | BLOB (string) | Companion CDE ASIN. For an audio book, we may have a companion ebook and vice-versa |
| p_seriesState | BLOB (int) | Series membership state. `0` = this entry is a member of a series (the firmware hides it from the main library and shows it inside the series container). `1` = default value for all other entries (books not in a series, dictionaries, collections, system items, and `Entry:Item:Series` container rows themselves) |
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

Maps books to series for the "Group Series in Library" feature (firmware
5.13.4+). For Amazon-purchased books, this table is populated by Amazon's
servers during metadata sync. It can also be populated manually for sideloaded
books to achieve the same visual grouping.

Each row links a single book to a series. A series with 9 books has 9 rows,
all sharing the same `d_seriesId`.

| Column | Type | Description |
| ------ | ---- | ----------- |
| d_seriesId | BLOB (string) | Series identifier. Format: `urn:collection:1:asin-{ASIN}` where `{ASIN}` is the Amazon series ASIN (e.g. `B08BX5D4LC`) |
| d_itemCdeKey | BLOB (string) | `p_cdeKey` of the member book from the [Entries][] table |
| d_itemPosition | BLOB (double) | 0-indexed position in the series (Book 1 = `0`, Book 2 = `1`, etc.) |
| d_itemPositionLabel | BLOB (string) | 1-indexed display label shown in the UI (e.g. `"1"`, `"2"`) |
| d_itemType | BLOB (string) | Type of the member. Always `Entry:Item` for books |
| d_seriesOrderType | BLOB (string) | Sort behavior. Observed values: `ordered`, `unordered` |

There is a `UNIQUE (d_seriesId, d_itemCdeKey)` constraint on this table.

### Series grouping requirements

For a series to appear in the Kindle library, three things must exist in the
database:

1. **Rows in the `Series` table** linking each book to the series (this table)
2. **An `Entry:Item:Series` row in the [Entries][] table** acting as the
   series container (see below)
3. **`p_seriesState = 0`** on each member book's `Entries` row, which tells the
   firmware to hide the individual book and show it inside the series instead

### Program badges (Kindle Unlimited, Prime Reading)

The Kindle UI shows program badges on books and series based on the
`p_originType` and `p_contentState` fields of the member book entries. This is
relevant when creating series from sideloaded books that use real Amazon ASINs
as their `p_cdeKey`.

| `p_originType` | `p_contentState` | Badge behavior |
| -------------- | ---------------- | -------------- |
| `0` | `1` | No badges. Treated as purchased/owned content |
| `21` | `1` | Shows **Kindle Unlimited** badge |
| `null`/empty | `0` | Shows **Kindle Unlimited** and **Prime Reading** badges (if the ASIN is eligible in Amazon's catalog) |

When building series from sideloaded books, set `p_originType = 0` and
`p_contentState = 1` on member books to suppress unwanted program badges.

### Entry:Item:Series rows

Each series has a corresponding row in the [Entries][] table with
`p_type = 'Entry:Item:Series'`. This is the series container that appears in
the library. Key fields:

| Field | Value / Description |
| ----- | ------------------- |
| `p_type` | `Entry:Item:Series` |
| `p_cdeKey` | The series ASIN (matches the `{ASIN}` portion of `d_seriesId`) |
| `p_cdeType` | `series` |
| `p_cdeGroup` | Same as `d_seriesId`: `urn:collection:1:asin-{ASIN}` |
| `p_mimeType` | `application/x-kindle-series` |
| `j_titles` | JSON: `[{"display":"Series Name","collation":"Series Name","language":"en","pronunciation":"Series Name"}]` |
| `j_credits` | Copied from the first book's author credits |
| `p_thumbnail` | Path to the first book's cover (e.g. `/mnt/us/system/thumbnails/thumbnail_{cdeKey}_EBOK_portrait.jpg`) |
| `j_members` | `[]` (empty — membership is tracked via the `Series` table, not this field) |
| `p_memberCount` | Total number of books in the series |
| `p_homeMemberCount` | Number of downloaded/sideloaded books in the series on the device |
| `j_displayObjects` | `[{"ref":"titles"},{"ref":"credits"}]` |
| `p_isArchived` | `1` |
| `p_isVisibleInHome` | `1` |
| `p_seriesState` | `1` (the container itself uses `1`; member books use `0`) |
| `p_visibilityState` | `1` |
| `p_originType` | `-1` |
| `p_contentIndexedState` | `2147483647` (INT_MAX — not applicable for series containers) |
| `p_virtualCollectionCount` | `p_memberCount + 1` |

## Versions

Unknown.

| Column | Type | Description |
| ------ | ---- | ----------- |
| x_table | BLOB (string) PRIMARY KEY NOT NULL UNIQUE | |
| x_version | BLOB (int) | |



[com.lab126.ccat]: ../kindle-apps-and-services/com.lab126.ccat.html
[Entries]: #Entries
