# Windows Event Trace Log (ETL)

The Event Trace Log format (`.etl`) is the binary output of an ETW (Event Tracing for Windows) session, captured with tools such as `logman`, `xperf`, `wpr` (Windows Performance Recorder), or Performance Monitor's Data Collector Sets. Each event carries a high-resolution timestamp, the provider that raised it, and a set of strongly-typed properties describing what happened — used for performance profiling, driver and kernel diagnostics, and detailed application tracing.

Log Lion decodes each event using the Windows Trace Data Helper (TDH) APIs and the event's provider manifest, producing a formatted **Message** summarizing the event's properties. If the provider that raised an event isn't registered on this machine — a common situation when opening a capture from a different computer — the row is still shown with all of its header fields (time, process, thread, level, and so on); only the Message column falls back to a placeholder noting that no manifest was available.

## Common Use Cases

- **Performance profiling** — Correlate **Provider**, **Task/Opcode**, and **Process ID**/**Thread ID** to trace what a specific thread or component was doing around a slow operation.
- **Driver and kernel diagnostics** — Inspect kernel provider events (process, thread, disk I/O, network) captured by `xperf` or WPR general-profile traces.
- **Application tracing** — Follow custom ETW providers instrumented into an application, using the **Message** column's decoded property values.
- **Field diagnostics** — Open a trace captured on a customer or lab machine and sent back for analysis, even when the exact provider versions aren't installed locally.

## Tips

- Log Lion detects `.etl` files by validating the trace file header, so files are recognized correctly even without relying on the extension alone.
- Only closed, complete trace files are supported — a trace still being written to by an active logging session should be stopped first (`logman stop <name> -ets`) before opening it in Log Lion.
- Because ETW does not support random access to individual events, the first open of a trace fully decodes every event; subsequent opens load instantly from a cached index file (`<filename>.etl.llidx`) stored alongside the trace.
- Filter by **Provider** to focus on one component, or by **Level** to isolate errors and warnings.
- The **Task/Opcode** column is a good starting point for spotting matched start/stop pairs (for example `Start` / `Stop`) that bound a timed operation.
- Adjust the **Time Zone Adjustment** option if timestamps appear in the wrong time zone.

## Columns

| Column | Description |
| --- | --- |
| **Time** | The date and time the event was recorded, converted from the trace's high-resolution timestamp. |
| **Provider** | The friendly name of the ETW provider that raised the event, or its GUID if the provider's manifest could not be resolved. |
| **Event ID** | The numeric event identifier defined by the provider. |
| **Level** | The event's severity as reported by ETW: *LogAlways*, *Critical*, *Error*, *Warning*, *Information*, or *Verbose*. |
| **Task/Opcode** | The named task and opcode the event belongs to (for example `PageFault_Start / Start`), when the provider's manifest defines names; otherwise a numeric fallback. |
| **Process ID** | The identifier of the process that raised the event. |
| **Thread ID** | The identifier of the thread that raised the event. |
| **Processor#** | The CPU core the event was recorded on. |
| **Keywords** | The provider-defined keyword bitmask associated with the event, shown in hexadecimal. |
| **Message** | A summary of the event's decoded properties as `name=value` pairs. Shows a diagnostic placeholder when the provider's manifest is unavailable. |

## Entry Types

Each row's icon and color reflect the event's **Level**, mapped onto Log Lion's common severity scheme:

| Type | Description |
| --- | --- |
| **Fatal** | A *Critical* level event. |
| **Error** | An *Error* level event. |
| **Warn** | A *Warning* level event. |
| **Info** | A *LogAlways* or *Information* level event. |
| **Debug** | A *Verbose* level event. |
| **Unknown** | An event whose level value did not match any of the above. |

## Options

Options are configurable settings found under **File > Source Settings > Windows Event Trace Log (ETL)**.

| Option | Values | Description |
| --- | --- | --- |
| Load / Save Full Index | Yes, No | When enabled, the decoded index is cached alongside the trace file so subsequent opens are fast. |
| Time Zone Adjustment | UTC to Local, UTC, User Offset | Controls how event timestamps are adjusted for display. |
| Custom Time Zone Offset | Numeric (minutes) | Custom offset applied when **Time Zone Adjustment** is set to *User Offset*. |
| Date Time Format | Format string | Controls how the **Time** column is formatted, using tokens similar to the POSIX `strftime()` function. |

## Properties

Properties are read-only metadata shown in the **Properties** pane.

| Property | Description |
| --- | --- |
| Event Count | Total number of events indexed from the trace. |
| File Size | Size of the file in bytes. |
| Created | File creation date and time. |
| Modified | Date and time the file was last modified. |

---

[All documentation](README.md)
