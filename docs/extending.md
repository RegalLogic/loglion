# Extending Log Lion

Two different jobs, and it is worth being clear which one you have before you start.

|  | [Scripts](scripts.md) | [Extensions](extensions.md) |
| --- | --- | --- |
| Answers | "What does this log *tell* me?" | "How do I *read* this at all?" |
| Written in | Lua, in a text editor | C or C++, compiled to a DLL |
| Written by | You, in a few minutes | A developer, against the Extension SDK |
| Produces | A table of results | A new log format Log Lion can open |
| Runs | When you press Run | Loaded at startup, after you approve it |

## Which do I want?

**Log Lion can already open the file, and you want to know something about its contents** — how often something happens, which requests never completed, what the errors have in common. That is a [script](scripts.md). Start there: it is a text file, there is nothing to build, and the shipped example is a working answer to a real question.

**Log Lion cannot open the file at all**, because it is a proprietary binary format or lives in a system nothing else can reach. That is an [extension](extensions.md).

**It is a text format Log Lion does not recognize** — neither. Use the [Import Text Log Wizard](log-types-text-log-wizard.md) or [custom parsed files](log-types-custom-parsed.md). Both need no programming, both can be adjusted by whoever is using them, and between them they cover most text logs.

## External tools

There is a third, simpler kind of extension that needs no code at all: **external tools** are your own programs, scripts or web links added to the **Tools** menu, launched with values from the current selection passed as arguments. Set them up under **Tools ▸ Customize ▸ Tools**; the available macros are listed at the bottom of [Tools](analyzing.md).

This is the right answer when the thing you want to do is already a command — look an id up in a bug tracker, open the source file a stack frame names, or run a decoder over a value.

---

[All documentation](README.md)
