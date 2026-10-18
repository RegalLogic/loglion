# Windows Event Log

The Windows Event Log is the centralized logging facility built into every version of Windows. It records events from the operating system, device drivers, and applications across three primary channels: **Application**, **Security**, and **System**. Additional channels exist for individual Windows components and installed software.

This source type connects to a **live** event log on the local machine via the Windows Event Log API. No file is required; Log Lion reads events directly from the system. For archived event log files use the [Windows Legacy Event Log](log-types-legacy-event-log.md) or Windows Event Log (.evtx) source instead.

## Tips

- Select **File > Open** and choose a named source such as *Application*, *Security*, or *System* to connect to that channel.
- Filter by **Entry Type** to see only errors or warnings at a glance.
- The **Source** column identifies the application or Windows component that logged the event — use it to quickly narrow to events from a specific service.
- The **Event ID** column, combined with the **Source**, uniquely identifies the event definition. Use Microsoft’s online event database to look up descriptions and remediation steps for unfamiliar IDs.
- Adjust the **Time Zone Adjustment** option if timestamps appear in the wrong time zone.

## Columns

| Column | Description |
| --- | --- |
| **Message** | The formatted message text of the event, prefixed with the severity level. |
| **Time** | The date and time when the event was logged. |
| **Source** | The application, service, or Windows component that generated the event. |
| **Category** | An application-defined category string that further classifies the event. |
| **Event ID** | A numeric identifier that uniquely describes the event within its source. |
| **Computer** | The name of the computer where the event originated. |
| **User** | The user account associated with the event, or a blank value if not applicable. |
| **Data** | The hexadecimal value of the raw event data if fewer than five bytes; otherwise the byte count of the data section. |

## Entry Types

| Type | Description |
| --- | --- |
| **Success** | Indicates a successful operation. |
| **Error** | A significant problem such as a failed operation or data loss. |
| **Warning** | A potential problem that is not immediately critical but may indicate a future issue. |
| **Information** | A record of a successful operation or a change in state for informational purposes. |
| **Audit Success** | A security audit event that records a successful access attempt. |
| **Audit Failure** | A security audit event that records a failed access attempt. |
| **Unknown** | An event whose type could not be determined. |

## Options

Options are configurable settings found under **File > Source Settings > Windows Event Sources**.

| Option | Values | Description |
| --- | --- | --- |
| Time Zone Adjustment | UTC to Local, UTC, User Offset | Controls how event timestamps are adjusted for display. *UTC to Local* converts stored UTC times to the local system time zone. |
| User Offset from UTC | Numeric (minutes) | Custom offset applied when **Time Zone Adjustment** is set to *User Offset*. |

---

[All documentation](README.md)
