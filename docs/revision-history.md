# Revision History

## Version 3.1.3

- NEW: Docker, the first of Log Lion's Enterprise extensions, is included with Log Lion. *File\Open Source\Docker* shows the logs of a compose project's containers, or the ones you pick, from this computer's Docker engine or a remote one, merged by time and followed live, with each container in its own color. JSON and logfmt lines open as fields. .docker connection files open like any log, and are where each service's lines can be decoded with JSON, logfmt or a pattern and their fields shown as columns. Enterprise extensions are built and signed by Regal Logic. Installing a newer Docker extension from *Tools\Extensions\Install* replaces the included one.
- NEW: *File\Source Settings* has the settings for each kind of log, which were under *Log Sources* in *View\Settings*. Pick the kind of log from the list at the top, and its settings get the full width of the dialog. Changes to several kinds are kept until you choose OK. *View\Settings* now has only Log Lion's own settings.
- NEW: A connection from an extension, such as *File\Open Source\Docker...*, now asks what to open before opening it: Docker lists the engine's compose projects and containers to choose from, along with how much history to read and whether to keep following. Your choices are remembered for next time, and Cancel leaves the log you have open alone. An extension says which of its sources are connections; one that isn't, like a generator of test data, opens at once, with its settings in *File\Source Settings*.
- NEW: The dialog a connection shows before it opens, such as *File\Open Source\Docker...*, has a Save As button that saves its choices, such as a Docker compose project, as a connection file (.docker for Docker). The dialog stays open, so you can still open the connection or cancel. Opening the file shows the same view again, and it is where the settings go that the connection's options don't have, such as how to decode each Docker service's lines. The file leaves out passwords and tokens.
- NEW: An extension can say, under its options in *File\Source Settings* and in the dialog a connection shows before it opens, what its options don't cover and where to find it. Docker, Splunk and Grafana MCP say what their connection files add.
- FIXED: A source from an extension was shown by its internal name, such as "Docker.docker", in the title bar, the recent lists and *Get Started*. It is now "Docker", and the title bar names what is shown, such as a compose project.
- FIXED: During the 45-day tryout, before a license is loaded, *Help\About* showed nothing under the license. It now shows that Log Lion is in its tryout period, when the tryout ends and how many days are left.
- NEW: Extensions digitally signed by Regal Logic are implicitly trusted: they load without asking whether to enable them, both at startup and when installed from *Tools\Extensions\Install*. Other extensions still ask. You can still block one of ours in *Tools\Extensions*, and its details say when an extension is implicitly trusted.
- NEW: *Tools\Extensions\Install* installs an extension from the .zip package it is delivered in, starting in your Downloads folder, so there is no need to unzip it into the extensions folder by hand. The package is checked first, and one that could place files outside the extension's own folder, or is damaged, is refused. Installing a newer version asks before replacing the old one, and an update to an extension that is in use is finished the next time Log Lion starts, even if a File Explorer window is open on one of its folders; when it cannot be, the banner says why. Afterwards the extension's guide (its README) is shown.
- NEW: Extensions that read from a service, such as Splunk or Grafana, can now run your filter on the server. The filter you apply is handed to the extension, which fetches the matching entries from the whole time range rather than Log Lion filtering only the entries already fetched. Such an extension can also keep its view up to date on a schedule (Auto Refresh turns on by itself when it opens) and open with a filter already applied.
- FIXED: With *View\Auto Refresh* on, a log whose refresh runs in the background lost its active filter after each refresh. The filter is now turned back on when the refresh finishes.
- NEW: *LogLion.exe --data-dir &lt;folder>* keeps the data folder (filters, profiles, scripts, extensions) in that folder and the settings in a separate sandbox registry key for that run, so extensions and settings can be tried without touching your own. The license is still read from the usual place.
- NEW: *Tools\Extensions* has *Remove*, which deletes an extension you installed, and *Read Me*, which shows the guide an extension came with. Extensions that are not loaded show the version their package declares, and the details show when the selected extension was installed, which tells apart two builds of the same version.
- FIXED: Closing *Help\About* with the X in its title bar, or with Esc, opened the Load License file browser instead of closing the dialog.
- FIXED: With *View\Auto Refresh* on, a log from an extension could now and then lose its selection and put its columns back to their default widths. Each refresh briefly emptied the extension's list of columns while the window could be reading it. A refresh now leaves the columns, entry types and source info alone unless they actually changed.
- NEW: An extension's options can offer common values, such as the usual Docker hosts or a few time spans, in a drop-down list in *File\Source Settings*. Pick one, or type any other value as before.
- FIXED: A source from an extension had no settings in *File\Source Settings* unless it was the source open, and the options saved for it were not applied when it opened, so a source opened by name, such as Docker, always opened with its defaults.
- FIXED: With a connection file open, such as a .docker file, *File\Source Settings* showed the first kind of log in the list instead of the settings of the extension that opened it, such as Docker.
- FIXED: With syntax highlighting on, a name=value pair in the log list showed only its name when the value was not a number: *order=A-10524* showed as *order*, and *level=info* as *level*. The preview showed the whole pair. The list now shows the value too, in its own color.

