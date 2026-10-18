# Live Logs, Refresh and Archives

A log on a running system is a moving target. Log Lion can follow one as it grows, and can open the compressed and rolled-over files it turns into afterwards.

## Refresh

| Command | Key | Does |
| --- | --- | --- |
| **View ▸ Refresh** | F5 | Re-read the log now, picking up whatever has been appended. |
| **View ▸ Auto Refresh** | Ctrl+F5 | Keep re-reading, so the view follows the log as it is written. |

Refresh is incremental. Log Lion indexes the new entries and adds them; it does not re-read the file from the beginning, so following a large log stays cheap.

## Following a live log

With **Auto Refresh** on, new entries appear as they are written. What you have set up survives each refresh — this is the point of it:

- Your [filter](filtering.md) keeps applying, so new entries are matched as they arrive. Filter to errors and leave it running, and the window becomes a live error monitor.
- [Coloring](coloring.md) keeps applying.
- [Bookmarks](bookmarks.md) and your position stay put.
- The [timeline](timeline.md) extends as time passes, so a burst is visible as it happens.

Reproducing a problem is the case this is for: set up the filter and coloring first, start Auto Refresh, then go and trigger the failure. What you see afterwards is the interesting entries only, already highlighted, in the order they happened.

Turn Auto Refresh off while you are reading. A view that keeps moving is hard to study, and scrolling back through something being appended to is a nuisance. F5 on demand is usually the better rhythm once you have caught the event.

## Log files that roll over

Most services do not write one endless file — they roll over into `service.1.log`, `service-2026-08-30.log` and so on. Log Lion understands this as a **file series**.

Configure it under **View ▸ Settings**, in the settings for the format you are reading:

- **File Series** — whether to treat matching files as a continuation of the same log.
- **File Name Mask** — the pattern that decides which files belong to the series.
- **Confirm Open Next File** — whether to ask before moving on to the next one.

This matters when an incident straddles a rollover. Without it you read to the end of one file and the story stops in the middle.

## Compressed logs

Log Lion opens **.zip** and **.gz** files directly. Archived logs need no unpacking first, and nothing is written to disk to read one — which also means opening a large compressed log takes longer than an uncompressed one, because it has to be decompressed to be indexed.

If you are going to work on the same archived log repeatedly, extracting it once will be quicker overall.

## Reading a log that is still being written

Log Lion opens files read-only and does not lock them, so having a log open here does not interfere with the service writing it.

Two things to expect:

- **A partial last entry.** If a refresh catches the writer mid-line, the final entry may be incomplete. The next refresh fixes it.
- **Truncation.** If the file is replaced or truncated rather than appended to — some rollover schemes do this — Log Lion notices the file no longer matches its index and re-reads it.

## Capturing output with no file at all

Two sources produce a live log with nothing on disk to point at:

- [Windows Debug Output](log-types-debug-output.md) captures what programs write with `OutputDebugString`, timestamped, as it happens. This is the one for debugging something that has no log file yet.
- [Windows Event Log](log-types-windows-event-log.md) reads the live event sources rather than a saved `.evtx`.

## Related

- [Limiting the Range](limiting-the-range.md) — note that a range with a fixed end will not include entries added afterwards.
- [Reading a Log](reading.md) — how indexing works and why following a large file stays fast.

---

[All documentation](README.md)
