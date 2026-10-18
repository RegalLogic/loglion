# CSV — Comma-Separated Values

Comma-Separated Values (CSV) is a widely used text format for tabular data, standardized by [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt). Each row represents a log entry and each column a field, separated by a configurable delimiter (default comma). CSV files are commonly exported from databases, spreadsheet tools, event tracing frameworks (ETW), web analytics platforms, and cloud logging services.

## Tips

- If the first row contains column headers, enable the **First Row** option so Log Lion uses those names as column labels.
- Log Lion auto-detects a timestamp column and uses it for timeline analysis. Point this out manually with **View > Set Time Column** if auto-detection misses it.
- When a field contains embedded commas or line breaks, wrap it in the configured **Text Qualifier** (default `"`).
- For files with a non-comma delimiter (pipe `|`, semicolon `;`, etc.), change the **Field Delimiter** option before opening.
- Saving the index (**Load / Save Full Index**) significantly speeds up reopening large files.

## Columns

Columns are determined dynamically from the file content. If **First Row** is enabled, column names come from the header row; otherwise columns are labeled **Column 0**, **Column 1**, and so on. Log Lion auto-detects which column contains the timestamp and which contains the primary message text.

## Properties

Properties are read-only metadata shown in the **Properties** pane.

| Property | Description |
| --- | --- |
| Created | File creation date and time. |
| Modified | Date and time the file was last modified. |
| Accessed | Date and time the file was last accessed. |
| File Size | Size of the file in bytes. |
| Index Size | Number of indexed rows. |
| Average Line Size | Average number of bytes per row. |
| Text Encoding | Detected character encoding. |
| Time Field | Name and column number of the auto-detected timestamp column. |
| Message Field | Name and column number of the auto-detected primary message column. |

## Options

Options are configurable settings found under **File > Source Settings > Comma-separated values**.

| Option | Values | Description |
| --- | --- | --- |
| First Row | Yes, No | When enabled, the first row of the file is treated as a header and used as column labels. |
| Text Qualifier | Character (default `"`) | Character used to enclose field values that contain the delimiter or embedded line breaks. |
| Field Delimiter | Character (default `,`) | Character that separates adjacent fields within a row. |
| Load / Save Full Index | Yes, No | When enabled, the row offset index is persisted to disk for fast reopening. |
| Free Index Reserve >(KB) | Numeric | Minimum file-growth threshold (in KB) that triggers a partial index rebuild on refresh. |
| Line Endings | Auto, Windows (CR LF), Unix (LF), Macintosh (CR) | Controls how row boundaries are detected. |
| File cache limit (MB) | Numeric | Maximum amount of the file to memory-map at once. |

---

[All documentation](README.md)
