# JSON / JSON Lines

Log Lion supports two closely related structured logging formats built on JSON:

- **JSON Lines** (`.jsonl`) — Each line of the file is an independent, complete JSON object. Defined at <https://jsonlines.org/>. This is the most common format for structured logging because it is streamable, appendable, and trivially parseable line by line.
- **JSON** (`.json`) — A single JSON document, typically an array of objects at the top level, where each object is one log entry.

Both formats are widely adopted across modern logging ecosystems: OpenTelemetry, Elasticsearch Common Schema (ECS), Compact Log Event Format (CLEF / Serilog), Logstash, Datadog Agent, Fluentd, and virtually every cloud-native logging pipeline emit some variant of newline-delimited JSON.

## Tips

- Log Lion automatically discovers the schema of your JSON log by sampling entries. Columns appear as keys are encountered — you do not need to configure anything for well-formed files.
- Nested objects are flattened using dot notation up to the **Nested Depth Limit** (e.g., `request.method`, `response.status`). Increase this limit to expose deeper fields.
- Log Lion recognizes many common timestamp field names automatically (`timestamp`, `@timestamp`, `time`, `ts`, `@t`, `date`, and others). Use **View > Set Time Column** to override the auto-detected choice.
- The **Time Zone Adjustment** option is essential when log timestamps are in UTC but you want to view them in local time.
- If your log files contain file name references for source code (e.g., a `file` field), configure **Source Directories** so Log Lion can locate and open those files directly from the detail pane.
- The **Reverse Index by Time Ascending** option is useful for logs written newest-first.

## Columns

Columns are discovered dynamically from the JSON content. Each unique key found across the sampled entries becomes a column. Nested objects are represented with dot-separated key paths (e.g., `context.userId`) up to the configured **Nested Depth Limit**. Array elements appear as `key[0]`, `key[1]`, and so on.

## Properties

Properties are read-only metadata shown in the **Properties** pane.

| Property | Description |
| --- | --- |
| Created | File creation date and time. |
| Modified | Date and time the file was last modified. |
| Accessed | Date and time the file was last accessed. |
| File Size | Size of the file in bytes. |
| Index Size | Number of indexed entries. |
| Average Line Size | Average number of bytes per entry. |
| Message Field | Name and column number of the auto-detected primary message column. |
| Time Field | Name and column number of the auto-detected timestamp column. |
| Nav Field | Name and column number of the auto-detected navigation context column (e.g., logger, service, thread). |
| Filename Field | Name and column number of the auto-detected source code file name column, used to open source files from the detail pane. |

## Options

Options are configurable settings found under **File > Source Settings > JSON**.

| Option | Values | Description |
| --- | --- | --- |
| Load / Save Full Index | Yes, No | When enabled, the entry offset index is persisted to disk for fast reopening of large files. |
| File cache limit (MB) | Numeric | Maximum amount of the file to memory-map at once. Increase for faster random access when RAM permits. |
| Nested Depth Limit | Numeric (default 3) | Maximum depth of nested JSON objects to flatten into columns. Deeper content is stored as a raw JSON string. |
| Source Directories | Semicolon-separated paths | Directories Log Lion searches when resolving source code file names found in log entries. |
| Time Zone Adjustment | UTC to Local, UTC, User Offset | Controls how timestamps are adjusted for display. *UTC to Local* converts UTC timestamps to the local system time zone. |
| Custom Time Zone Offset | Numeric (minutes from UTC) | Offset applied when **Time Zone Adjustment** is set to *User Offset*. |
| Date Time Format | Format string (default `%H:%M:%OS`) | POSIX strptime-style format string used to parse the timestamp column. Supports fractional seconds via `%OS`. |
| Reverse Index by Time Ascending | Yes, No | When enabled, entries are re-ordered so that the earliest timestamp appears first, regardless of the physical order in the file. |

---

[All documentation](README.md)
