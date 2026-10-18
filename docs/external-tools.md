# External Tools

An **external tool** is your own program, script or web address added to the **Tools** menu. Log Lion launches it and passes values from what you have selected — the file name, the entry numbers, the timestamps — as command line arguments.

Add and edit them under **Tools ▸ Customize ▸ Tools**.

## When this is the right answer

Whenever the thing you want to do next is already a command. Log Lion has three ways to be extended and this is much the simplest:

| Use | When |
| --- | --- |
| **External tool** | The work happens *outside* Log Lion. Look an id up, open a file, run an existing script. |
| [Script](scripts.md) | The work is over the entries themselves and the answer is a table. |
| [Extension](extensions.md) | Log Lion cannot read the format at all. |

## Macros

Put these in the argument string and Log Lion substitutes them when the tool runs.

| Macro | Becomes | Example |
| --- | --- | --- |
| `%File` | The log's full path and file name. | `c:\logs\today.log` |
| `%FileName` | The file name alone. | `today.log` |
| `%Path` | The folder alone. | `c:\logs\` |
| `%Extension` | The file extension. | `.log` |
| `%FirstTimeStamp` | Timestamp of the first selected entry. | `17:24:54.545` |
| `%LastTimeStamp` | Timestamp of the last selected entry. | `17:24:55.052` |
| `%FirstEntryNumber` | Number of the first selected entry. | `1644` |
| `%LastEntryNumber` | Number of the last selected entry. | `1728` |

## Things people set up

### Look something up on the web

A tool can be a URL, opened in your browser. Point one at your issue tracker, your runbook, or a Windows error-code lookup, and a value you are staring at becomes one click from its explanation.

### Open the log somewhere else

```

Command:   C:\Program Files\Notepad++\notepad++.exe
Arguments: "%File" -n%FirstEntryNumber
```

Opens the same file in another editor at the entry you are on. Useful when you want to look at the raw bytes of a line that Log Lion is parsing into columns.

### Cut out the interesting part

```

Command:   powershell.exe
Arguments: -NoProfile -Command "Get-Content '%File' | Select-Object -Skip %FirstEntryNumber -First 500 | Set-Content '%Path%FileName.slice.txt'"
```

Writes the entries around your selection to a smaller file to send to someone. Note that [Export Selection](exporting.md) does this better in most cases — it understands the format — but a command has the advantage of doing exactly what you tell it.

### Archive or clean up

A tool that zips `%File`, copies it to a share, or deletes the rolled-over siblings in `%Path`. Log management from inside the log viewer.

## Quoting

**Put quotes around any macro that can contain a space**, which in practice means `%File`, `%Path` and `%FileName`. Write `"%File"`, not `%File`. A path like `C:\Program Files\logs\today.log` otherwise reaches your program as three separate arguments, and the failure is confusing because it only happens for some paths.

## Notes

- Timestamp macros need the log to have a time field. On a format without one they come through empty — see [The Timeline](timeline.md).
- Entry numbers start at 0, matching **Go To** and the status bar.
- An external tool is a program you chose, launched with your privileges. Log Lion does not inspect what it does.

---

[All documentation](README.md)
