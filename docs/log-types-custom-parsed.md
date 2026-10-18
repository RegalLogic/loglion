# Custom Parsed Files

The **Import Text File Wizard** lets you define how Log Lion should parse any custom or proprietary text log format. You describe the layout of a log entry field by field — specifying a **parser** for each field in sequence. Log Lion stores this description as a profile and reuses it every time you open a matching file.

To create or edit a profile, choose **File > Import Text File Wizard**. Log Lion samples lines from your file and uses heuristics to auto-detect common patterns (timestamps, delimited fields, enclosures) and pre-populate the profile for you. You then review, adjust, and name the result. For a step-by-step walkthrough, see the [Import Text Log Wizard](log-types-text-log-wizard.md) help page.

## Profile Settings

The following settings apply to the profile as a whole and control how the file is pre-processed before field parsing begins.

| Setting | Description |
| --- | --- |
| **Name** | Display name for this log source profile, shown in the Log Source selector. |
| **File Extension** | File extension to associate with this profile (e.g., `log`). Files with this extension will be offered this profile automatically. |
| **Line Endings** | How line boundaries are detected: Auto, Windows (CR+LF), Unix (LF), or Macintosh (CR). |
| **Ignore First N Lines** | Number of lines at the start of the file to skip (e.g., skip a file-level header block before entries begin). |
| **First Line is Header** | When enabled, the first non-skipped line is treated as a column header row and its fields become column labels. |
| **Header Delimiter** | Delimiter used to split the header row into column names when **First Line is Header** is enabled. |
| **Line Comment Prefix** | Lines beginning with this string are treated as comments and skipped (e.g., `#` or `//`). |
| **Continue Lines Without Digits** | When enabled, a line that contains no digit characters is treated as a continuation of the previous entry rather than a new entry. Useful for log formats where stack traces or multi-line messages follow the initial entry line. |

## Field Parsers

Each field in the profile uses one of the parsers below. Parsers are applied left to right in the order they appear in the profile. When a parser finishes consuming its portion of the input, the next parser starts immediately from where the previous one stopped — there is no automatic skip or whitespace advance between fields. This means every character in the line must be accounted for, including separators and structural punctuation. A field can be **excluded** to consume structural characters (separators, brackets, etc.) without displaying them as a column.

### Date/Time

Parses a date and/or time value using a POSIX `strptime`-style format string. The parsed value populates Log Lion’s time column and enables timeline features (histogram, range navigation, time-based filtering). The first Date/Time field in the profile automatically becomes the time column.

**When to use:** Any human-readable timestamp — ISO 8601, RFC 2822, US date formats, 24-hour time, 12-hour time with AM/PM, or timestamps with fractional seconds.

| Parameter | Description |
| --- | --- |
| **Format** | The pattern used to parse the timestamp. See the format tokens table below. |
| **Reformat** | Optional `strftime`-style format string that controls how the parsed timestamp is displayed. Leave empty to display the timestamp exactly as it appears in the file. Example: `%Y-%m-%d %H:%M:%OS` normalizes any date format to ISO 8601. |

**Format tokens:**

| Token | Matches | Example |
| --- | --- | --- |
| `%Y` | 4-digit year | `2026` |
| `%y` | 2-digit year (00–68 = 2000s, 69–99 = 1900s) | `26` |
| `%m` | Month as decimal 01–12 | `05` |
| `%d` | Day of month 01–31 | `31` |
| `%H` | Hour 00–23 (24-hour clock) | `10` |
| `%I` | Hour 01–12 (12-hour clock) | `10` |
| `%M` | Minutes 00–59 | `00` |
| `%S` | Seconds 00–59 (whole number) | `01` |
| `%OS` | Seconds with optional fractional part | `01.987` |
| `%p` | AM or PM indicator | `PM` |
| `%z` | Timezone offset (ISO 8601 form, or `Z` for UTC) | `+0500`, `Z` |
| `%a` | Abbreviated weekday name | `Mon` |
| `%b` | Abbreviated month name | `Jan` |
| `%F` | Equivalent to `%Y-%m-%d` | `2026-05-31` |
| `%T` | Equivalent to `%H:%M:%S` | `10:00:01` |

**Format string examples:**

- `2026-05-31T10:00:01Z` → Format: `%Y-%m-%dT%H:%M:%SZ`
- `06/13/06 12:22:01 pm` → Format: `%m/%d/%y %I:%M:%S %p`
- `Fri Jan 24 08:33:14 2003` → Format: `%a %b %d %H:%M:%S %Y`
- `12:22:01.987` → Format: `%H:%M:%OS`

