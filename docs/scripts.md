# Scripts

A **script** is a small program you write that Log Lion runs over every entry in the open log. It sees each row in order, keeps whatever running totals you want, and produces a table of results you can sort, export, and navigate from.

The other entries on the **Tools** menu each answer one fixed question. A script answers *your* question. Scripts are written in **Lua**, a small language that takes an hour to pick up, and they live in files you own and can edit in any text editor.

## When to use a script

Reach for something simpler first if it will do:

| If you want to… | Use |
| --- | --- |
| See only the entries matching some condition | A [filter](filtering.md) |
| Count how often each distinct value appears in a column | [Subtotals](tools-subtotals.md) |
| Get min/max/average of numbers in a column | [Statistics](tools-statistics.md) |
| Time the gap between matching start and end entries | [Profiler](tools-profiler.md) |
| Anything that needs its own logic, state, or arithmetic | **A script** |

Scripts earn their keep when the question involves *remembering something between rows* — correlating a request with its response, spotting a gap in a heartbeat, grouping messages that are the same apart from an ID, tracking a state machine, or summing a number only while some other condition holds.

## Getting started

Open **Tools ▸ Scripts**. The dialog lists the scripts you have, and Log Lion puts two there the first time you open it:

- **Error Summary** — a working script that groups error and warning entries by the shape of their message and counts them. Run it on a log to see what a result looks like.
- **New Script** (`template.lua`) — a commented skeleton with every hook and setting in place.

Press **New...** to start a script of your own. Log Lion asks for a file name, writes the template to it, and opens it in whatever program you use for `.lua` files. If nothing is associated with `.lua`, Notepad opens instead — but any editor will do, and an editor with Lua syntax highlighting is worth setting up if you are going to write more than one.

Edit, save, then come back to **Tools ▸ Scripts**, select the script and press **Run**. There is nothing to compile or install; Log Lion reads the file each time you run it.

### Where scripts live

Scripts are files with a `.lua` extension in:

```
%APPDATA%\Log Lion\Scripts\
```

**Open Folder** in the Scripts dialog takes you straight there — use it if you installed from the Microsoft Store, which keeps the folder elsewhere (see [Where Log Lion Keeps Your Data](data-folder.md)). Only that folder is looked in, and only the folder itself — not subfolders. To share a script with a colleague, send them the file and have them drop it in the same place.

The two shipped samples are written only if a file of that name is not already there, so your edits are safe. If you delete one and want it back, delete the file and reopen the dialog.

### Why is Run greyed out?

The reason is shown in the box at the bottom of the dialog. It is one of:

