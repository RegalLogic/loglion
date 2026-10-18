# Plain Text

Plain Text files are the simplest and most universal log format. Any application that writes unstructured text to a file — one log entry per line — produces a plain text log. Common producers include scripting languages, legacy server software, embedded systems, and any tool that pipes output to a file.

## Supported Encodings

Log Lion detects the file encoding automatically. The following encodings are supported:

- **ASCII** — 7-bit characters. No byte order mark (BOM) required.
- **UTF-8** — Unicode encoded as variable-width bytes. BOM (`EF BB BF`) is detected but optional.
- **UTF-16 LE** — Unicode encoded as 16-bit little-endian words. BOM (`FF FE`) is required for detection.

Line endings for Windows (CR+LF), Unix / Linux (LF), and classic Macintosh (CR) are all supported. The **Line Endings** option can force a specific line ending style if auto-detection produces incorrect results.

## Tips

- Use **Filters** with regular expressions to isolate entries containing keywords, error codes, or timestamps.
- If entries span multiple lines (e.g., Java stack traces), consider the **Import Text File Wizard** to define a multi-line entry rule.
- The **Tab Size** option controls how tab characters are expanded for display.
- Saving the index (**Load / Save Full Index**) significantly speeds up reopening large files.

## Columns

| Column | Description |
| --- | --- |
| **Text** | The raw text content of the line. |

## Properties

Properties are read-only metadata shown in the **Properties** pane.

| Property | Description |
| --- | --- |
| Created | File creation date and time. |
| Modified | Date and time the file was last modified. |
| Accessed | Date and time the file was last accessed. |
| File Size | Size of the file in bytes. |
| Index Size | Number of indexed entries (lines). |
| Average Line Size | Average number of bytes per line, calculated from the index. |
| Text Encoding | Detected character encoding: ASCII, UTF-8, or UTF-16 LE. |

## Options

Options are configurable settings found under **File > Source Settings > Plain Text**.

| Option | Values | Description |
| --- | --- | --- |
| Load / Save Full Index | Yes, No | When enabled, the line offset index is cached to disk alongside the file. Subsequent opens of large files complete instantly. |
| Tab Size | Numeric (spaces) | Number of spaces each tab character is expanded to for display purposes. |
| Line Endings | Auto, Windows (CR LF), Unix (LF), Macintosh (CR) | Controls how line boundaries are detected. Auto is recommended for most files. |
| File cache limit (MB) | Numeric | Maximum amount of the file to memory-map at once. Increase for faster random access on large files when RAM permits. |

---

[All documentation](README.md)
