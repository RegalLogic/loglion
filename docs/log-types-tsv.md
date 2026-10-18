# TSV — Tab-Separated Values

Tab-Separated Values (TSV) is a plain-text tabular format where fields within each row are delimited by tab characters (`\t`), as specified by the IANA registered MIME type `text/tab-separated-values`. Because tab characters rarely appear inside data values, TSV files typically require no quoting or escaping, making them simpler to produce and parse than CSV. TSV is commonly used for log exports from database tools, test frameworks, and systems that avoid comma ambiguity in numeric or localized data.

## Tips

- If the first row contains column names, enable the **First Row** option so Log Lion uses those names as column labels.
- Unlike CSV, TSV does not require a **Text Qualifier** because the tab delimiter almost never appears inside field values.
- Log Lion auto-detects a timestamp column for timeline analysis.
- Saving the index (**Load / Save Full Index**) significantly speeds up reopening large files.

## Columns

Columns are determined dynamically from the file. If **First Row** is enabled, column names come from the header row; otherwise columns are labeled **Column 0**, **Column 1**, and so on.

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

## Options

Options are configurable settings found under **File > Source Settings > Tab-separated values**.

| Option | Values | Description |
| --- | --- | --- |
| First Row | Yes, No | When enabled, the first row of the file is treated as a header and used as column labels. |
| Load / Save Full Index | Yes, No | When enabled, the row offset index is persisted to disk for fast reopening. |
| Free Index Reserve >(KB) | Numeric | Minimum file-growth threshold (in KB) that triggers a partial index rebuild on refresh. |
| Line Endings | Auto, Windows (CR LF), Unix (LF), Macintosh (CR) | Controls how row boundaries are detected. |

---

[All documentation](README.md)
