# Windows Defender Firewall Log

Windows Defender Firewall can log allowed and dropped network connections to a file in [W3C Extended Log Format](log-types-w3c.md), per firewall profile (Domain, Private, and Public). Log Lion automatically detects when this logging is enabled and surfaces it as a named source, so you don’t need to hunt down the log file’s path or its registry configuration yourself.

This is a named source built on top of the W3C Extended Log Format parser — no separate file type or column layout is introduced. See [W3C Extended Log Format](log-types-w3c.md) for general parsing behavior and options; this page covers what’s specific to the Firewall log.

## Detection

On the local machine, Log Lion checks each firewall profile’s logging settings in the registry. A named source appears for a profile only when both of the following are true:

- Logging is enabled for that profile (dropped connections, successful connections, or both).
- The configured log file appears to exist.

If logging is enabled for more than one profile, a separate entry is listed for each — for example **Windows Defender Firewall Log.Domain Profile** and **Windows Defender Firewall Log.Public Profile**. If no profile has logging enabled, no entry is listed. This check is performed fresh every time the source list is opened, so enabling or disabling logging is reflected the next time you open **File > Open Source**.

Firewall logging is off by default. To enable it, run the following from an elevated command prompt:

```
netsh advfirewall set allprofiles logging droppedconnections enable
netsh advfirewall set allprofiles logging allowedconnections enable
```

## Administrator Access

The firewall log file is only readable by administrators. Selecting this source does **not** require Log Lion to already be running elevated — if the file can’t be read, Log Lion offers to restart itself with administrative privileges. Accept the prompt, approve the Windows User Account Control dialog, and Log Lion reopens the same source automatically once it restarts elevated.

## Tips

- Select **File > Open Source** and choose the profile you want under **Windows Defender Firewall Log**.
- Color rows by the **Action** column to visually separate `ALLOW` from `DROP` entries.
- Use Filters to narrow to a specific **src-ip**, **dst-ip**, or **dst-port** when investigating a particular connection.

## Columns

Columns come from the log file’s `#Fields` directive, the same as any W3C Extended Log Format file. A default Windows Defender Firewall log includes:

| Field Name | Description |
| --- | --- |
| `date` | Date of the connection event. |
| `time` | Time of the connection event. |
| `action` | The action the firewall took: `ALLOW` or `DROP`. |
| `protocol` | The protocol used, such as `TCP`, `UDP`, or `ICMP`, or a raw protocol number. |
| `src-ip` | Source IP address that sent the packet. |
| `dst-ip` | Destination IP address the packet was sent to. |
| `src-port` | Source port number, blank for protocols without ports. |
| `dst-port` | Destination port number. |
| `size` | Packet size in bytes. |
| `tcpflags` | TCP control flags present in the packet, in hexadecimal. |
| `tcpsyn` | TCP sequence number. |
| `tcpack` | TCP acknowledgement number. |
| `tcpwin` | TCP window size in bytes. |
| `icmptype` | ICMP type field. |
| `icmpcode` | ICMP code field. |
| `info` | Additional information; content varies by record type. |
| `path` | Direction of the communication: `SEND`, `RECEIVE`, `FORWARD`, or `UNKNOWN`. |

The exact set of fields depends on the `LogDroppedPackets` and `LogSuccessfulConnections` settings and the Windows version, since Log Lion reads whatever `#Fields` directive is actually present in the file.

## Options

Options are shared with the W3C Extended Log Format parser and are found under **File > Source Settings > W3C Extended Log Format**. See [W3C Extended Log Format](log-types-w3c.md) for details.

---

[All documentation](README.md)
