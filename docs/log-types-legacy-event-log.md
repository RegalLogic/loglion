# Windows Legacy Event Log

The Windows Legacy Event Log format (`.evt`) is the binary event log file format used by Windows 2000, Windows XP, and Windows Server 2003. Starting with Windows Vista, Microsoft replaced this format with the newer XML-based `.evtx` format. Legacy `.evt` files are still encountered when diagnosing older systems, reading archived log files, or working with virtual machines running legacy operating systems.

Unlike the live [Windows Event Log](log-types-windows-event-log.md) source, this source type opens an `.evt` file directly from disk rather than connecting to a live channel. Log Lion can repair and partially display `.evt` files that have become corrupt due to improper shutdown or circular buffer wrapping.

## Tips

- Log Lion detects `.evt` files automatically by their binary header signature.
- Even corrupt or wrapped circular-buffer files can be opened; Log Lion uses the file header and footer to calculate valid record boundaries.
- The **Date** and **Time** columns are separate in `.evt` files, reflecting how the format stores date and time independently.
- Adjust the **Time Zone Adjustment** option if the timestamps were recorded in a different time zone.

## Columns

| Column | Description |
| --- | --- |
| **Message** | The formatted message text of the event, prefixed with the severity level. |
| **Date** | The date portion of when the event was logged. |
| **Time** | The time portion of when the event was logged. |
| **Source** | The application or component that generated the event. |
| **Category** | An application-defined category string. |
| **Event ID** | The numeric event identifier. |
| **Computer** | The name of the computer where the event was recorded. |
| **User** | The user account associated with the event. |
| **Data** | The hexadecimal value of the raw event data if fewer than five bytes; otherwise the byte count. |

## Entry Types

| Type | Description |
| --- | --- |
| **Success** | A successful operation. |
| **Error** | A significant problem. |
| **Warning** | A non-critical potential issue. |
| **Information** | An informational status record. |
| **Audit Success** | A successful security access audit. |
| **Audit Failure** | A failed security access audit. |
| **Unknown** | An event whose type could not be determined. |

## Options

Options are configurable settings found under **File > Source Settings > Windows Legacy Event Log**.

| Option | Values | Description |
| --- | --- | --- |
| Time Zone Adjustment | UTC to Local, UTC, User Offset | Controls how timestamps are adjusted for display. |
| User Offset from UTC | Numeric (minutes) | Custom offset applied when **Time Zone Adjustment** is set to *User Offset*. |

---

[All documentation](README.md)
