# Binary Data File

The Binary source type opens any file in a classic hex-dump view. Each row displays 16 bytes of the file: the byte offset on the left, the raw hexadecimal values in the center, and the printable ASCII representation on the right. Non-printable bytes are rendered as a period (`.`).

Every part of that layout can be changed — row width, grouping, hexadecimal case, offset format, and text encoding. See **Options** below.

This is a general-purpose viewer for any file whose contents you need to inspect at the byte level — executables, DLLs, proprietary binary formats, network captures, firmware images, database files, or any binary log format that lacks a dedicated parser.

## Tips

- Use **Filters** with regular expressions against the **ASCII** column to search for embedded text strings within binary files (e.g., URLs, error messages, version strings).
- The **Offset** column displays hexadecimal addresses. Use it to locate specific byte positions identified by other tools.
- Filter against the **Binary** column to search for specific byte sequences (e.g., magic bytes, protocol headers, signatures).
- Files that are not evenly divisible by 16 bytes end with a partial row. The cells past the end of the file are left blank in both the **Binary** and **ASCII** columns, so padding is never mistaken for a run of real zero bytes.
- Because each row holds 16 bytes, the last hexadecimal digit of the **Offset** is always zero and the position of a byte within the row is its offset from that address.
- Supports 64-bit file offsets, so files larger than 4 GB can be opened.

## Columns

| Column | Description |
| --- | --- |
| **Offset** | The byte offset of the first byte in this row, relative to the start of the file plus **Base Address**. See the **Offset Format** and **Base Address** options. |
| **Binary** | The hexadecimal representation of the bytes in this row. See the **Group Size** and **Hex Case** options. |
| **ASCII** | The characters for the same bytes. By default this is the printable ASCII range (`0x20`–`0x7E`) and all other bytes show a period (`.`). The heading changes to name whichever **Text Encoding** is in use. |

## Options

Options are set under **File > Source Settings > Binary Files** and are remembered between sessions. Changing **Bytes Per Row** reopens the current file, because it changes how many rows the file has.

