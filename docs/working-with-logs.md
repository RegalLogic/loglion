# Working with a Log

Once a log is open, everything in this section works the same way whatever format it came from. A Windows Event Log, a CSV, a database table and a custom text format all become the same thing — a numbered list of **entries**, each with the same set of **columns** — and the features below all operate on that.

## The short version

| To… | Use | Key |
| --- | --- | --- |
| Understand what you are looking at | [Reading a Log](reading.md) |  |
| Find text anywhere in the log | [Finding](finding.md) | Ctrl+F |
| Jump to an entry, a time, or back where you were | [Navigating](navigating.md) | Ctrl+G |
| Mark entries you want to come back to | [Bookmarks](bookmarks.md) | Ctrl+F2 |
| Work on one part of a large log | [Limiting the Range](limiting-the-range.md) | Ctrl+B / Ctrl+E |
| Make patterns visible | [Coloring](coloring.md) |  |
| See when things happened | [The Timeline](timeline.md) |  |
| Get entries out of Log Lion | [Exporting and Copying](exporting.md) | Ctrl+C |

Narrowing down *which* entries you see is a separate subject with its own section — see [Filtering](filtering.md).

## Nothing you do changes the log

Log Lion opens log files **read only** and never writes to them. Filtering, coloring, bookmarking, setting a range and running tools all happen in Log Lion's own memory. This is deliberate: a log is frequently evidence, and a tool that might alter it is a tool you cannot use for the cases where it matters most.

The things Log Lion *does* save — your filters, bookmarks, colors and profiles — go beside the application's settings, never next to your log. See [Extensions](extensions.md) and [Scripts](scripts.md) for the folders involved.

## Everything is happening in the background

Log Lion is built to stay responsive on files far larger than will fit in memory. Opening a log reads an index, not the contents; entries are parsed as they come into view. Indexing, filtering and searching all run on background threads, so you can start reading the top of a four-gigabyte log while the rest is still being indexed.

The status bar tells you what is going on — how many entries have been indexed so far, how many are selected, and which entry the cursor is on. A progress bar appears there for anything that takes long enough to be worth reporting, and long operations can be cancelled.

---

[All documentation](README.md)
