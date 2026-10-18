# Log Types

## Log Sources

Log Lion supports built-in operating system event and debugging sources. These sources are accessible via OS APIs and do not require a file to be opened.

| Source Type | Description |
| --- | --- |
| [Windows Event Log](log-types-windows-event-log.md) | Live connection to the built-in Windows Event Log (Application, Security, System, and other channels). Records system events, application issues, and security activity. Available on the local machine without opening a file. |
| [Windows Debug Output](log-types-debug-output.md) | Real-time capture of `OutputDebugString()` calls from any running process. Each captured message is timestamped and attributed to its originating process. |
| [Windows Defender Firewall Log](log-types-windows-firewall-log.md) | Automatically detected when Windows Defender Firewall logging is enabled. One entry is listed per firewall profile (Domain, Private, Public) with logging turned on. Opens the underlying log file using the W3C Extended Log Format parser, escalating to administrator privileges only when the source is opened. |

## Extensions

The sources listed on this page are built in. [Extensions](extensions.md) add more: a third party can supply a log format or a live data source that Log Lion does not handle on its own, and the sources it provides then appear alongside the built-in ones — in the **File ▸ Open** dialog for a file type, or among the named sources for a connection.

Because an extension is a program rather than a settings file, Log Lion asks before loading a new one. See [Extensions](extensions.md) for how to install one, what the prompt is telling you, and how to review or block one afterwards.

## File Types

Log Lion supports a variety of standard file types as log sources. When Log Lion first runs you are given the option of associating Log Lion for these file extensions with the OS shell. Options for each of these file types are located under **File > Source Settings**.

| File Type | Extension | Description |
| --- | --- | --- |
| [Plain Text](log-types-plain-text.md) | .txt | Unstructured text files with one log entry per line. Supports ASCII, UTF-8, and UTF-16 LE encodings. Windows, Unix, and Macintosh line endings are all recognized. |
| [JSON / JSON Lines](log-types-json.md) | .json, .jsonl | Structured logging in newline-delimited JSON (JSON Lines) or single-document JSON format. Columns are discovered automatically from the JSON schema. Widely used by OpenTelemetry, Elasticsearch, Serilog (CLEF), and cloud-native logging pipelines. |
| [HTTP Archive](log-types-har.md) | .har | Browser DevTools network capture format. Each entry is one HTTP request/response pair with timing data. Exported from Chrome, Firefox, Edge, Fiddler, and Charles. |
| [BSON](log-types-bson.md) | .bson | MongoDB's binary document format, one document after another. Columns are discovered from the documents, and values keep their real types — dates, numbers, and binary stay distinct. Written by mongodump, change stream and oplog captures, and services that store events in MongoDB's encoding. |
| [Comma-Separated Values](log-types-csv.md) | .csv | Tabular text format as defined by RFC 4180. Columns come from a configurable header row. Common for database exports, test results, and cloud log downloads. |
| [Tab-Separated Values](log-types-tsv.md) | .tsv | Tabular text format using tab characters as delimiters, per the IANA `text/tab-separated-values` MIME type. Fields typically need no quoting, making the format simple to produce and parse. |
| [W3C Extended Log Format](log-types-w3c.md) | .log | Web server access log format standardized by the W3C. Used by Microsoft IIS, Windows Defender Firewall, and Azure CDN. The `#Fields` directive defines the column schema. |
| [MSI Log](log-types-msi.md) | .log | Windows Installer verbose log produced by `msiexec /l*v install.log`. Captures every action, condition evaluation, and error from an MSI installation. |
| [Windows Legacy Event Log](log-types-legacy-event-log.md) | .evt | Binary event log file format used by Windows 2000, XP, and Server 2003. Log Lion can open and partially repair corrupt or wrapped circular-buffer files. |
| Windows Event Log | .evtx | XML-based event log file format introduced with Windows Vista and used by all modern Windows versions. |
| [Windows Event Trace Log](log-types-etl.md) | .etl | Binary ETW (Event Tracing for Windows) capture produced by `logman`, `xperf`, `wpr`, or Performance Monitor. Used for performance profiling and detailed kernel or application tracing. |
| [SQL Database](log-types-database.md) | .lldb | A connection profile describing a log held in a database table rather than a file. Connects over ODBC, so SQL Server, PostgreSQL, MySQL, Oracle, SQLite and others are all supported. A Lua script can shape the query and the schema for an unusual layout. |
| [Binary Data File](log-types-binary.md) | any | Opens any file in a hex-dump view showing 32 bytes per row as hexadecimal values alongside the printable ASCII representation. Supports 64-bit file offsets for files larger than 4 GB. |
| [Custom Parsed Files](log-types-custom-parsed.md) | .log (configurable) | Profile-driven parser for custom or proprietary text log formats. Use the **Import Text File Wizard** to define a sequence of field parsers (date/time, delimited, enclosure, regex, and more) that describe your log entry layout. Log Lion saves the profile and reuses it automatically. |

## Import Text File Wizard

Import custom log file formats by defining flexible rules to parse fields. A wide variety of text formats can be interpreted and made available in Log Lion by defining a set of rules to parse the field data of custom text log entries. Log Lion has a set of heuristics to attempt to detect common formats such as date/time and delimited fields and automatically add those parsing rules for you.

### Supported Parsing Methods

The following parsing methods are supported.

| Parsing Method | Parameters | Output |
| --- | --- | --- |
| **Date/Time**<br>Parses date time fields via pattern tokens similar to the POSIX strptime() function. | **Format**<br>Specifies the parsing format of the field profile. Examples:<br>2006-06-13 12:22:01 "%Y-%m-%d %H:%M:%S"<br>06/13/06 12:22:01 pm "%m/%d/%y %I:%M:%S %p"<br>12:22:01.987 "%H:%M:%S:%OS"<br>Fri Jan 24 08:33:14 2003 "%a %b %d %H:%M:%S %Y" | **Reformat**<br>Specifies how the parsed input will be reformatted for display. Leave empty for no transformations or specify formatting tokens similar to the POSIX strftime() function.<br>Example: %Y-%m-%d %H:%M:%OS |
| **Delimited**<br>Parses a field separated by a delimiter character such as a comma, tab, semi-colon, pipe, or end of line. | **Delimiter**<br>The character to terminate field parsing by. Use \n for newlines and \t for tabs. **Text Qualifier**<br>The character to qualify literal text which is allowed to embed the field delimiter. Qualifier is optional and is escaped within a string sequence by double qualifiers such as "" | |
| **Enclosure**<br>Parses a field enclosed in start and ending tags such as parentheses. | **Start Tag**<br>The sequence of characters that opens the enclosure. The starting tag will be omitted from the output. **End Tag**<br>The sequence of characters that closes the enclosure. The ending tag will be omitted from the output. | |
| **Fixed Width**<br>Parses exactly a fixed length of characters. | **Width**<br>The number of characters to extract for the field. | |
| **Integer**<br>Parses a numerical integer by the specified base. | **Base**<br>The radix of the integer being parsed such as 10 for decimal or 16 for hexadecimal. Set to 0 to autodetect based on common prefix notations (0-octal, 0x-hex) | |
| **Real**<br>Parses a numerical floating point value with a decimal point. | | |
| **Regular Expression**<br>ECMAScript regular expression patterns used to match character combinations. | **Format**<br>The matching pattern where the following characters have special meaning **.^$*+?{}\|\:;=!-/%&~><** | **Reformat**<br>A string literal with replacement symbols such as $1 for the first group. |

---

[All documentation](README.md)
