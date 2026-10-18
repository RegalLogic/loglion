# Extensions

An **extension** teaches Log Lion to read a log format or connect to a data source that it does not handle on its own. Extensions are written by third parties — a vendor who supplies the equipment producing your logs, your own development team, or anyone who has published one — using the Log Lion Extension SDK.

Once an extension is installed and enabled, the sources it provides appear alongside Log Lion's own. A file type it supports shows up in the **File ▸ Open** dialog; a connection it provides appears among the named sources. Everything else — filtering, the boolean expression language, Find, bookmarks, the timeline, column statistics, export — works exactly as it does for a built-in format.

## Installing an Extension

The easiest way is to let Log Lion do it. Open **Tools ▸ Extensions**, click **Install...**, and choose the extension's package — the `.zip` file it was delivered in, usually in your Downloads folder, which is where the dialog starts. Log Lion checks the package, creates a folder for the extension in the right place, and unpacks it there; there is no need to unzip it first. A package that names other applications than Log Lion as the ones it was made for says so before it installs, and installing a newer version of an extension you already have asks before replacing it.

A bare `.llx` file installs the same way. If it came with other files beside it, you are asked whether to copy those as well; say yes unless you know they are unrelated, because an extension that ships more than one file usually needs all of them.

Log Lion refuses a package that could put files anywhere but the extension's own folder, or that is damaged or much larger than any extension needs, and installs nothing from it.

You are then asked whether to enable it, exactly as described below. Installing an extension and enabling it are separate steps: declining leaves the file installed but not loaded, and you will be asked again next time. If the extension came with a guide (a `README.md`), it opens next; **Read Me** in **Tools ▸ Extensions** shows it again later.

Updating an extension that is loaded cannot replace its files while Log Lion is using them, so the update is kept and finished the next time Log Lion starts, before anything is loaded.

Either way, **restart Log Lion** afterwards. Extensions are looked for once, at startup.

### Installing by hand

If you would rather copy the files yourself, an extension is a file with the `.llx` extension, in a folder of its own, under either of these locations:

- `%APPDATA%\Log Lion\Extensions\<Name>\` — just for you.
- `%ProgramData%\Log Lion\Extensions\<Name>\` — for everyone on the machine. Only an administrator can install here, and Log Lion checks that: if this folder can be written to by ordinary users, nothing in it is loaded.

**Tools ▸ Open Data Folder** opens `%APPDATA%\Log Lion\` so you do not have to type the path. If you installed from the Microsoft Store the folder is somewhere else (see [Where Log Lion Keeps Your Data](data-folder.md)), and Open Data Folder takes you to the right place. Create an `Extensions` folder there if it does not exist, then a folder for the extension, and put the `.llx` file — along with any other files it came with — inside it.

Log Lion looks for extensions only in these two places. It will not load one from its own program folder, from the folder a log file came from, or from anywhere else.

## Enabling an Extension

The first time Log Lion finds an extension it has not seen before, it asks whether to enable it:

- **Enable** — load it now and every time from now on.
- **Not Now** — do not load it this time. You will be asked again next time.
- **Never** — do not load it, and stop asking.

**An extension is a program, not a settings file.** Enabling one lets it run as part of Log Lion with your full user privileges: it can read and change anything you can. Only enable an extension you obtained from someone you trust, and treat it with the same care you would any other program you install.

The dialog shows everything Log Lion knows about the file before it runs: its name, where it is, its size, its SHA-256 fingerprint, and — if it is digitally signed — who signed it and whether that signature is valid. An extension that is not signed is not unusual and is not by itself a warning sign; it simply means Log Lion cannot tell you who wrote it.

Log Lion remembers your answer against the fingerprint of the file, not its name. So if the extension is updated, you will be asked again — the file has changed, and that is a fresh decision.

## Reviewing Extensions

**Tools ▸ Extensions** lists every extension found on your machine and what happened to it:

- **Loaded** — in use now, with the number of sources it provides.
- **Not approved** — found, but you have not enabled it. You will be asked at the next start.
- **Blocked** — you chose **Never**. It will not be loaded and you will not be asked again.
- **Approved — will load at the next restart** — enabled since Log Lion started.
- **Disabled after a failure** — the extension misbehaved and Log Lion switched it off to protect the rest of the session. See *If Something Goes Wrong* below.

From here you can **Install** a new extension, **Remove** one you installed (its folder is deleted; one that is loaded goes the next time Log Lion starts), **Block** one, **Revoke** a decision so that Log Lion asks about it again next time, read an extension's guide with **Read Me**, or **Open Folder** to see the file in Windows Explorer — or, with nothing selected, to open the folder extensions are installed into. Blocking and revoking take effect the next time Log Lion starts.

The **Publisher** column shows only what Log Lion could verify for itself. An extension that carries a valid digital signature shows the name on that signature; anything else shows **Not signed**. An extension can put any text it likes in its own vendor field — including the name of a company it has nothing to do with — so that text is shown in the details below the list, marked as self-declared, and never in the Publisher column.

## Starting Without Extensions

Hold **Shift** while Log Lion starts, or run `LogLion.exe --no-extensions`, and no extension is loaded at all. A banner across the top of the window confirms this while it is in force.

This is the way back if an extension prevents Log Lion from starting. Start without extensions, use **Tools ▸ Extensions** to block the one causing trouble, then start normally.

## If Something Goes Wrong

Extensions are separated from Log Lion carefully. One that crashes is switched off for the rest of the session rather than taking the application down with it, and one that returns nonsense — too many columns, malformed text — has that corrected or ignored rather than being trusted. If this happens you will see a banner at the top of the window, and the extension will show as **Disabled after a failure** in **Tools ▸ Extensions**.

Report the problem to whoever supplied the extension; it is not a fault in Log Lion. Restarting Log Lion enables it again, since the failure is not remembered between sessions.

If an extension you installed does not appear at all, check that the file ends in `.llx`, that it is in a folder of its own under one of the two locations above, and that you restarted Log Lion. **Tools ▸ Extensions** lists extensions that were found but not loaded, along with the reason.

## Writing an Extension

Extensions are written against the Log Lion Extension SDK, which is a single C header file with no library to link against. It can be used from C, C++, or any language able to produce a Windows DLL and call a C function.

The SDK includes two complete, documented sample extensions — one generating data in memory, one reading a real binary file format — and `llxprobe`, a command-line tool that loads an extension and checks it against the interface contract before you install it in Log Lion.

Extensions are worth writing for proprietary binary formats and for live connections to systems Log Lion cannot reach on its own. They are *not* the right tool for text log formats: the [Text Log Wizard](log-types-text-log-wizard.md) and [regular expression profiles](log-types-custom-parsed.md) already handle those, need no programming, and can be adjusted by the people using them.

---

[All documentation](README.md)
