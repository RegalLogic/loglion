# W3C Extended Log Format

The W3C Extended Log Format (ELF) is a standardized text format for web server access logs, defined by the World Wide Web Consortium at <http://www.w3.org/TR/WD-logfile.html>. It is the default format used by Microsoft Internet Information Services (IIS) and is also produced by the Windows Defender Firewall, Azure CDN, and a variety of network appliances. Each file begins with comment directives (`#Version`, `#Software`, `#Fields`) that describe the log format, followed by space-separated data rows.

## Common Producers

- Microsoft IIS (Internet Information Services) — HTTP access logs
- Windows Defender Firewall — network connection logs. Log Lion detects this automatically; see [Windows Defender Firewall Log](log-types-windows-firewall-log.md).
- Azure CDN / Front Door access logs
- Network appliances and reverse proxies configured for ELF output

## Tips

- Log Lion reads the `#Fields` directive and automatically names and orders columns. No manual configuration is typically needed.
- The **Field Labels** option lets you switch between friendly names (e.g., *Client IP*) and the raw directive names (e.g., `c-ip`) used in the `#Fields` line.
- The `date` and `time` fields are combined by Log Lion into a single timestamp for timeline analysis.
- Log Lion detects ELF files automatically by reading the `#Version` and `#Fields` directives near the start of the file.

## Columns

Columns are populated dynamically from the `#Fields` directive. Common fields include:

| Field Name | Friendly Name | Description |
| --- | --- | --- |
| `date` | Date | Date of the request. |
| `time` | Time | Time of the request. |
| `c-ip` | Client IP | IP address of the client. |
| `cs-username` | User Name | Authenticated user name, or a hyphen if unauthenticated. |
| `s-ip` | Server IP | IP address of the server that handled the request. |
| `s-port` | Server Port | Server port number. |
| `cs-method` | Method | HTTP method (GET, POST, etc.). |
| `cs-uri-stem` | URI Stem | The path portion of the requested URL. |
| `cs-uri-query` | URI Query | The query string portion of the URL. |
| `sc-status` | Protocol Status | HTTP response status code (e.g., 200, 404). |
| `sc-bytes` | Bytes Sent | Number of bytes the server sent to the client. |
| `cs-bytes` | Bytes Received | Number of bytes the server received from the client. |
| `time-taken` | Time Taken | Time in milliseconds to process the request. |
| `cs(User-Agent)` | User Agent | Browser or client user-agent string. |
| `cs(Referer)` | Referer | URL of the referring page. |

## Properties

Properties are read-only metadata shown in the **Properties** pane.

| Property | Description |
| --- | --- |
| Created | File creation date and time. |
| Modified | Date and time the file was last modified. |
| Accessed | Date and time the file was last accessed. |
| File Size | Size of the file in bytes. |
| Index Size | Number of indexed entries. |
| Average Line Size | Average number of bytes per entry. |

## Options

Options are configurable settings found under **File > Source Settings > W3C Extended Log Format**.

| Option | Values | Description |
| --- | --- | --- |
| Field Labels | Friendly names, Directive names | Controls whether column headers display human-readable labels or the raw `#Fields` directive tokens. |
| Load / Save Full Index | Yes, No | When enabled, the entry offset index is persisted to disk for fast reopening. |
| Free Index Reserve >(KB) | Numeric | Minimum file-growth threshold (in KB) that triggers a partial index rebuild on refresh. |
| Line Endings | Auto, Windows (CR LF), Unix (LF), Macintosh (CR) | Controls how entry boundaries are detected. |

---

[All documentation](README.md)
