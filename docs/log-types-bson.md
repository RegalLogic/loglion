# BSON

BSON (`.bson`) is the binary document format used by MongoDB. A BSON log file is simply one document after another with nothing between them — the shape written by `mongodump`, by MongoDB change stream and oplog captures, and by services that persist structured events in MongoDB's own encoding rather than as text.

Unlike a text log, every document declares its own length, so Log Lion can move directly to any record without reading the ones before it: jumping to the last row of a large file is as fast as jumping to the first. BSON also carries real types. A date is a date rather than a string that has to be recognized, a number is a number, and binary payloads stay binary — so the time column, severity icons, and the timeline are derived from the data itself instead of from pattern matching.

## Common Use Cases

- **MongoDB collection dumps** — Read a collection exported with `mongodump` directly, without converting it to JSON first and without a MongoDB server running.
- **Application event stores** — Inspect services that write structured events as BSON because it is the encoding their database already speaks.
- **Change streams and oplog captures** — Follow a recorded stream of database operations, using the timeline to see when activity clustered.
- **Recovering damaged captures** — Open a dump that was truncated mid-write or has a corrupt region in the middle, and still read everything either side of the damage.

## Fields and Columns

Columns are discovered from the documents themselves rather than being fixed, in the order the fields first appear. Nested documents are flattened into dotted columns, so `{ "context": { "host": "node09" } }` becomes a column named `context.host`. How deep that goes is controlled by **Nested Depth Limit**, and anything below the limit is shown as compact JSON in a single cell. Arrays are handled separately — see **Array Handling** below.

A column's name is exactly the field name from the document, because that is also the name used in filter expressions. A field flattened to `context.host` is filtered as `$context.host`. Field names containing brackets need the qualified form, `$(items[0])`.

Values are displayed according to their BSON type:

| BSON Type | Displayed As |
| --- | --- |
| Double, 32-bit integer, 64-bit integer | The number, with no thousands separators or units, so that it can be compared numerically in a filter. |
| Decimal128 | The decimal value, decoded rather than shown as raw bytes. |
| String, JavaScript, Symbol | The text, with any malformed byte sequences replaced. |
| UTC datetime | A formatted date and time, controlled by the **Date Time Format** and time zone options. |
| ObjectId | The 24-character hexadecimal identifier. The detail pane also shows the timestamp embedded in it. |
| Binary | Per the **Binary Display** option. UUID subtypes are shown in the canonical 8-4-4-4-12 form. |
| Boolean, Null | *true* / *false*, and an empty cell for null. |
| Regular expression | `/pattern/options`. |
| Timestamp | The date followed by its increment. This is a MongoDB replication value, not a wall-clock time, so it is not used as the time column unless nothing else is available. |
| Embedded document, Array | Flattened into columns, or shown as JSON, according to the options below. |
| Min key, Max key, Undefined, DBPointer, JavaScript with scope | Shown in a readable form. These are deprecated or special-purpose types that appear rarely. |

## Tips

- Log Lion detects `.bson` files by validating the structure of the first documents, so a file is recognized on its content rather than on its extension alone.
- The time column is chosen by type first: a real date field wins, and a field named `ts`, `timestamp`, `time`, `@t`, or `date` is preferred when there is more than one. Failing that, an integer that looks like a Unix epoch, an ObjectId's embedded timestamp, or a text timestamp is used.
- Severity icons come from a field named `level`, `severity`, `severitynumber`, and similar, understanding both text levels and the numeric OpenTelemetry, syslog, and Python schemes.
- A file whose documents are not in time order is sorted for display, so Goto Time and the timeline work correctly. Set **Order by Time** to *File order* to see the file's own order instead.
- The first open indexes the file and caches the result alongside it as `<filename>.bson.llidx`, so reopening is immediate. Changing an option that affects the column set, or changing the file, rebuilds the index automatically.
- Set **Detail Pane** to *Full analysis* to see a document's complete field list with each field's BSON type, an ObjectId broken into its parts, binary subtypes, and a hex dump of the raw document. This is also where fields that did not become columns can be seen.
- A selection can be written back out as BSON with *File\Save As*, choosing the **BSON** file type. The result is an ordinary BSON file that Log Lion and other BSON tools can read.

## Damaged Files

