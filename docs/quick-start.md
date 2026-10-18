# Quick Start

Ten minutes' worth of Log Lion, in the order you are likely to need it. Everything here has a fuller topic behind the link.

## 1. Open the log

**Ctrl+O**, or drag the file onto the window. Log Lion works out the format from the contents rather than the extension, and asks if more than one format could read it.

It does not read the file into memory — it builds an index — so a four-gigabyte log opens in seconds and you can start reading the top while the rest is still being indexed. The status bar counts up as it goes.

If your log is text in a shape Log Lion does not recognize, it still opens as a single **Text** column. To get real columns out of it, run the [Import Text Log Wizard](log-types-text-log-wizard.md) (**Ctrl+I**) once and save a profile; every future file of that shape then opens properly.

## 2. Find your way around

- **Ctrl+F** to [find](finding.md) text; **F3** for the next match.
- **F8** for the next entry of the same type — select an error and press it repeatedly to walk the errors.
- **Enter** for the whole of the selected entry, when the row is too narrow.
- **Alt+Left** to go back where you were. This one saves more time than it looks like it should. See [Navigating](navigating.md).

## 3. Cut the log down

Almost everything is faster once Log Lion has less to look at, so do this early.

Select the entry where the trouble starts and press **Ctrl+B**; select where it ends and press **Ctrl+E**. That is a [viewable range](limiting-the-range.md), and every filter, search and tool from now on only considers what is inside it. **Ctrl+Shift+X** clears it.

You can also drag across the [timeline](timeline.md) at the top of the window, which is easier when you know roughly *when* rather than which entry.

## 4. Filter

Type into the **Filter Settings** pane and apply. A bare word searches the message:

```

timeout
```

Or be precise about a column:

```

$Level eq "ERROR" and not health
```

**F4** toggles the filter off and on, which is how you check what it is hiding. The quickest start of all is **Filter ▸ Filter By** — select an entry, pick a column, and Log Lion writes the expression for you. See [Filtering](filtering.md).

## 5. Color instead of filtering

This is the habit that most distinguishes someone who knows the tool. A filter hides everything that does not match, and the context is often the part you needed.

- **Color ▸ Column ▸ Thread** (or session, or request id) gives every value its own color. Interleaved work separates into visible strands with nothing hidden.
- **Color ▸ Filter Result Set** (**Ctrl+Shift+S**) marks what your filter matches *in place*, instead of hiding the rest.

See [Coloring](coloring.md).

## 6. Get the answer out

- **Ctrl+Insert** copies as HTML **with the colors** — paste into a ticket and the reader sees what you saw.
- **File ▸ Export Selection** writes to HTML, CSV, JSON and more. Filter, **Ctrl+A**, export, and you have handed over the interesting 500 entries out of millions.

See [Exporting and Copying](exporting.md).

## Then what

| If you find yourself… | Look at |
| --- | --- |
| Asking the same question of every log | [Saved filters and filter sets](filtering.md) — and the **Ctrl+1**…**Ctrl+9** shortcuts that come with them. |
| Counting things by hand | [The analysis tools](analyzing.md) — Subtotals, Statistics, Word Counter. |
| Wanting an answer no tool gives | [Scripts](scripts.md) — a few lines of Lua over every entry. |
| Watching a problem happen | [Auto Refresh](live-logs.md) — set the filter up first, then reproduce. |
| Reaching for the mouse constantly | [Keyboard Shortcuts](keyboard-shortcuts.md). |

## Two things that trip people up

- **"Find cannot see text I know is there."** A [filter](filtering.md) or a [range](limiting-the-range.md) is limiting where it looks, or **Look in** is pointing at the wrong column. Clear the filter, **Ctrl+Shift+X** to clear the range, and try again.
- **"The timeline is empty and Go To Time is greyed out."** The format has not identified a timestamp column. Nominate one under **View ▸ Settings**, or use the Import Wizard. See [The Timeline](timeline.md).

---

[All documentation](README.md)
