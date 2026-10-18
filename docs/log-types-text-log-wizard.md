# Import Text Log Wizard

The Import Text Log Wizard lets you teach Log Lion how to parse any custom or proprietary text log format. You walk through four steps — selecting a file, setting global options, defining field parsers, and previewing results — and Log Lion saves your settings as a reusable profile. The next time you open a file with the same extension, Log Lion offers to apply your saved profile automatically.

## Step 1 — Select File

On the first page, identify the log file to import and name the profile that will describe it.

| Setting | Description |
| --- | --- |
| File | Click the browse button (**...**) to select the log file to import. Log Lion reads a sample from this file throughout the wizard to give you a live preview of your settings. |
| Log Source | A name for this profile. Log Lion populates the drop-down with previously saved profiles; selecting one loads all of its settings for editing. Type a new name to create a new profile. If left blank the profile is saved under the name `temp_import_wizard_profile`. |

Before advancing, Log Lion validates that the file exists, can be opened, and contains text data. Possible error conditions:

- **Can't open file** — The file path does not exist or the application lacks read permission.
- **Can't access file** — The file exists but cannot be read (locked by another process or insufficient permissions).
- **Binary data not supported** — The file is a binary file, not a text log. Select a plain text file.
- **Unsupported encoding** — The file uses a Unicode encoding (UTF-16, UTF-32) that is not currently supported. Log Lion supports ASCII and UTF-8 encoded files.

## Step 2 — Options

The Options step sets global rules that apply before any field parsing begins. A preview pane on the left shows a raw sample of the file content so you can verify how these settings affect what Log Lion sees.

| Option | Values | Description |
| --- | --- | --- |
| Lines to sample | 0–500 | How many lines Log Lion reads from the file when displaying previews in this wizard. Does not affect how many lines are read when the file is opened normally in the log view. |
| Line Endings | Windows (CRLF), Unix (LF), Macintosh (CR) | How line boundaries are detected. Log Lion auto-detects the correct value from your file; change it only if auto-detection produces wrong results. |
| Ignore first N lines | 0–100 | Skip this many lines at the very beginning of the file before parsing starts. Use this for files that open with a version header, preamble, or other non-entry content. |
| Ignore lines starting with | `#`, `'`, `//`, `REM`, `--`, `!`, or any custom prefix | Any line whose first characters match this prefix is treated as a comment and skipped entirely. You can type any custom prefix. Leave blank if the file has no comment lines. |
| Is first line header? | True / False | When True, Log Lion reads the first non-skipped, non-comment line as column names rather than as a log entry. Those names appear as the initial field names in the next step. |
| Field delimiter | comma, pipe, semicolon, colon, tab, or custom | Used only when **Is first line header?** is True. Specifies how the header line is split into individual field names. Log Lion auto-detects this from the header content if left blank. |

## Step 3 — Fields

The Fields step is where you define how each piece of data in a log entry is extracted. The left panel shows a raw sample of your file with comment and ignored lines filtered out. The upper-right panel lists the fields in the order they are parsed, from left to right across each log line. The lower-right panel shows the configurable properties for the currently selected field.

### The Field List

Log Lion's auto-detection heuristics analyze the first three sample log entries and try to pre-populate the field list with matching parsers. Review the suggestions and adjust them as needed.

| Button | Action |
| --- | --- |
| Add (**+**) | Adds a new field at the bottom of the list with default Delimited settings. |
| Delete (**X**) | Removes the currently selected field permanently. |
| Up arrow | Moves the selected field one position earlier in the parsing order (closer to the start of the line). |
| Down arrow | Moves the selected field one position later in the parsing order (closer to the end of the line). |
| Rename (pencil) | Renames the selected field. The name becomes the column header in the log view. Certain names activate special features — see [Field Output Settings](log-types-custom-parsed.md) in the Custom Parsed Files reference. |

### Field Properties — Parsing

Selecting a field in the list populates the lower property grid. The **Method** property controls which other properties are visible; unrelated properties are hidden automatically. See the [Custom Parsed Files](log-types-custom-parsed.md) page for a complete description of all nine methods and their parameters.

| Property | Description |
| --- | --- |
| Method | The algorithm used to extract this field from the log line. Choices: Date/Time, Delimited, Enclosure, Fixed Width, Integer, Real, Regular Expression, Unix Timestamp, Until End of Line. |
| Format | (Date/Time and Regular Expression) The parsing pattern. |
| Width | (Fixed Width) Number of characters to read, 2–500. |
| Base | (Integer) The numeric radix: 0 = auto, 10 = decimal, 16 = hex. |
| Delimiter | (Delimited) The character that ends this field. |
| Text Qualifier | (Delimited) Optional quoting character; a doubled qualifier inside the value represents one literal qualifier. |
| Start Tag | (Enclosure) The string that opens the enclosed value; consumed and excluded from output. |
| End Tag | (Enclosure) The string that closes the enclosed value; consumed and excluded from output. |
| Precision | (Unix Timestamp) Seconds, Milliseconds, Microseconds, or Nanoseconds. |

### Field Properties — Output