- **No log is open.** A script runs over a log; open one first. (You can still write, copy and delete scripts with no log open.)
- **Log Lion is still indexing.** Wait for the entry count in the status bar to settle.
- **A filter is applied.** Clear the filter. A script always runs over the whole viewable range rather than over a filtered subset — see [Which entries a script sees](#range) below.

## The shape of a script

A script is an ordinary Lua file that defines up to three functions and, optionally, a table called `Script` describing itself:

```

Script = {
    name        = "My Script",
    description = "What this script answers about a log.",
    results     = { "Entry", "Message" },
}

function Setup(ctx)
    -- Called once, before the scan.  Create your tables here.
end

function OnRow(row)
    -- Called once for every entry, in order.
end

function Teardown(ctx)
    -- Called once, after the scan.  Report what you found.
end
```

**`OnRow` is required.** `Setup`, `Teardown` and the `Script` table are all optional. The order is always the same: your file is loaded top to bottom (so anything at file scope runs first), then `Setup`, then `OnRow` once per entry in ascending entry order, then `Teardown`.

Because entries arrive strictly in order, you can rely on "the previous row" being the row before this one in the log. Nothing runs your script on two entries at once, and you do not have to think about threads.

### A complete first script

This one lists every entry whose message mentions a timeout, with a running count:

```

Script = {
    name        = "Timeouts",
    description = "Every entry mentioning a timeout.",
    results = {
        { name = "#",     sort = "numeric", justify = "right" },
        { name = "Entry", role = "entry" },
        { name = "Message" },
    },
}

local count

function Setup(ctx)
    count = 0
end

function OnRow(row)
    local text = row._text or ""

    if text:lower():find("timeout", 1, true) then
        count = count + 1
        ll.emit(count, row._n, text)
    end
end

function Teardown(ctx)
    print(string.format("%d timeouts in %d entries.", count, ctx.rowsProcessed))
end
```

## Reading a row

The `row` passed to `OnRow` gives you the entry's columns and a few facts about the entry itself.

### Columns

Use the column's heading, exactly as it appears in the log view. Matching ignores case, and a column's short alias works too:

```

local level = row.Level
local msg   = row.Message
local host  = row["Remote Host"]      -- headings with spaces
```

Every column comes back as **text**. If you want a number, convert it:

```

local ms = tonumber(row.Duration) or 0
```

A name that is not a column of this log returns `nil`, so `row.Level or ""` is a safe way to write a script that runs on more than one log format. You can also ask by column number, which is the fastest form because it skips the name lookup — columns are numbered from 1:

```

local first = row[1]
local n     = #row                    -- how many columns this log has
```

### Facts about the entry

Names beginning with an underscore describe the entry rather than a column. They are spelled that way so they can never collide with a real column heading — a log with a column called `Time` is perfectly ordinary.

| Field | Meaning |
| --- | --- |
| `row._n` | Entry number. **The first entry in a log is 0.** This is the value to emit for a `role = "entry"` column so **Goto** works. |
| `row._time` | Timestamp as `YYYY-MM-DD HH:MM:SS.mmm` in local time. Sortable as text. See the warning below. |
| `row._ticks` | The same instant as a whole number, for arithmetic. The unit is 100 nanoseconds, so divide by 10,000 for milliseconds. |
| `row._type` | The entry type label — `"Error"`, `"Warning"`, and so on. See the warning below. |
| `row._indent` | Scope nesting level, for formats that have one. |
| `row._text` | The log's message column, whatever it happens to be called. The portable way to get "the text of this entry". |

**Two things differ between log formats, and a script that ignores them will quietly find nothing:**

- **Not every log has a timestamp.** A plain text log with no time field set reports the *same* instant for every entry. Check `ctx.hasTime` in `Setup` before you show or subtract times.
- **Not every log classifies its entries.** A Windows Event Log tells Log Lion that an entry is an error, so `row._type` is `"Error"`. A plain text log has a single entry type called `"Lines"`, and the severity is only ever words inside the message. The shipped **Error Summary** script shows how to handle both: try `row._type` first, then look for the level in the text.

### The row is only valid inside OnRow

Do not store `row` in a table and read it later — Log Lion reuses the underlying entry as soon as `OnRow` returns, and by `Teardown` it holds nothing. **Copy the values you need**, not the row:

```

-- WRONG - keeps the row
table.insert(slow, row)

-- Right - keeps the values
table.insert(slow, {n = row._n, text = row._text, ms = tonumber(row.Duration) or 0})
```

## Producing results

### ll.emit — rows in the results grid

`ll.emit(...)` adds one row to the results table. Each argument becomes a cell:

```

ll.emit(count, row._n, "some text")
```

You can call it from `OnRow` (one result row per matching entry) or from `Teardown` (a summary built up while scanning). Numbers stay numbers, so a count column sorts as 1, 2, 10 rather than 1, 10, 2.

Describe the columns with `Script.results`:

```

results = {
    { name = "Count",   sort = "numeric", justify = "right" },
    { name = "Entry",   role = "entry" },
    { name = "Message" },
},
```

| Key | Meaning |
| --- | --- |
| `name` | The column heading. |
| `sort` | `"string"` (the default), `"numeric"` for whole numbers, or `"real"` for numbers with a decimal point. This is how the column sorts when the heading is clicked. |
| `justify` | `"left"` (the default), `"right"` — usual for numbers — or `"center"`. |
| `role` | `"entry"` marks the column that holds an entry number. That is what enables the **Goto** button, so selecting a result row and pressing it jumps the log view to that entry. Emit `row._n` into it. |

Where only a heading is needed, a plain string will do: `results = { "Count", "Message" }`. If you skip `Script.results` altogether the grid still appears, with generic headings.

### print — the output box

`print(...)` writes to the output box below the results grid. It is never a message box, so it is safe to call while scanning. Use it to say what the script did, and especially to explain an empty result:

```

print(string.format("%d sessions, %d never closed.", total, leaked))
```

`ll.print(...)` is the same function under another name.

### Exporting and copying

**Export...** writes the grid to a CSV file, in the sort order and column order you are looking at. **Ctrl+C** copies the selected rows to the clipboard, tab separated, so they paste into a spreadsheet as cells.

## Making a script fast

Log Lion runs scripts over millions of entries without difficulty, and the per-entry overhead is deliberately tiny. Two settings in the `Script` table do far more than anything you can do in Lua, because both let Log Lion avoid work *before* your code is reached.

### Script.filter — skip entries entirely

A [filter expression](filter-language.md), checked by Log Lion itself. Entries that do not match never reach `OnRow` at all:

```

Script = {
    filter = '$Level eq "ERROR"',
}
```

On a log where one entry in a hundred is interesting, this is the difference between a script that finishes immediately and one you cancel. It uses the same syntax as the filter box, including `%name` user variables, so you can develop the expression in the filter pane and paste it in.

### Script.columns — parse less of each entry

Naming the one column you read lets the log source pull out that field instead of taking apart the whole line:

```

Script = {
    columns = { "Message" },
}
```

**This is a promise, not a hint.** If you declare one column and then read another, the second comes back empty. Leave `columns` out if you read more than one. (Log Lion also ignores the shortcut automatically when a `Script.filter` reads a different column, because the filter would otherwise be handed unparsed text.)

### Script.entryTypes — only certain kinds of entry

```

Script = {
    entryTypes = { "Error", "Warning" },
}
```

Only useful on formats that classify entries — see the warning about `row._type` above. Names that the log does not have are ignored, with a note in the output box.

### Did it help?

Turn on filter statistics (**Filter ▸ Show Statistics**) and a run reports how many entries per second it managed, how many reached the script, and whether the single-column shortcut was in effect. Run once with a setting and once without to see the difference on your own log.

### Things that are slow

- **Building a string per entry** that you then throw away. Test with `find` before you build.
- **A pattern applied to a whole message** when the part you want is near the front. `text:sub(1, 64)` first.
- **Emitting a row per entry** on a large log. Summarize in `Teardown` instead; the grid stops collecting at 100,000 rows anyway.
- **Concatenating a report with `..` in a loop.** Collect into a table and `table.concat` once.

## Which entries a script sees

A script sees the **viewable range** of the open log: normally every entry, or just the range you set with **View ▸ Range**. `ctx.minEntry` and `ctx.maxEntry` tell you what that range was.

Scripts cannot be run while a filter is applied. If you want a script to look at a subset, express the subset in `Script.filter` rather than filtering the view first — that way the script is self-contained and gives the same answer whoever runs it.

## How do I…

### …count how often each distinct value occurs?

```

local counts

function Setup(ctx) counts = {} end

function OnRow(row)
    local key = row.Level or "(none)"
    counts[key] = (counts[key] or 0) + 1
end

function Teardown(ctx)
    for key, n in pairs(counts) do
        ll.emit(n, key)
    end
end
```

### …show the top 10 rather than everything?

Collect the keys into a list as you meet them, sort the list in `Teardown`, then emit only the first few. (A plain `pairs()` loop has no order, which is why the list is kept separately.)

```

local counts, order

function Setup(ctx) counts, order = {}, {} end

function OnRow(row)
    local key = row._text or ""
    if counts[key] == nil then
        counts[key] = 0
        order[#order + 1] = key
    end
    counts[key] = counts[key] + 1
end

function Teardown(ctx)
    table.sort(order, function(a, b) return counts[a] > counts[b] end)

    for i = 1, math.min(10, #order) do
        ll.emit(counts[order[i]], order[i])
    end
end
```

### …measure the time between two entries?

Use `row._ticks`, which is a number. Units are 100 nanoseconds, so divide by 10,000 to get milliseconds:

```

local previous

function Setup(ctx)
    previous = nil
    if not ctx.hasTime then
        print("This log has no time field - gaps cannot be measured.")
    end
end

function OnRow(row)
    local now = row._ticks

    if previous ~= nil then
        local gapMs = (now - previous) / 10000
        if gapMs > 5000 then
            ll.emit(row._n, string.format("%.1f s gap", gapMs / 1000), row._text)
        end
    end

    previous = now
end
```

### …match a request with its response?

Remember the opening entry in a table keyed by whatever identifies the pair, and look it up when the closing entry arrives. Whatever is still in the table at the end never completed — usually the interesting part:

```

local open, count

function Setup(ctx) open, count = {}, 0 end

function OnRow(row)
    local text = row._text or ""

    local id = text:match("BEGIN request (%w+)")
    if id then
        open[id] = {n = row._n, ticks = row._ticks}
        return
    end

    id = text:match("END request (%w+)")
    if id and open[id] then
        local ms = (row._ticks - open[id].ticks) / 10000
        ll.emit(string.format("%.1f", ms), open[id].n, id)
        open[id] = nil
        count = count + 1
    end
end

function Teardown(ctx)
    for id, start in pairs(open) do
        ll.emit("never completed", start.n, id)
    end
    print(count .. " completed requests.")
end
```

### …pull a value out of the message text?

Lua patterns are not regular expressions, but they cover most of what you need and are quick. `match` returns the captures, or `nil`:

```

local ms = row._text:match("completed in (%d+) ms")
local ip = row._text:match("(%d+%.%d+%.%d+%.%d+)")
local a, b = row._text:match("from (%S+) to (%S+)")
```

The common classes are `%d` digit, `%a` letter, `%w` alphanumeric, `%s` space, `%x` hex digit, and `%S` "not a space". A `%` escapes a magic character, so a literal dot is `%.` and a literal percent is `%%`.

For a plain substring, `find` with the fourth argument set is faster and needs no escaping at all:

```

if row._text:find("timeout", 1, true) then ... end
```

### …group messages that differ only by an ID or a number?

Replace the parts that vary, then group on what is left. This is what **Error Summary** does:

```

local function shape(text)
    text = text:gsub("%x%x%x%x%x%x%x%x%-[%x%-]+", "<guid>")
    text = text:gsub("0[xX]%x+", "<hex>")
    text = text:gsub("%d+", "<n>")
    return text
end
```

### …build a histogram by hour?

```

local byHour

function Setup(ctx) byHour = {} end

function OnRow(row)
    local hour = row._time:sub(1, 13)          -- "2026-08-30 14"
    byHour[hour] = (byHour[hour] or 0) + 1
end

function Teardown(ctx)
    local hours = {}
    for hour in pairs(byHour) do hours[#hours + 1] = hour end
    table.sort(hours)

    for _, hour in ipairs(hours) do
        ll.emit(byHour[hour], hour, string.rep("#", byHour[hour] // 100))
    end
end
```

### …write one script that works on several log formats?

Read `ctx.columns` in `Setup` and adapt, or use `ll.column` to test whether a column exists:

```

local levelColumn

function Setup(ctx)
    levelColumn = ll.column("Level") or ll.column("Severity") or ll.column("Priority")

    for _, c in ipairs(ctx.columns) do
        print(string.format("column %d: %s", c.nbr, c.label))
    end
end

function OnRow(row)
    local level = levelColumn and row[levelColumn] or row._text
end
```

### …stop early, or handle a cancel?

Pressing **Cancel** during a run stops the scan and still calls `Teardown`, so whatever you have collected is reported. `ll.cancelled()` tells you it happened, which is worth checking if `Teardown` does a lot of work:

```

function Teardown(ctx)
    if ctx.cancelled then
        print("Cancelled - these results are partial.")
    end
end
```

There is no way for a script to stop the scan itself. If you only want part of the log, set a **View ▸ Range** or use `Script.filter`.

## When something goes wrong

**The script will not load.** A syntax error, or a missing `OnRow`, is reported straight away with the line number, before anything runs.

**An error inside `OnRow`.** That entry is skipped and the error is written to the output box with a traceback. Only the first 20 are listed — the rest are counted — and the run stops once 100 errors have happened, so a broken script over a large log gives you a usable report instead of a wall of identical messages. Change the limit with `Script.maxErrors`.

**The script found nothing.** The results window still opens, with your `print` output in it — which is the reason to print something in `Teardown` even when there is nothing to report. The usual causes are a column name that does not exist in this log (check the headings in the log view), a `Script.filter` that matches nothing, or relying on `row._type` on a format that does not classify entries.

**The script is stuck.** Press **Cancel**. It works even for a script caught in an endless loop.

**A stray `nil`.** `attempt to index a nil value` almost always means a column name that is not in this log. Write `local text = row.Message or ""` rather than assuming.

## Reference

### The Script table

| Key | Meaning |
| --- | --- |
| `name` | Shown in the Scripts list and on the results window. |
| `description` | One line, shown beside the name. |
| `results` | The results grid columns. See above. |
| `filter` | A [filter expression](filter-language.md). Only matching entries reach `OnRow`. |
| `columns` | The columns the script reads. Naming exactly one speeds up parsing. |
| `entryTypes` | Only entries of these types reach `OnRow`. |
| `maxErrors` | Stop after this many errors in `OnRow`. Default 100. |

### The ll table

Also available under the longer name `loglion`.

| Function | Meaning |
| --- | --- |
| `ll.emit(...)` | Add a row to the results grid. |
| `ll.print(...)` | Write a line to the output box. Same as `print`. |
| `ll.column(name)` | The number of that column, or `nil` if the log does not have it. |
| `ll.detail(row, column)` | The expanded detail text for a cell, where the format provides one. |
| `ll.cancelled()` | `true` once Cancel has been pressed. |
| `ll.version` | Version of this scripting interface. |

### The ctx table

Passed to `Setup` and `Teardown`.

| Field | Meaning |
| --- | --- |
| `logName`, `logTitle` | The open log's full path and its display title. |
| `sourceType` | The format, for example `"Text File"`. |
| `entryCount` | Entries in the whole log. |
| `minEntry`, `maxEntry` | The range this run covers. |
| `columns` | Array of `{label, alias, nbr}`, one per column. |
| `hasTime` | `false` when the log has no time field, in which case `row._time` is meaningless. |
| `timeColumn` | Number of the time column, when there is one. |
| `scriptPath` | The file this script was loaded from. |
| `rowsProcessed` | In `Teardown`: entries scanned. |
| `rowsMatched` | In `Teardown`: entries that reached `OnRow` after `filter` and `entryTypes`. |
| `errors` | In `Teardown`: errors raised by `OnRow`. |
| `elapsed` | In `Teardown`: seconds the scan took. |
| `cancelled` | In `Teardown`: `true` if the run was cancelled. |

### Limits

| Limit | Value |
| --- | --- |
| Rows kept in the results grid | 100,000 |
| Lines kept in the output box | 200, the most recent |
| Errors listed in full | 20 |
| Errors before the run stops | 100, or `Script.maxErrors` |

Reaching a limit is reported in the output box rather than passing unnoticed.

## About the Lua in Log Lion

Scripts are Lua 5.5. The standard library is available, including `string`, `table`, `math`, `os` and `io`, so a script can read a lookup file or write a report of its own if you want it to. The `debug` library is removed. `print` goes to the output box; there is no console.

A script written for Lua 5.4 almost always runs unchanged. Two differences can show up:

- The variable of a `for` loop can no longer be assigned to. The script is rejected with *attempt to assign to const variable*; declare a local copy inside the loop instead (`local i = i`).
- A fractional number turned into text with `tostring` or `..` now shows every digit needed to read it back exactly, so `0.1 + 0.2` prints as `0.30000000000000004` rather than `0.3`. Use `string.format("%.2f", n)` when you want a fixed number of places.

If Lua is new to you, the string library and its patterns are the parts worth reading first — they are what most log scripts spend their time in.

**A script is a program, and it runs with your privileges.** Log Lion does not sandbox it, for the same reason it does not sandbox a batch file: the folder scripts are loaded from is one only you can write to, and nothing runs unless you pick it and press Run. Treat a script someone sends you the way you would treat any other program from that person, and read it before you run it.

## See also

- [Boolean Expression Language](filter-language.md) — the syntax for `Script.filter`.
- [Tools](analyzing.md) — the fixed analyses, which are often quicker than writing a script.
- [Extensions](extensions.md) — for teaching Log Lion to *read* a format it does not know, which is a different job.

---

[All documentation](README.md)