A BSON file depends on each document's length being correct. If a length is wrong — because the file was truncated mid-write, or a region was overwritten — Log Lion shows a row marked **Corrupt** describing where the damage is and how many bytes were skipped, then looks ahead for the next genuine document and carries on. Recovery is confirmed against several consecutive documents before it is accepted, so a damaged file does not turn into pages of nonsense rows. Use the **Corruption Recovery** option to stop at the first problem instead.

## Entry Types

Each row's icon and color reflect the severity field, when the documents have one:

| Type | Description |
| --- | --- |
| **Fatal** | A critical, fatal, alert, emergency, or panic level. |
| **Error** | An error or severe level. |
| **Warn** | A warning level. |
| **Notice** | A notice level. |
| **Info** | An informational level, and the default when the documents carry no severity field. |
| **Debug** | A debug or verbose level. |
| **Trace** | A trace level. |
| **Success** | A success, ok, or passed level. |
| **Unknown** | A severity field was present but its value was not recognized. |
| **Corrupt** | A region of the file that does not contain a readable document. See *Damaged Files* above. |

## Options

Options are configurable settings found under **File > Source Settings > BSON**.

| Option | Values | Description |
| --- | --- | --- |
| Load / Save Full Index | Yes, No | When enabled, the index is cached alongside the file so subsequent opens are immediate. |
| File cache limit (MB): | Numeric (megabytes) | How much of the file is memory mapped for fast access. The remainder is streamed. |
| Nested Depth Limit | Numeric (0 - 16) | How many levels of embedded documents are flattened into their own columns. Anything deeper is shown as JSON in a single cell. |
| Array Handling | JSON Text, Index Columns (name.0), Index Columns (name[0]), First Element, Count | How arrays are presented. *JSON Text* keeps an array in one cell. The *Index Columns* settings give each element its own column, the `name.0` form being the one that can be filtered without qualifying the name. *First Element* and *Count* show just that. |
| Max Columns | Numeric (8 - 512) | An upper bound on how many columns are created, for documents with a very large or open-ended set of fields. Fields beyond the limit are reported in the **Fields Omitted** property and remain visible in the detail pane. |
| Binary Display | Hex, Base64, Size only | How binary values are shown in the grid. |
| Corruption Recovery | Resync, Stop at first error, Off | What to do when a document's length is not valid. *Resync* reports the damage and looks for the next genuine document. See *Damaged Files* above. |
| Order by Time | Auto, File order | *Auto* sorts the documents by time when they are not already in order, which is what Goto Time and the timeline need. *File order* keeps the order the file has. |
| Timeline | Enabled, Disabled | Whether the timeline pane is shown for this source. |
| Time Zone Adjustment | UTC to Local, Custom Time Zone Offset, Raw | Controls how times are adjusted for display. *Raw* leaves them in UTC. |
| Custom Time Zone Offset | ±HH:MM | The offset applied when **Time Zone Adjustment** is set to *Custom Time Zone Offset*. |
| Date Time Format: | Format string | Controls how date and time values are formatted, using tokens similar to the POSIX `strftime()` function. The default, `%Y-%m-%d %H:%M:%OS`, sorts correctly when compared as text. |
| Detail Pane | Full analysis, Plain text | *Plain text* shows the selected cell with syntax highlighting. *Full analysis* shows the whole document, each field's BSON type, decoded identifiers, and a hex dump. |

## Properties

Properties are read-only metadata shown in the **Properties** pane.

| Property | Description |
| --- | --- |
| File | Full path of the file. |
| Size | Size of the file. |
| Documents | Number of documents indexed, including any corrupt regions. |
| Fields | Number of columns discovered. |
| Time Order | Whether the documents were already in ascending time order, in descending order, or unordered. |
| Corrupt Regions | Number of damaged regions found, and how many of them reading resumed after. |
| Fields Omitted | Number of fields that exceeded **Max Columns** and so did not become columns. |
| Values Not Mapped | Number of values found in documents after the column set was final. They are shown in the detail pane rather than the grid. |
| Ambiguous Fields | Fields whose flattened name collides with a literal field of the same name, such as a field actually called `a.b`. |
| Created | File creation date and time. |
| Modified | Date and time the file was last modified. |

---

[All documentation](README.md)