### Unix Timestamp

Parses a numeric Unix epoch timestamp (integer count of time units since January 1, 1970 00:00:00 UTC) and converts it to a human-readable date/time for display. Like Date/Time, the first Unix Timestamp field becomes the time column. The parsed value is displayed in ISO 8601 format (UTC) with subsecond precision where applicable.

**When to use:** Logs produced by systems that write epoch integers rather than formatted dates. Common in Linux system logs, microservice backends, and many application logging frameworks (e.g., Logback, Zap, Bunyan).

| Parameter | Description |
| --- | --- |
| **Precision** | How many digits the timestamp contains and therefore how to interpret the value:<br>**Seconds (s)** — 10-digit timestamp (e.g., `1705012800`)<br>**Milliseconds (ms)** — 13-digit timestamp (e.g., `1705012800000`)<br>**Microseconds (us)** — 16-digit timestamp (e.g., `1705012800000000`)<br>**Nanoseconds (ns)** — 19-digit timestamp (e.g., `1705012800000000000`)<br>Log Lion auto-detects the precision from the number of digits when creating a new profile. |

### Delimited

Parses a field that ends when a specific delimiter character is encountered. The delimiter is consumed but not included in the field value. This is the most common parser for space-separated, comma-separated, tab-separated, pipe-separated, and similar formats.

**When to use:** Any field that ends at a consistent single character: space, comma, pipe, semicolon, colon, or tab. Also use it with Delimiter set to `\n` as a simple “rest of line” reader when no text qualifiers are present.

| Parameter | Description |
| --- | --- |
| **Delimiter** | The character that marks the end of the field. Common choices: space, `,` (comma), `\|` (pipe), `;` (semicolon), `:` (colon), `\t` (tab), `\n` (end of line). |
| **Text Qualifier** | Optional character (e.g., `"` or `'`) that encloses field values permitted to contain the delimiter character. A doubled qualifier inside the value (e.g., `""`) represents a literal qualifier character. Leave blank if the format does not use quoting. |

### Until End of Line

Parses from the current position to the end of the line. Use this as the **last** field parser in a profile to capture any remaining text on the line as a single field (typically the message or description column). When the profile has **Continue Lines Without Digits** enabled, subsequent lines that contain no digit character are appended to this field, which is useful for multi-line stack traces or exception messages.

**When to use:** Always place this as the final parser in the list to capture the remainder of the line.

| Parameter | Description |
| --- | --- |
| **Text Qualifier** | Optional character that may enclose the value. Leave blank if the format does not use quoting. |

### Enclosure

Parses a field delimited by explicit start and end tag strings rather than a single character. Both tags are consumed and excluded from the field value. Tags may be multi-character strings.

**When to use:** Fields wrapped in brackets `[...]`, parentheses `(...)`, angle brackets `<...>`, XML-like tags `<msg>...</msg>`, or any other paired multi-character delimiter.

| Parameter | Description |
| --- | --- |
| **Start Tag** | The character sequence that opens the field (e.g., `[`, `(`, `<msg>`). Consumed and excluded from the field value. |
| **End Tag** | The character sequence that closes the field (e.g., `]`, `)`, `</msg>`). Consumed and excluded from the field value. |

### Fixed Width

Parses exactly a specified number of characters regardless of content. Use this for fixed-column formats where every field occupies the same width in every entry, common in mainframe, legacy syslog, and fixed-column database export formats.

| Parameter | Description |
| --- | --- |
| **Width** | The exact number of characters to consume. Valid range: 2 to 500. |

### Integer

Parses a numeric integer value, consuming digits (and an optional leading sign) until a non-digit character is encountered.

**When to use:** Numeric ID fields, error codes, process IDs, port numbers, or any field that is an integer in decimal, hexadecimal, or octal.

| Parameter | Description |
| --- | --- |
| **Base** | The numeric base (radix) of the integer:<br>**0** — Auto-detect: values starting with `0x` or `0X` are parsed as hexadecimal; values starting with `0` are parsed as octal; all others as decimal.<br>**10** — Decimal.<br>**16** — Hexadecimal.<br>Any other standard radix (2–36) is also accepted. |

### Real

Parses a floating-point number (e.g., `3.14`, `-1.5e10`). Consumes the number including an optional sign, decimal point, and exponent. No parameters are required.