| Option | Values | Description |
| --- | --- | --- |
| **Bytes Per Row** | 8, 16, 24, 32, 48, 64 | Bytes shown on each row. Default 16. |
| **Group Size** | None, 2, 4, 8, 16 bytes | Bytes between the wider gaps in the **Binary** column. Bytes are always separated by a single space; this adds a second space every N bytes. Default 4. |
| **Hex Case** | Lowercase, Uppercase | Case of the hexadecimal digits in the **Binary** column. Default Uppercase. |
| **Offset Format** | Hex, Decimal | How the **Offset** column is written. Hex is `0x` prefixed and uses eight digits below 4 GB and sixteen above, chosen from the file size so the column keeps a constant width. Default Hex. |
| **Base Address** | a number, e.g. `0x140000000` | Added to every offset, so addresses line up with a memory dump, a PE image base, or a firmware load address. Accepts hexadecimal with an `0x` prefix or plain decimal. Default 0. |
| **Text Encoding** | ASCII, Latin-1 (CP1252), OEM (CP437), UTF-8, UTF-16LE, UTF-16BE, EBCDIC (CP037) | How bytes are turned into characters in the text column. The column heading changes to name the encoding in use. **ASCII** is the safe default. **CP1252** is the Windows "ANSI" encoding a Western locale uses, so it is what many older application logs are written in, and it turns accented text into readable words instead of dots. **CP437** is the original MS-DOS encoding; almost nothing is written in it now, but it is the only choice that gives every byte from `80` to `FF` a distinct visible glyph, which makes repeated structures and field boundaries stand out as repeated shapes while you scroll. **EBCDIC** is for mainframe data. Characters are written one per character rather than one per byte, so UTF-16LE text reads as `gs\PITR\` and not `g.s.\.P.I.T.R.\.` — byte positions are read from the **Binary** column, which leaves this one free to spend its width on being legible. A UTF-16 column is therefore about half the width of a single byte one. |
| **Decoded Values Column** | None, Int8, UInt8, Int16 LE/BE, UInt16 LE, Int32 LE/BE, UInt32 LE, Int64 LE, Float32 LE, Float64 LE | Adds a fourth column showing the row as an array of that type, which makes a file holding a table of numbers readable. Only whole values are shown; a partial one at the end of the file is left blank. Changing this reopens the current file, because it changes the number of columns. Default None. |
| **Byte Order** | Auto, Little-endian, Both | Whether the preview pane shows a big-endian column beside the little-endian one. Big-endian data is rare on Windows outside network captures, and a second column of nonsense next to every real value is a distraction, so **Auto** shows it only on rows that actually read as big-endian. See **Byte Order Detection** below. **Little-endian** never shows it and **Both** always does. Default Auto. |
| **Detail Pane** | Full analysis, Plain text | Whether the preview pane analyzes the selected row or just shows its text. See **Preview Pane** below. Default Full analysis. |

## Preview Pane

Selecting a row shows an analysis of its bytes in the preview pane. On **Full analysis** it contains:

- Where the row sits — its offset in hexadecimal and decimal, its row number, how far into the file it is, and its classification.
- The row under a **column ruler** numbering the byte positions, so the byte at `+0x0B` is a lookup rather than a count.
- **Decoded values** — the row read as 8, 16, 32 and 64 bit integers and as `float32` and `float64`, in both byte orders, plus a GUID. Each type is read right *across* the row rather than only at its start, so a 16 byte row gives sixteen `int8` values, eight `int16`, four `int32` or two `int64`. That matters because a row is usually several fields: a header holding an 8 byte timestamp followed by an 8 byte count shows you both. `int8` spans both byte order columns, because a single byte reads the same either way. The unsigned line appears only when something in the signed one is actually negative.
- **Timestamps** — the row read as each of the epochs a Windows file is likely to hold, in both byte orders, shown only where the result is a believable date. Like the integer decodes these are read at each aligned position across the row, so a header carrying created, modified and accessed times shows all three, each tagged with its offset. Some decodes are much better evidence than others, and the weak ones are treated differently — see below.
- **Text decodes** — the same bytes under ASCII, Latin-1, OEM, UTF-8, UTF-16LE and UTF-16BE, so a row can be recognized as text without changing the **Text Encoding** option. A row of UTF-16LE text reads as `gs\PITR\` while the **ASCII** line above it shows `g.s.\.P.I.T.R.\.` — that difference is how you tell which encoding fits.
- **Bits** for the leading bytes, for flag words and packed fields.
- **Context** — the rows either side, with the selected row highlighted.

## Byte Order Detection

Nothing in a file says which way round its numbers are written, but a row of structured data gives it away. Structures are full of small numbers — counts, flags, lengths, indices, and fields left at zero — and read the wrong way round a small number becomes an enormous one. The `1` in `01 00 00 00 00 00 00 00` reads as `72,057,594,037,927,936` big-endian. Counting how many aligned words come out small in each direction therefore separates the two, and a timestamp that decodes to a believable date in one direction only is stronger evidence still, so it counts for more.

The verdict for the selected row is stated at the top of the preview, and on **Auto** the big-endian column appears only when the row reads as big-endian. A clear margin is required before either verdict is given, because a row that happens to start with a zero byte or two reads "small" big-endian by accident. Without that margin, 13.3% of rows in a Windows executable — a file that is little-endian by definition — claimed to be big-endian; with it, 1.3% do, while deliberately big-endian structures are still recognized because they win by far more. Rows with nothing to go on, including text, runs of zeros, and compressed or encrypted data, are reported as **undetermined** and shown little-endian. Set **Byte Order** to **Both** to see the column on every row regardless.

## Timestamp Decodes

The preview reads the leading bytes of the selected row as each of these, in both byte orders:

| Decode | Size | What it is | Matches random data |
| --- | --- | --- | --- |
| **SYSTEMTIME** | 16 bytes | The Windows structure: eight 16-bit fields for year, month, day of week, day, hour, minute, second and milliseconds. Every field is range checked, which is why it is the most reliable decode here. | 0.0000% |
| **Unix seconds (64-bit)** | 8 bytes | Seconds since 1970 — `time_t` on a 64-bit build. | 0.0000% |
| **Unix milliseconds** | 8 bytes | Milliseconds since 1970 — Java, JavaScript, and many log formats. | 0.0002% |
| **Unix microseconds** | 8 bytes | Microseconds since 1970 — Python, pcap, several databases. | 0.0188% |
| **OLE date** | 8 bytes | A double holding days since 1899-12-30 — VARIANT `DATE`, Excel serial dates, MSI. | 0.0244% |
| **FILETIME** | 8 bytes | 100-nanosecond units since 1601 — the native Windows timestamp, used throughout the file system and the API. | 0.1850% |
| **.NET DateTime ticks** | 8 bytes | The same 100-nanosecond tick counted from year 1 instead of 1601 — what a serialized .NET `DateTime` holds. | 0.2022% |
| **Unix nanoseconds** *(weak)* | 8 bytes | Nanoseconds since 1970 — Go, ClickHouse, some tracing formats. | 18.9% |
| **Unix seconds (32-bit)** *(weak)* | 4 bytes | Seconds since 1970 in 32 bits, the classic `time_t`. | 35.3% |
| **MS-DOS date/time** *(weak)* | 4 bytes | Not a seconds count at all — two packed 16-bit words holding calendar fields directly, with two-second resolution and no time zone. Obsolete as an operating system format, but ZIP froze it into its file headers in 1989, so every `.zip`, `.jar`, `.docx`, `.xlsx` and `.apk` stores its timestamps this way, as does every FAT and exFAT directory entry. | 47.9% |

**About the weak ones.** Any eight bytes can be read as a date, so a decode is only shown when the result lands between 1990 and 2100. How much that test is worth varies enormously. The right-hand column above is measured — 500,000 rows of random bytes fed through each decoder — and it shows the test all but eliminates false matches for most formats while doing very little for three of them. A 32-bit seconds count in the modern era occupies a third of the 32-bit value space, nanoseconds since 1970 occupy about a fifth of the 64-bit space, and the MS-DOS field constraints rule out only about half of theirs. No tightening of the range fixes that; it is a property of the formats.

That is also why the three weak decodes are read only at the **start** of the row, while the other seven are read at every aligned position across it. Scanning costs a reliable decode almost nothing — a `FILETIME` goes from matching 0.18% of random eight byte windows to 0.73% of random sixteen byte rows — but it ruins a weak one. Given four chances in a sixteen byte row a 32-bit seconds count matches **96.9%** of random rows, and MS-DOS **92.6%**; in a sixty-four byte row both are effectively certain. A line that appears on almost every row tells you nothing, so those three stay where they can still answer a real question — *is there a ZIP header exactly here?* — and are marked `weak · +00 only` to say so.

## Row Types

Each row is given a type from its own bytes. The type shows as an icon on the row, appears in the **Filters** pane, and can be used in a filter expression.

| Type | Meaning |
| --- | --- |
| **Text** | At least 90% of the bytes are printable ASCII, tab, carriage return, or line feed, and none of them is a zero. |
| **Zeros** | Every byte in the row is `00`. |
| **Fill** | Every byte in the row is the same non-zero value — `FF`, `CC`, and `DD` are common padding, and a row of `=` or `-` in a text file lands here too. |
| **Binary** | Anything else. |

This is what makes filtering useful on an opaque file. Filtering an executable to **Text** leaves just the embedded strings; hiding **Zeros** collapses the padding in a sparse file so the real content is a short scroll instead of a long one.

Row counts per type are not shown, because counting them would mean reading the whole file — which for this source can be very large.

## Properties

Properties are read-only metadata shown in the **Properties** pane.

| Property | Description |
| --- | --- |
| Created | File creation date and time. |
| Modified | Date and time the file was last modified. |
| Accessed | Date and time the file was last accessed. |
| File Size | Size of the file in bytes. |
| Detected Format | What the file appears to be, identified from its leading bytes — see **Detected Format** below. |
| Rows | Number of rows in the view, which depends on **Bytes Per Row**. |
| Bytes Per Row | The row width currently in use, so the Properties pane explains the layout. |
| Full Path | The full path of the open file. |

## Detected Format

When the file is opened its first bytes are checked against a list of known signatures, and the result is shown as the **Detected Format** property. Recognized types include Windows executables (following the MZ stub through to the PE header to report **x86**, **x64** or **ARM64**), ELF and Mach-O executables, ZIP, 7-Zip, RAR, CAB, gzip, bzip2, XZ and Zstandard archives, Compound File documents such as `.doc`, `.xls` and `.msi`, PNG, JPEG, GIF, BMP, ICO and WebP images, WAV and AVI media, Ogg and Matroska containers, PDF documents, SQLite databases, Windows registry hives, pcap and pcapng captures, Java class files and WebAssembly modules. Anything unrecognised, including plain text, shows as **Unknown**.

This is for information only. The Binary source is the fallback for files no other source can parse, and it never claims a file on the strength of a signature — recognizing a file as a ZIP archive does not stop you opening it here, and does not cause it to be opened here instead of somewhere better.

---

[All documentation](README.md)
