# Exporting and Copying

Getting entries out of Log Lion — into a ticket, a mail, a spreadsheet, or another tool. Two routes: the clipboard for a handful of entries, a file for the rest.

## Copying

Select some entries and copy. The format you choose decides what the other end receives.

| Command | Key | Gives you |
| --- | --- | --- |
| **Copy** | Ctrl+C | The default format. |
| **Copy As ▸ Simple Text** | Alt+C | Plain text, no markup. For a terminal, a code block, or a chat window. |
| **Copy As ▸ Csv Text** | Ctrl+Shift+C | Comma separated, one row per entry. Pastes into a spreadsheet as cells. |
| **Copy As ▸ Rich Text** | Alt+Insert | RTF **with the coloring you are looking at**. For Word, Outlook and most mail clients. |
| **Copy As ▸ Html** | Ctrl+Insert | HTML, also colored. For a wiki, an issue tracker, or a web mail client. |
| **Copy As ▸ Xml** | Ctrl+Shift+Insert | One element per entry, columns as fields. For feeding another tool. |
| **Copy Column** |  | Just one column's value from each selected entry. |

**Rich Text and HTML keep the colors.** That is the one worth remembering. Paste a dozen entries into a ticket as HTML and whoever reads it sees the same [syntax highlighting and highlight rules](coloring.md) you were looking at, which carries a surprising amount of the explanation with it. Pasting the same lines as plain text throws that away.

**Copy Column** is the quiet time-saver: select a few hundred entries, copy just the request id column, and you have a list to paste into a query.

## Exporting to a file

**File ▸ Export Selection** writes the selected entries — or everything in the [viewable range](limiting-the-range.md) if you press **Ctrl+A** first — to a file. The format is chosen by the file type in the save dialog:

| Format | Good for |
| --- | --- |
| **HTML** | A readable, colored record. The best choice for attaching to an incident write-up. |
| **RTF** | The same, for word processors. |
| **CSV** | Spreadsheets, and anything expecting a table. |
| **Plain text** | A smaller log file that other tools can read. |
| **XML** | Structured hand-off to another system. |
| **JSON** / **JSON Lines** | Scripts and log pipelines. One object per entry. |
| **BSON** | A compact binary form of the same. |
| **logfmt** | `key=value` pairs, as several logging libraries emit. |
| **Markdown** | A table for a pull request, a wiki, or a README. |

Exports respect what you are looking at: the columns you have shown or hidden, their order, and the range. Log Lion can reopen most of these formats, so exporting a slice as JSON Lines or CSV is a practical way to hand a colleague the interesting 500 entries out of a four-gigabyte file.

## Getting results out of the tools

The [analysis tools](analyzing.md) and [scripts](scripts.md) produce their own tables, and each of those windows has its own **Export** button that writes CSV, plus **Ctrl+C** to copy the selected rows tab-separated. Those export the computed results — the counts and the summaries — not the underlying entries.

## Just the file path

**Tools ▸ Copy File Path** puts the open log's full path on the clipboard, for when the answer to someone's question is where the file is rather than what is in it.

## Related

- [Coloring](coloring.md) — what HTML and RTF carry across.
- [Filtering](filtering.md) — filter first, **Ctrl+A**, then export, to write out exactly the entries that matter.
- [Scripts](scripts.md) — for an export shaped differently from anything here.

---

[All documentation](README.md)