| Property | Description |
| --- | --- |
| Alignment | Left, Center, or Right column alignment in the log view. Right is conventional for numeric fields. |
| Exclude | Set to **True** to consume this field during parsing without creating a visible column. Use this to skip separator characters (spaces, commas, structural punctuation) that you do not want displayed. |
| Reformat | (Date/Time and Regular Expression) Optional pattern that transforms the parsed value before display. For Date/Time, use `strftime` tokens. For Regular Expression, use `$1`, `$2` capture group references. |

## Step 4 — Preview

The Preview step parses your sample file using the profile you have built and shows the results as a table. Each row is a parsed log entry; each column corresponds to a non-excluded field. Review the table to confirm that values land in the correct columns before saving.

- If a column is missing or shows garbled text, click **Back** to return to the Fields step and adjust the affected parser.
- If there are fewer rows than expected, check the **Ignore first N lines** setting and confirm the comment prefix is correct.
- A **[BAD ENTRY]** prefix in a cell indicates that the parser definitions did not consume the entire log line — there was leftover text. Add more fields or end the list with an **Until End of Line** field.
- Clicking **Finish** saves the profile to `%APPDATA%\Log Lion\Text Log Profiles\`. Log Lion will offer this profile automatically when you open a file with the same extension in the future.

## Worked Example

This example walks through configuring Log Lion to parse the following log format:

```
2026-05-31T10:00:01Z INFO [auth-service] User login successful for uid=4829
2026-05-31T10:00:15Z WARN [payment-service] Database connection latency high (450ms)
2026-05-31T10:01:02Z ERROR [gateway] Upstream timeout on /api/v1/checkout - 504 Gateway Timeout
2026-05-31T10:02:45Z INFO [auth-service] Token refreshed for uid=4829
2026-05-31T10:05:12Z DEBUG [cache-manager] Cache hit ratio at 94.2%
```

Each entry follows the structure: `TIMESTAMP LEVEL [SERVICE] MESSAGE`

### Step 1 — Select File

Browse to your `.log` file and type `My App Log` in the Log Source box.

### Step 2 — Options

Leave all options at their defaults. Line Endings will be auto-detected. There are no comment lines and no header row, so **Is first line header?** stays False.

### Step 3 — Fields

Log Lion's auto-detection will likely recognize the ISO 8601 timestamp and possibly the space-delimited Level field. Review and complete the field list as follows. Fields 2 and 5 handle the space separators; name them anything (e.g. `Sep1`, `Sep2`) and set their **Exclude** property to **True** so they do not appear as columns.

| # | Name | Method | Key Settings | Why | Example Output |
| --- | --- | --- | --- | --- | --- |
| 1 | Timestamp | Date/Time | Format: `%Y-%m-%dT%H:%M:%SZ` | ISO 8601 with a literal `T` separator and `Z` UTC suffix. This field becomes the time column for the histogram and time-range navigation. | `2026-05-31T10:00:01Z` |
| 2 | Sep1 | Delimited | Delimiter: space<br>Exclude: True | Consumes the single space between the timestamp and the level word without creating a column. | (hidden) |
| 3 | Level | Delimited | Delimiter: space | The severity word (INFO, WARN, ERROR, DEBUG) runs until the next space. The name `Level` is a recognized nav-context column name. | `INFO` |
| 4 | Service | Enclosure | Start Tag: `[`<br>End Tag: `]` | The service name is wrapped in square brackets. Enclosure reads the content between `[` and `]` and excludes both brackets from the output. The name `Service` is a recognized nav-context column name. | `auth-service` |
| 5 | Sep2 | Delimited | Delimiter: space<br>Exclude: True | Consumes the space between the closing `]` and the start of the message. | (hidden) |
| 6 | Message | Until End of Line | (none) | The remainder of the line is free-form text. “Until End of Line” reads everything to the newline. Naming this field `Message` designates it as the primary search column. | `User login successful for uid=4829` |

Set **Alignment** to Center for Level and Left for Timestamp and Message.

### Step 4 — Preview

The preview table should show five rows with four visible columns: Timestamp, Level, Service, and Message. Verify the Timestamp column shows correctly parsed values. If the Service column is empty or contains bracket characters, return to Step 3 and confirm Start Tag is `[` and End Tag is `]`.

## Tips

- Always add a field to consume separator characters (spaces, commas, colons) that appear between data fields, even if you don't want them as columns. Set **Exclude** to **True** for those fields. Forgetting to consume a separator causes every subsequent field to misparse.
- The first **Date/Time** or **Unix Timestamp** field in the list automatically becomes Log Lion's time column, enabling the histogram, time-range navigation bar, and time-based filtering.
- Naming a field exactly `Message` (case-sensitive) designates it as the primary search and display column.
- The following field names are automatically recognized as navigation context columns, enabling navigation panel features: `thread`, `threadid`, `thread_id`, `service`, `level`, `pid`, `process`, `host`, `hostname`, `traceid`, `sessionid`, `userid`, `appname`, `category`.
- The following field names are recognized as source file references and rendered as clickable links: `caller`, `source`, `filename`, `file`, `filepath`.
- When you re-open the wizard and select an existing profile name from the drop-down, all previous settings are loaded for editing.
- Profiles are XML files stored in `%APPDATA%\Log Lion\Text Log Profiles\`. You can copy them between machines, share them with colleagues, or check them into version control.
- If a line has variable structure (e.g. optional fields), consider using **Regular Expression** for that section. See the [Custom Parsed Files](log-types-custom-parsed.md) page for examples.

---

[All documentation](README.md)
