# Where Log Lion Keeps Your Data

Everything Log Lion saves for you lives in one folder, and nothing it saves goes anywhere near your logs. **Tools ▸ Open Data Folder** opens it:

```
%APPDATA%\Log Lion\
```

That expands to something like `C:\Users\you\AppData\Roaming\Log Lion\`. Because it is your roaming profile, a domain account carries these settings between machines.

## If you installed from the Microsoft Store

Windows gives each Store app a private copy of the folders it writes to, so the Store version of Log Lion keeps this folder somewhere else:

```
%LOCALAPPDATA%\Packages\RegalLogic.LogLion_id\LocalCache\Roaming\Log Lion\
```

The *id* part is a short code Windows assigns. Everything below works the same way — only the location differs — and **Tools ▸ Open Data Folder** always opens the right one, so use it rather than typing a path from this page. Files you put in `%APPDATA%\Log Lion\` yourself are not seen by the Store version.

The registry settings described below are kept in the app's private copy of the registry too, so they do not appear under that key in Registry Editor.

## What is in it

| Path | Holds |
| --- | --- |
| `Filters\` | Saved [filters and filter sets](filtering.md). A `SampleFilters` set is written here the first time the folder is created. |
| `Scripts\` | Your [Lua scripts](scripts.md), plus the two shipped samples. |
| `Extensions\` | Per-user [extensions](extensions.md), each in a folder of its own. |
| `Text Log Profiles\` | Profiles saved by the [Import Text Log Wizard](log-types-text-log-wizard.md). |
| `Plugins\LuaDb\` | Scripts and profiles for [database log sources](log-types-database.md). |
| `RegExProfiles.xml` | Named sets of [highlight expressions](coloring.md), and the profiles used by [custom parsed files](log-types-custom-parsed.md). |
| `SyntaxColoringRules.xml` | The [syntax highlighting](coloring.md) rules. |
| The license file | Your license, if you have one. |

Machine-wide extensions, installed for everyone rather than just you, live separately:

```
%ProgramData%\Log Lion\Extensions\
```

Only an administrator can install there, and Log Lion checks that: if that folder can be written to by ordinary users it loads nothing from it. See [Extensions](extensions.md).

## Settings not kept in files

Window size and position, pane layout, toolbar customization, column widths and order, the recent file list and your [extension trust decisions](extensions.md) are stored in the registry, under:

```
HKEY_CURRENT_USER\Software\Regal Logic\Log Lion\
```

## Things worth doing with this folder

### Back it up

Copy the whole `Log Lion` folder. Filters and scripts represent real work — the accumulated understanding of what your logs mean — and they are the part that would be tedious to reconstruct.

### Share what you have built

These are ordinary files. A filter set that encodes how to read your service's log, or a script that answers a question your team asks weekly, is worth more to a new colleague than a document explaining the log. Send them the file and have them drop it in the same place.

### Reset one feature

Deleting a file returns that feature to its defaults, and only that feature. Delete `SyntaxColoringRules.xml` to go back to the built-in coloring; delete a script to have the shipped sample restored. **None of this can affect your log files** — Log Lion opens those read-only and never writes to them.

### Move to a new machine

Copy the folder across before your first run on the new machine. Filters, scripts, profiles and extensions all come with it. Window layout and toolbar customization will not, as those are in the registry.

## Finding the log you had open

**Tools ▸ Copy File Path** puts the current log's full path on the clipboard, and **File ▸ Open Recent** lists what you have been looking at lately.

---

[All documentation](README.md)