**When to use:** Floating-point measurements such as response times, cache hit ratios, CPU percentages, and similar numeric data.

### Regular Expression

Parses a field by matching an ECMAScript regular expression pattern (implemented via Boost.Regex). This is the most flexible parser and can capture any sub-structure that a regular expression can describe. Use capturing groups `(...)` to designate what to extract; the Reformat parameter controls which groups appear in the output.

**When to use:** Fields with variable structure, optional components, or formats that cannot be expressed cleanly with simpler parsers. Also useful for stripping prefixes, suffixes, or surrounding punctuation in a single step.

**Note:** The Regular Expression parser searches forward in the input for the first match rather than anchoring at the current position, so it may skip leading characters if the pattern does not match at the start. Anchor your pattern with `^` or structure it to begin matching at the expected position.

| Parameter | Description |
| --- | --- |
| **Format** | The ECMAScript regular expression pattern to match. The following characters have special meaning and must be escaped with `\` when used literally: `. ^ $ * + ? { } \| \ ( ) [ ]`<br>Use capturing groups `( )` to mark the portions to extract. |
| **Reformat** | Optional replacement string that constructs the output value from the match result. Use `$1` for the first capture group, `$2` for the second, and so on. Leave empty to use the entire matched text as the field value. |

**Examples:**

| Pattern | Reformat | What it extracts |
| --- | --- | --- |
| `\w+` | (empty) | A single word (letters, digits, underscores) |
| `\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}` | (empty) | An IPv4 address |
| `uid=(\d+)` | `$1` | The numeric user ID from `uid=4829`, outputting `4829` |
| `"([^"]*)"` | `$1` | The content of a double-quoted string (without the quotes) |

## Field Output Settings

Each field has the following output settings that control how its value is presented as a column.

| Setting | Description |
| --- | --- |
| **Name** | Column label displayed in the log view. Certain names activate special features automatically:<br>**Message** — designates the field as the primary search column.<br>**thread, threadid, thread_id, service, level, pid, process, host, hostname, traceid, sessionid** — displayed in the navigation context panel.<br>**caller, source, filename, file, filepath** — treated as source file references and rendered as clickable links. |
| **Exclude** | When enabled, the field is consumed by its parser but not shown as a column. Use this to skip structural separator characters without cluttering the column view. |
| **Alignment** | Column text alignment: Left, Center, or Right. Right alignment is conventional for numeric fields such as Integer and Real. |
| **Reformat** | (Date/Time and Regular Expression only) Transforms the parsed value before display. For Date/Time, use `strftime` tokens. For Regular Expression, use `$1`, `$2` capture group references. |

## How Parsers Chain Together

Each parser begins exactly where the previous one ended. There is no automatic whitespace skipping between fields. You must account for every character in the line, including separators, spaces, and structural punctuation. The typical pattern for separator characters is to add a Delimited field with the separator as the delimiter, enable **Exclude**, and give it an arbitrary name like `Sep1`. The separator is consumed without producing a visible column.

If a parser fails to match the content at its expected position, Log Lion attempts error recovery by skipping forward to the next whitespace character and resuming from there. Any entry where parsing could not consume the entire line will show a **[BAD ENTRY]** prefix in the message column. Return to the Fields step in the wizard and review the parser definitions for that entry's structure.

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
| Nav Field | Name and column number of the auto-detected navigation context column. |

## Options

Options are configurable settings found under **File > Source Settings** for the selected profile.

| Option | Values | Description |
| --- | --- | --- |
| Load / Save Full Index | Yes, No | When enabled, the entry offset index is persisted to disk for fast reopening of large files. |
| File cache limit (MB) | Numeric | Maximum amount of the file to memory-map at once. Increase for faster random access when RAM permits. |
| Source Directories | Semicolon-separated paths | Directories Log Lion searches when resolving source code file names found in log entries. |
| Reverse Index by Time Ascending | Yes, No | When enabled, entries are reordered so the earliest timestamp appears first, regardless of physical order in the file. |

## Profile Storage

Profiles are saved as XML files in `%APPDATA%\Log Lion\Text Log Profiles\`. Each file corresponds to one log source name. You can copy profile files between machines, share them with colleagues, or inspect and edit them in any text editor. To load an existing profile, open the Import Text File Wizard and select the profile name from the Log Source drop-down on the first step.

---

[All documentation](README.md)
