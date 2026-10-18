# HTTP Archive (HAR)

The HTTP Archive format (HAR) is a JSON-based file format that records all HTTP network activity captured by a browser's developer tools. It is the standard export format from Chrome DevTools, Firefox Developer Tools, Edge DevTools, and browser-based network recording proxies such as Fiddler and Charles. Each entry in a HAR file represents one HTTP request/response pair along with precise timing data.

HAR files differ from JSON Lines in structure: the entire file is a single JSON document where all entries live inside the `log.entries` array. Log Lion uses a streaming parser to index each entry by its byte offset so that individual entries can be read without loading the entire file into memory.

## Common Use Cases

- **Performance analysis** — Identify slow requests using the **Duration (ms)** column. Sort or filter to find the longest-running calls.
- **API debugging** — Filter for a specific endpoint URL and inspect request/response details in the detail pane.
- **Error investigation** — 4xx and 5xx responses are automatically classified as **Error** entries and displayed in red, making failures immediately visible.
- **Redirect tracing** — 3xx responses appear as a distinct **Redirect** type so redirect chains are easy to follow.
- **Security review** — Inspect headers and payloads to verify that sensitive data is not inadvertently transmitted.

## Tips

- To capture a HAR file in Chrome: open DevTools (**F12**), go to the **Network** tab, reproduce the issue, then right-click any request and choose **Save all as HAR with content**.
- Filter by **Method** (e.g., `POST`) or by URL substring to focus on a specific API.
- The **Message** column provides a compact summary of every request: `METHOD URL -> STATUS StatusText (Xms)`.
- Index caching is supported: the first open of a large HAR file builds the index; subsequent opens load it instantly from the cached index stored alongside the file.
- Entries that cannot be parsed (e.g., due to file truncation) are shown as **Corrupt** with a diagnostic message; valid entries before and after remain fully accessible.

## Columns

| Column | Description |
| --- | --- |
| **Time** | The date and time when the request was initiated, from `startedDateTime` (ISO 8601 format). |
| **Method** | The HTTP request method, such as `GET`, `POST`, `PUT`, or `DELETE`. |
| **Status** | The HTTP response status code (e.g., `200`, `404`, `500`). |
| **URL** | The full URL of the request. |
| **MIME Type** | The MIME type of the response content (e.g., `application/json`, `text/html`). |
| **Size** | The size of the response body in bytes (`bodySize`). Displays empty if not reported. |
| **Duration (ms)** | Total elapsed time from the start of the request to the last byte of the response, in milliseconds. |
| **Message** | A composite summary: `METHOD URL -> STATUS StatusText (Dms)`. For corrupt entries, displays a diagnostic error description. |

## Entry Types

| Type | Description |
| --- | --- |
| **HTTP** | A normal successful request (status 1xx or 2xx). |
| **Redirect (3xx)** | A redirect response (status 300–399). |
| **Error (4xx/5xx)** | A client error (4xx) or server error (5xx) response. Displayed in red. |
| **Corrupt** | An entry whose JSON could not be parsed, typically due to file truncation or corruption. The Message column describes the error. Indexing continues past corrupt entries. |

## Properties

Properties are read-only metadata shown in the **Properties** pane.

| Property | Description |
| --- | --- |
| HAR Version | The HAR specification version declared in the file (e.g., `1.2`). |
| Creator | The name of the tool that generated the file (e.g., *Chrome DevTools*, *Firefox*). |
| Entry Count | Total number of HTTP request/response entries indexed from the file. |
| File Size | Size of the file in bytes. |
| Created | File creation date and time. |
| Modified | Date and time the file was last modified. |

---

[All documentation](README.md)