## Version 3.1.2 (2026-10-10)

- NEW: A welcome message in the banner at the top of the window introduces Log Lion 3.1 and links to *See what's new* on our website. Close it and it does not come back.
- FIXED: In JSON Lines, CSV and other logs whose first column is the time, every entry showed the same time (the file's creation time) and the timeline collapsed into a single bar. ISO 8601 times such as *2026-09-26T13:30:00.640Z* were recognized, but the detected format was then replaced by the display format, so no entry could be read with it.
- FIXED: Source Info for ETL and HAR logs ran the Created and Modified dates together with the property shown before them.
- FIXED: The color column showed its outline and coloring even when coloring was turned off.
- FIXED: The timeline's tooltip stayed on screen after the mouse left the timeline.
- FIXED: In the filter editor, typing a space inside a column name such as *Client IP Address* while the suggestion list was open inserted the suggestion and then the rest of the typed name, duplicating it. A space now keeps the list open while it can still continue a suggestion.
- FIXED: The timeline was labeled in UTC while the time column of JSON Lines, Logfmt, BSON, ETL, HAR, Windows Event Log, debug output and database logs showed local time (or the time zone set in the log's options), so the two disagreed by the time zone offset. The timeline axis, its tooltips, and double-click now use the same time as the column.
- FIXED: A hidden column shown again from the header menu or the Fields pane could come back as the first column, or out of order when several were hidden. It now returns to where it was, whatever order columns are shown again in.
- FIXED: *Color\None* was forgotten when the log was closed, so coloring came back the next time a log of that type was opened. The menu also never showed *None* as checked, and the column header kept its coloring highlight while coloring was off.
- FIXED: With syntax highlighting on, text found by *Edit\Find* showed as *~!login!~* instead of highlighted when it followed punctuation, as in */account/login*.
- FIXED: The column list that opens when typing *$* in the filter editor was too small for its text, cutting off the ends of longer column names, and the column icons were drawn on black squares. The list now sizes itself to its text and the screen's DPI, and the icons keep their transparency.
- FIXED: Editing a filter's expression while the filter was toggled off, then toggling it back on (F4), showed the results of the old expression. A new quick filter such as *$msg contains "Payment"* could appear to find nothing, or the wrong entries, until the filter was reapplied. Toggling now applies the expression as it is now, and only reuses earlier results when it is unchanged.
- FIXED: Comparing a column with a number in a filter, such as *$Duration > 100*, compared them as text, so *26* was greater than *100*. A column that holds a number is now compared as a number with <, <=, > and >=.
- CHANGED: A filter applied from *Filter\Settings* is now translated to a filter expression and shown in the Filter pane as the quick (ad hoc) filter, switching the pane to it if it was on a saved filter. You can read and extend what the dialog wrote, and toggling the filter (F4) works on it like any other quick filter.
- NEW: Microsoft Store edition: Log Lion now appears in Explorer's *Open with* list for log file types (.log, .txt, .csv, .json, .jsonl, .evtx, .etl, .har and others), and *loglion &lt;file>* opens a log from a command prompt. *File\File Type Associations* opens Windows' Default apps settings, where the Store edition's file types are chosen.

## Version 3.1.1 (2026-09-30)

- NEW: Initial release of version 3.1 which brings several new log source types including ETL, BSON, Logfmt, and SQL Databases as well as many enhancements and bug fixes across the application.
- NEW: Continued app UI modernizations.
- NEW: Getting Started dialog when Log Lion is launched.
- NEW: LUA scripting support for custom log processing.Log Lion does a table scan passing all entries to the script for processing.
- NEW: Extensions (*.llx). Third parties can now add support for a log format or a live data source that Log Lion does not handle on its own, using a documented C interface that needs no Log Lion source code and no particular compiler. Extensions install into a folder of their own under *%APPDATA%\Log Lion\Extensions* (or *%ProgramData%* for everyone on the machine), and the sources they provide behave like built-in ones throughout — filtering, Find, bookmarks, the timeline, statistics, and export. Because an extension is a program rather than a settings file, Log Lion asks before loading a new one, showing its publisher and fingerprint, and remembers the answer against the file's SHA-256 so an update is a fresh decision. Menu *Tools\Extensions* lists what is installed and what loaded, and lets a decision be changed; holding Shift at startup, or *--no-extensions*, starts without loading any. See the Extensions help topic.
- NEW: Windows Event Trace Logs (*.etl) parsing support. Decodes ETW (Event Tracing for Windows) captures from tools such as logman, xperf, wpr, and Performance Monitor using the Windows Trace Data Helper (TDH) APIs, showing Time, Provider, Event ID, Level, Task/Opcode, Process ID, Thread ID, Processor#, Keywords, and a decoded Message column. Events whose provider manifest isn't registered on this machine still display their header fields, with a placeholder noting the manifest is unavailable. The first open of a trace decodes and caches the index alongside the file for fast reopening.
- NEW: BSON (*.bson) log parsing support. Reads MongoDB's binary document format — one document after another, as written by mongodump, by change stream and oplog captures, and by services that store events in MongoDB's own encoding. Columns are discovered from the documents and nested documents are flattened into dotted columns such as *context.host*, with arrays presented as JSON, as indexed columns, or as a count. Because BSON carries real types, the time column, severity icons, and the timeline come from the data itself rather than from pattern matching, and Decimal128, ObjectId, UUID, and binary values are decoded rather than shown as raw bytes. Every document declares its own length, so jumping to the last row of a large file is as fast as jumping to the first, and the index is cached alongside the file for immediate reopening. A file damaged by a truncated write or an overwritten region reports the damage and resumes at the next genuine document instead of stopping.
- NEW: BSON export.
- NEW: Logfmt formatted log source support with *.logfmt file extensions. Logfmt is a structured logging format that allows for easy parsing and is human-friendly, containing name=value pairs.
- NEW: SQL Database log source for reading logs stored in a database table rather than a file. Connects over ODBC using a *.lldb connection profile, so SQL Server, PostgreSQL, MySQL, Oracle, SQLite, and any other database with an installed ODBC driver can be read. Profiles open like any other log through File/Open, the recent file list, drag and drop, and the command line.
- NEW: Windows Defender Firewall log support. Log Lion checks each firewall profile's (Domain, Private, Public) logging settings in the registry and automatically lists a named source for every profile that has logging enabled and an existing log file, so there's no need to hunt down the file path yourself. Built on the existing W3C Extended Log Format parser.
- ADDED: Extension SDK, with a fully documented C header, two complete sample extensions (one generating rows in memory, one reading BSON files), and *llxprobe*, a command-line tool that loads an extension and checks it against the interface contract before it is installed.
- ADDED: Logfmt as a field parser option to the Import Text Log Wizard.
- ADDED: Context menu for Source Info pane with select all, copy, and refresh options.
- ADDED: Menu *Tools\Open Data Folder*, which opens the folder holding plugins, text log profiles, filters, and the license file. Saves having to type the location from the documentation.
- ADDED: "Capture.Debug Output" log source options under View/Settings/DBGOUT to adjust time zone offsets and OS Namespace. Use Namespace=Global for Windows Services (requires administrative privileges).
- ADDED: Content-based file type detection for Plain Text, CSV, TSV, JSON, JSON Lines, and Regular Expression Parsed log sources, extending auto detection to more of the ambiguous file extensions.
- CHANGED: Improved loading and highlighting of timeline pane.
- CHANGED: Color keyword mappings to include new color scheme for severity levels.
- CHANGED: About Box dialog for better clarity.
- FIXED: Date/time values carrying a UTC offset, such as 2026-01-15T10:30:00-08:00, were not recognized as timestamps. The %z format token now accepts the +HHMM, +HH:MM, and Z forms and adjusts the parsed time to UTC, which affects Logfmt and HTTP Archive logs in particular.
- FIXED: Right and center justified column text was drawn hard against the column edge with no padding, so a narrow column ran visually into its neighbor.
- FIXED: Crash when opening a file whose name is shorter than one of the registered file extensions, such as opening "a.txt" by a relative path.
- FIXED: HTTP Archive (.har) file type detection ignored a confident content match, so it was never auto-selected without an ambiguous source dialog.
- FIXED: Opening a general text file with a .log extension could take an extremely long time (appearing to hang) while auto detection tried to match it against a field parser profile with an Enclosure field, such as Capture Debug Output (dbgview).
- FIXED: Tools\Unmatched Pairs and Tools\Profiler matched an end event to the oldest open begin for the same identifier. Matching now pairs with the newest open begin, which fixes nested and recursive scope duration calculations.
- FIXED: A helper move constructor used by pair matching moved from uninitialized members instead of the source object, which could corrupt matching keys under move operations.
- FIXED: Tools\Unmatched Pairs no longer drops end events that have no matching begin. These unmatched end entries are now included in the results.
- FIXED: Short TSV rows with missing fields could cause the indexer to consume data from the following row. A stray *true;* statement was corrected to *return true;*, and a regression test now covers this case.
- FIXED: XML entity decoding relied on unspecified expression evaluation order and used *memcpy* on overlapping memory. The decode path now uses deterministic evaluation and overlap-safe copying.
- FIXED: Possible crash with IP address formatter buffer overflow due to insufficient size for IPv6 addresses.

## Version 3.0.40 (2025-05-25)

- NEW: App modernization visual enhancements such as updated icons, fonts, color schemes, and other UI elements.
- ADDED: Information banner for alerting user of license issues and other in app messages.
- ADDED: Import Unix Timestamp field parsers for seconds(10), milliseconds(13), microseconds(16), and nanoseconds(19) lengths with auto detection heuristics
- ADDED: Import EOL field parser for extracting remaining text until End of Line
- ADDED: Syntax highlighting performance improvements.

## Version 3.0.39 (2024-10-19)

- NEW: Auto detection of file types when opening files with .log or other ambiguous extensions.
- NEW: (Import Log file) File previews by searching file columns values against configured "Source directories" option, matching partial path segments, loading text file, and displaying it as syntax highlighted source code. Detects column by matching "file", "filename", "caller", "source", and other fields.
- ADDED: (JSON & Import Log file) Navigation context column detection for common field names such as "thread", "pid", "logger", "requestId" and others.
- ADDED: (JSON & Import Log file) Source Info panel properties for "Nav Field" and "Filename Field" which displays the columns detected and used for the navigation context and source code file name fields respectfully.
- ADDED: "Software" property of W3C Extended log files to title, which helps identify "Microsoft Windows Firewall" and other types.
- ADDED: Restart with escalation requests when receiving privilege not held errors for Windows Security Event Log and files.
- CHANGED: Menu *File\Open Source* to group sources by type.
- FIXED: W3C Extended Log File parsing error when encountering blank lines, as commonly found in the Windows Defender firewall w3c formatted log file.
- FIXED: Center and Right justification not honored with syntax highlighting.
- FIXED: Open File dialog displaying error message "pfirewall.log You don�t have permission to open this file. Contact the file owner or an administrator to obtain permission.".

## Version 3.0.38 (2024-08-21)

- CHANGED: Multiple monitor support to center splash screen on current monitor instead of last applicaiton frame position.
- FIXED: LogLion.chm help file missing from Windows Store installations.
- FIXED: Plugin for .ininlog binary files missing from Windows Store installation.

## Version 3.0.00 (2024)

- General Availability of major revision 3.0

---

[All documentation](README.md)
