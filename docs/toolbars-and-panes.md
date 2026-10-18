# Toolbars and Panes

Everything around the entry list is a **pane**. Panes can be moved, stacked, floated or closed, and Log Lion remembers the arrangement between sessions.

## The panes

| Pane | Shows |
| --- | --- |
| **Range** | The [timeline](timeline.md), and where you drag to set a [viewable range](limiting-the-range.md). |
| **Preview** | The selected entry in full, wrapped and syntax colored. Worth keeping open for any format with long messages. |
| **Filter Settings** | The expression editor, with **Variables** and **Filter Sets** tabs. See [Filtering](filtering.md). |
| **Source Info** | Path, format, entry count, file and index size, encoding, and which columns are the time and message fields. The first place to look when a format is not behaving as you expect. |
| **Fields** | The selected entry's columns as a name/value list. The quickest way to see which column a value actually lives in. |
| **Bookmarks** | Every [bookmark](bookmarks.md) in the log. A table of contents for the investigation. |

## Moving them

- **Drag the title bar** to move a pane. Docking guides appear as you drag — drop on an edge to dock there, or on the middle of another pane to stack the two as tabs.
- **Drag it away from any edge** to float it as a separate window. Useful on a second monitor: float **Preview** onto it and the entry list gets the whole main window.
- **Double-click the title bar** to send a floating pane back where it was.
- **The pin** in a pane's title bar makes it auto-hide — it collapses to a tab at the edge and slides out when you point at it. Good for panes you consult rather than watch.
- **The close button** hides a pane. **View ▸ Tool Bars & Panes** brings it back, which is the answer to "my Preview pane has vanished".

## Arrangements worth trying

The default suits general reading. Two others are worth knowing:

- **Reading long messages.** Make **Preview** tall and close **Filter Settings**. Formats like Windows Event Log and HAR carry far more text per entry than a row can show, and this turns the window into a reader.
- **Wide tables.** Close or auto-hide everything on the right and use **Alt+Shift+Enter** for full screen. A CSV or database log with fifteen columns needs the width more than it needs the panes.

## Toolbars

The toolbars are **Standard**, **Filter**, **Find**, **Navigation**, **Find Types** and the **Menu Bar** itself. Show and hide them from **View ▸ Tool Bars & Panes**, and drag them by the grip at the left end.

**Tools ▸ Customize** is where you change what is on them — add a command you use constantly, remove ones you never touch. You can also drag a button off a toolbar while holding **Alt**, which is quicker when you only want to remove one thing.

## The status bar

Along the bottom: indexing progress, how many entries are selected, the current entry number, and the log format. **View ▸ Tool Bars & Panes ▸ Status Bar** hides it, though it is usually the cheapest information in the window.

## Starting over

If the layout ends up somewhere you cannot recover — a pane dragged onto a monitor that is no longer attached is the classic — **Tools ▸ Customize** resets the toolbars and menus. The window layout is stored in the registry with the rest of Log Lion's settings; see [Where Log Lion Keeps Your Data](data-folder.md).

---

[All documentation](README.md)
