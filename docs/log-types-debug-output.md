# Windows Debug Output

The Windows Debug Output source captures real-time output from any process on the local machine that calls the `OutputDebugString()` or `OutputDebugStringW()` API functions. These functions send a text string to any attached debugger or to a system buffer that can be monitored by listener tools. Log Lion listens on the standard `DBWIN_BUFFER` shared memory mechanism.

`OutputDebugString()` is a common lightweight tracing mechanism in Windows applications that does not require a log file — messages are emitted at runtime and disappear unless captured. Log Lion makes those messages persistent, timestamped, and searchable.

This is a **live**, named source. No file is opened; Log Lion connects to the shared memory buffer and begins capturing as soon as the source is activated.

## Tips

- Select **File > Open** and choose **OutputDebugString** from the named sources list to begin capturing.
- The **PID** and **Process** columns let you isolate messages from a specific application when multiple processes are running simultaneously.
- Log Lion may be unable to capture output if another debugger (Visual Studio, WinDbg, etc.) is already attached to the target process, as a debugger takes exclusive ownership of the debug output channel.
- Use Filters to focus on messages from a specific process name or containing a keyword.
- For reference on the underlying mechanism, see <http://unixwiz.net/techtips/outputdebugstring.html>.

## Columns

| Column | Description |
| --- | --- |
| **Timestamp** | The time when the debug string was captured by Log Lion. |
| **PID** | The numeric Process Identifier of the process that called `OutputDebugString()`. |
| **Process** | The executable name of the process that emitted the message. |
| **Message** | The text string passed to `OutputDebugString()`. |

## Options

Options are configurable settings found under **File > Source Settings > Debug Output**.

| Option | Values | Description |
| --- | --- | --- |
| Time Zone Adjustment | UTC to Local, UTC, User Offset | Controls how captured timestamps are adjusted for display. |
| User Offset from UTC | Numeric (minutes) | Custom offset applied when **Time Zone Adjustment** is set to *User Offset*. |

---

[All documentation](README.md)
