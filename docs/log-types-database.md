# SQL Database

Log Lion can read a log that lives in a database table rather than a file. The connection is made over ODBC, so any database with an installed ODBC driver can be used — Microsoft SQL Server, PostgreSQL, MySQL and MariaDB, Oracle, SQLite, IBM DB2 and others.

You do not connect from a dialog. Instead you open a small text file called a **connection profile**, saved with the `.lldb` extension, which describes what to connect to and what to read. Because a profile is just a file, it works with **File > Open**, the recent file list, drag and drop, and the command line exactly like any other log.

## Getting Started

A profile is an ordinary text file of `name = value` lines saved with the `.lldb` extension. Blank lines are ignored, and anything after a `#` or `;` at the start of a line is a comment. Values run to the end of the line and are taken literally, so a connection string keeps its backslashes, braces, and semicolons exactly as written.

### A Basic Profile

Create a text file named `app_log.lldb`. Four entries are usually all that is needed:

```

# Name shown in the window title and the recent file list.
name       = Application Log

# How to reach the database.  Trusted_Connection uses your Windows login,
# so no password has to be stored in this file.
connection = Driver={ODBC Driver 17 for SQL Server};Server=localhost;Database=applog;Trusted_Connection=Yes;

# The table to read.  May be schema qualified.
table      = dbo.app_log

# The ordering column - normally the primary key.  This is what lets Log Lion
# fetch one page at a time efficiently.  See "Paging and the Key Column".
key        = id
```

Open it with **File > Open**. Log Lion connects, reads the table's schema, and works out which column holds the timestamp, the severity, the message and the context by looking at the column names and the types the driver reports. When the guesses are right, nothing else is required.

### A More Complete Profile

Everything below is optional. Add only the entries you actually need — a profile that names the roles explicitly is worth writing when the column names are unusual, when the log is large, or when the level values do not match the common conventions.

```

# ---- Connection ---------------------------------------------------------
name           = Production Application Log

# Omitting PWD= is deliberate: the driver prompts for the password when the
# log is opened, so it is never written to this file.
connection     = Driver={ODBC Driver 18 for SQL Server};Server=sql01.example.com;Database=applog;UID=logreader;Encrypt=Yes;
prompt         = complete

timeout        = 60          # query timeout, seconds
logintimeout   = 10          # connection timeout, seconds

# ---- What to read -------------------------------------------------------
table          = dbo.app_log
schema         = dbo

# Listing columns explicitly is faster than * and fixes their order.
# A column whose name is a reserved word must be quoted here, as [level] is.
columns        = message, ts, [level], logger, id

# Applied to every query.  Literal SQL.
where          = ts >= DATEADD(day, -7, GETDATE())

key            = id
keytype        = int
ascending      = true

# ---- Column roles -------------------------------------------------------
# Only needed when the names would not be guessed correctly.
timecolumn     = ts
severitycolumn = level
messagecolumn  = message
navcontextcolumn = logger

# ---- Display order ------------------------------------------------------
# 'columns' put message first so the row icon sits beside the message text.
# 'columnorder' puts the display back into natural reading order.  Entries
# may name a column or use the role aliases time / severity / navcontext /
# message.
columnorder    = time, severity, navcontext, message, id

# ---- Severity -----------------------------------------------------------
# 'auto' already handles level names, syslog 0-7, and OpenTelemetry 1-24.
# Map individual values only for a scale none of those cover.
severity.scheme  = auto
severity.default = info
severity.map.30  = warn
severity.map.40  = error

# ---- Volume -------------------------------------------------------------
pagesize       = 500         # rows fetched per page
maxrows        = 5000000     # stop indexing beyond this
cachebudgetmb  = 256         # memory allowed for cached rows

# ---- Scripting ----------------------------------------------------------
# Optional.  A bare file name in %APPDATA%\Log Lion\Plugins\LuaDb\
# script       = sql_log.lua
```

## Connection Strings

The **connection** entry is the part most likely to give trouble, and almost always for the same reason: the driver name does not exactly match a driver installed on the machine. Get that right and the rest is usually straightforward.

### Anatomy

A connection string is a list of `Keyword=Value` pairs separated by semicolons. Order does not matter. Keywords are case-insensitive; values are generally not.

```

Driver={ODBC Driver 17 for SQL Server};Server=sql01;Database=applog;Trusted_Connection=Yes;
|                                 |             |                |
|                                 |             |                |__ how to authenticate
|                                 |             |___________________ which database on that server
|                                 |_________________________________ which machine to reach
|__ which ODBC driver to load - must match an installed driver exactly
```

Only **Driver** is universal. Every other keyword is defined by the driver you named, which is why the same idea is spelled `Server` for SQL Server, `Server`+`Port` for PostgreSQL, and `Dbq` for file-based drivers.

Braces around the driver name are required whenever it contains spaces or punctuation, which in practice is always. Write `Driver={PostgreSQL Unicode(x64)}`, not `Driver=PostgreSQL Unicode(x64)`.

### Finding Your Driver Name

The name must be copied *exactly* as installed, including the version number, capitalization, spaces and any punctuation. `ODBC Driver 17 for SQL Server` and `SQL Server` are two different drivers, and `ODBC Driver 18 for SQL Server` is a third. There are two reliable ways to see what is present.

**PowerShell** lists them with their architecture, which makes it the quicker check:

```

Get-OdbcDriver | Select-Object Name, Platform | Sort-Object Platform, Name
```

**ODBC Data Sources** (the **Drivers** tab) shows the same information in a dialog. Note that Windows names these backwards from what you would expect:

| Program | Shows |
| --- | --- |
| `C:\Windows\System32\odbcad32.exe` | **64-bit** drivers — the ones Log Lion can use. |
| `C:\Windows\SysWOW64\odbcad32.exe` | 32-bit drivers. Log Lion cannot use these. |

#### The 32-bit trap

**Log Lion is a 64-bit application and can only load 64-bit ODBC drivers.** The two lists are separate and often differ substantially — a machine can easily have twice as many 32-bit drivers installed as 64-bit ones, usually left behind by older Office components.

If a driver is visible in the ODBC administrator but Log Lion reports `Data source name not found and no default driver specified`, this is almost certainly the cause: you are looking at the 32-bit list. Install the 64-bit build of the driver, or confirm with:

```

Get-OdbcDriver -Platform '64-bit' | Select-Object -ExpandProperty Name
```

### Authentication

How you prove who you are is driver-specific, but the options fall into a few families.

| Method | Typical keywords | Notes |
| --- | --- | --- |
| **Windows integrated** | `Trusted_Connection=Yes`<br>`Integrated Security=SSPI` | Best option where it is available. Uses your Windows login, so there is no password to store anywhere. SQL Server and a few others only. |
| **Prompted** | omit `PWD=`, leave **prompt** at `complete` | **Recommended when integrated authentication is not possible.** The driver asks for the password when the log is opened, so it is never written to the profile. |
| **Database login** | `UID=` and `PWD=`<br>(`Uid=`/`Pwd=`, `User=`/`Password=`) | Works everywhere, but puts the password in a plain text file. Use a read-only login, and prefer the prompted form above. |
| **Entra ID / Azure AD** | `Authentication=ActiveDirectoryInteractive`<br>`Authentication=ActiveDirectoryIntegrated` | SQL Server driver 17 and later. The interactive form opens the standard sign-in window, including multi-factor prompts, and needs **prompt** left at its default. |
| **None** | — | File-based drivers such as SQLite and the text driver read files directly and authenticate through normal file permissions. |

### Examples

Replace each driver name with whatever is actually installed on your machine.

```

# ---- Microsoft SQL Server -----------------------------------------------
# Windows login.  Nothing secret in the file.
Driver={ODBC Driver 17 for SQL Server};Server=sql01;Database=applog;Trusted_Connection=Yes;

# Named instance, and a local developer instance.
Driver={ODBC Driver 17 for SQL Server};Server=sql01\SQLEXPRESS;Database=applog;Trusted_Connection=Yes;
Driver={ODBC Driver 17 for SQL Server};Server=(localdb)\MSSQLLocalDB;Database=applog;Trusted_Connection=Yes;

# SQL login, password left out so the driver prompts for it.
Driver={ODBC Driver 18 for SQL Server};Server=sql01;Database=applog;UID=logreader;Encrypt=Yes;

# ---- PostgreSQL ---------------------------------------------------------
# Note Port is a separate keyword rather than part of Server.
Driver={PostgreSQL Unicode(x64)};Server=10.0.0.5;Port=5432;Database=applog;Uid=logreader;

# ---- MySQL / MariaDB ----------------------------------------------------
Driver={MySQL ODBC 8.0 Unicode Driver};Server=10.0.0.6;Port=3306;Database=applog;User=logreader;

# ---- Oracle -------------------------------------------------------------
# The driver name embeds the Oracle home, so it varies per machine.
Driver={Oracle in OraClient19Home1};Dbq=//db01:1521/APPLOG;Uid=logreader;

# ---- SQLite -------------------------------------------------------------
# A file, not a server.  No authentication.
Driver={SQLite3 ODBC Driver};Database=C:\logs\app.db;

# ---- CSV files via the text driver --------------------------------------
# Dbq is the FOLDER; the table name is then the file name, e.g. applog.csv
Driver={Microsoft Access Text Driver (*.txt, *.csv)};Dbq=C:\logs\csv;Extensions=asc,csv,tab,txt;
```

#### Encryption on newer SQL Server drivers

**ODBC Driver 18 for SQL Server changed the default to `Encrypt=Yes`.** A connection string that worked with driver 17 may fail with driver 18 against a server using a self-signed certificate, typically with a certificate chain error. Either install a trusted certificate on the server, or, for a development server only, add `TrustServerCertificate=Yes`.

### Using a DSN Instead

A **DSN** is a named connection saved in the ODBC administrator. The profile then refers to it by name:

```

dsn = ProductionAppLog
```

This keeps server names and credentials out of the profile, which is useful for a shared profile. The cost is that the DSN must be created on every machine that opens it, so the profile is no longer self-contained. Create a **System DSN** in the 64-bit administrator if more than one user needs it.

### Best Practices

- **Use a read-only login.** This is the only dependable protection against a profile running something destructive — see [Security](#security). The **readonly** entry asks the driver nicely and is widely ignored.
- **Prefer integrated authentication, then prompting, then a stored password**, in that order.
- **Test the connection before writing the profile.** Create a temporary DSN in the 64-bit ODBC administrator and use its **Test** button. That isolates a connection problem from a profile problem.
- **Name the database explicitly.** Relying on the login's default database works until someone changes it.
- **Keep the trailing semicolon.** It is harmless, and it makes appending the next keyword less error-prone.
- **Set a short `logintimeout` for a server that may be unreachable**, so a mistyped host fails quickly instead of hanging.

### Where To Look Things Up

| For | Look at |
| --- | --- |
| Which drivers this machine has | `Get-OdbcDriver`, or the 64-bit `odbcad32.exe` |
| Keywords a specific driver accepts | That driver's own documentation. Vendors document their own keyword set; there is no common list beyond `Driver` and `DSN`. |
| Worked examples for almost any database | *connectionstrings.com* — organized by driver, and generally reliable. Confirm the driver name against what is installed before using one. |
| What Log Lion actually connected with | **Source Properties**. The **Connection**, **DBMS** and **Driver** rows show what was used, with any password masked. |

## Profile Reference

### Connection

| Entry | Description |
| --- | --- |
| **connection** | A full ODBC connection string. Required unless **dsn** is given. |
| **dsn** | The name of a configured ODBC data source, as an alternative to a connection string. |
| **prompt** | `complete` (default), `never`, or `always`. With `complete`, the driver asks for anything the connection string leaves out — which is how a password can be supplied without ever being written to the file. |
| **timeout** | Query timeout in seconds. Default 30. |
| **logintimeout** | Connection timeout in seconds. Default 10. |
| **readonly** | Requests a read-only session. Default true. This is *advisory*: many drivers ignore it. See [Security](#security). |
| **dialect** | Normally left alone. Log Lion detects the database from the driver and uses the appropriate paging syntax. Set explicitly to `mssql`, `postgres`, `mysql`, `oracle`, `sqlite`, `db2` or `generic` to override. |

### What To Read

| Entry | Description |
| --- | --- |
| **table** | The table to read. May be schema qualified, as in `dbo.app_log`. |
| **schema** | Schema name, if not already part of **table**. |
| **columns** | The columns to select, in order. Defaults to `*`. This is literal SQL, so a column whose name is a reserved word must be quoted here. |
| **where** | A filter applied to every query. Literal SQL. |
| **sql** | A complete query, used instead of **table**. Convenient for joins and computed columns, but it disables fast paging — see [Paging](#paging). |
| **key** | The ordering column. The single most important entry in the profile — see [Paging](#paging). |
| **keytype** | `int` (default), `string` or `datetime`. Describes the key so it can be bound correctly. |
| **ascending** | Sort direction of the key. Default true. |
| **orderby** | Overrides the ORDER BY expression when it differs from the key. |

### Column Roles

Roles tell Log Lion which column means what. Each is guessed from the column names if omitted, and the timestamp is also guessed from the type the driver reports, so these are often unnecessary.

| Entry | Description |
| --- | --- |
| **timecolumn** | The column holding the entry timestamp. Drives the timeline and the time range tools. |
| **timeformat** | Only needed when the timestamp is stored as *text*. A real DATE, TIME or TIMESTAMP column is converted by Log Lion into a consistent form, so the default already matches whatever the driver returns. |
| **severitycolumn** | The column holding the level. |
| **messagecolumn** | The column holding the message text. |
| **navcontextcolumn** | The column used for navigation and coloring, typically a logger, service or thread name. |

### Volume

| Entry | Description |
| --- | --- |
| **pagesize** | Rows fetched per page. Default 500, clamped between 50 and 20000. |
| **maxrows** | Stops indexing after this many rows and shows a notice. Default 5,000,000. |
| **cachebudgetmb** | Memory budget for cached rows, in megabytes. Default 256. |
| **countsql** | A replacement for the row count query, for when `COUNT(*)` is too slow. |

## Paging and the Key Column

Log Lion never loads a whole table. It reads one page of rows at a time, and how it asks for each page makes a very large difference on a big log.

When **key** names a unique, indexed, ordered column — usually the primary key — each page is fetched with a query of the form `WHERE key > ? ORDER BY key`. That is a single index seek, and it costs the same whether you are looking at the first page or the ten thousandth.

Without a usable key, Log Lion must fall back to `OFFSET`, which requires the database to produce and discard every preceding row. Scrolling deep into a large table then becomes progressively slower. A free-form **sql** entry cannot be rewritten into a keyed query, so it always uses the slower path; prefer **table** with **where** where you can.

Check **Source Properties** to see which is in use. The **Paging** property reads either `Keyset on "id", page size 500` or `OFFSET (slow) - set 'key' to an indexed ordered column`.

If the key column is unusable, the log will not open and the error says so specifically, rather than failing later during a scroll.

## Column Order

Two separate things control where a column appears.

**columns** is the SELECT list, so it also fixes the underlying column indexes. The first column is special: it is where the row's entry type icon is drawn. Putting the message there places the icon beside the message text.

**columnorder** then controls only where each column is *displayed*. So a log can have the message as the first column internally and still read in the natural order:

```

columns     = message, ts, level, logger, id
columnorder = time, severity, navcontext, message, id
```

Entries may name a column, or use one of the role aliases `time`, `severity`, `navcontext` and `message`. Aliases keep a profile usable against another database whose columns are named differently. Anything left out keeps its natural position and is appended, so a partial list is fine.

A profile's **columnorder** takes precedence over a column layout saved from a previous session.

## Severity

Values in the severity column are mapped onto Log Lion's entry types. The default scheme recognizes the usual level names (`ERROR`, `WARN`, `INFO` and so on), syslog severities 0–7, and OpenTelemetry severity numbers 1–24.

| Entry | Description |
| --- | --- |
| **severity.scheme** | `auto` (default), `text`, `syslog`, `otel`, `python`, or `custom`. |
| **severity.default** | The type used when a value is not recognized. Default `info`. |
| **severity.map.*value*** | Maps one specific value onto a type. Repeat as needed. |

Use the map for scales the built-in schemes do not cover:

```

severity.map.30 = warn
severity.map.40 = error
```

Entry type counts are exact. They come from a single grouped query over the whole table rather than from the rows read so far, which is something a file based log cannot offer.

## Security

**A connection profile is closer to a script than to a configuration file.** The **table**, **where** and **sql** entries become SQL exactly as written, so a profile can run any statement at all against the server it connects to, using your credentials.

**readonly** does not prevent this. It asks the driver for a read-only session and a good many drivers, including the Microsoft SQL Server driver, ignore the request. A database login that only has read permission is the only dependable control.

Because of this, the first time you open a profile Log Lion shows you the server it is about to contact and the query it is about to run, and asks for confirmation. The prompt appears *before* connecting, since connecting to a server named by an untrusted profile already discloses your credentials to it.

Choosing to remember a profile records the connection, query, filter and script only. Renaming a profile or changing its page size will not ask again; changing what it executes will.

Treat a `.lldb` you were sent the way you would treat a script you were sent.

## Passwords

A profile is plain text, so the safest arrangement is to keep the password out of it entirely. In order of preference:

- Use integrated authentication, for example `Trusted_Connection=Yes` on SQL Server.
- Omit `PWD=` and leave **prompt** at its default. The driver asks for the password when the log is opened and it is never written to disk.
- Only as a last resort, place the password in the connection string.

Log Lion removes passwords from the Properties pane and from every error message, but it cannot protect the file itself.

## Scripting

For a schema that needs more than the profile entries can express, a profile may name a Lua script:

```

script = sql_log.lua
```

The script's `Describe` function returns the columns and the SQL to use, and whatever it returns overrides the profile. It runs once, after connecting, and is told which database it is talking to, so it never has to guess. Log Lion executes the SQL the script returns; the script is not involved in reading individual rows, so a slow script cannot make scrolling stutter.

Scripts are loaded only from `%APPDATA%\Log Lion\Plugins\LuaDb\`, and a **script** entry containing a path or a parent reference is refused. A profile you were sent can therefore only name a script you have already installed. A worked template, `sql_log.lua`, is supplied with Log Lion.

## Options

Settings under **File > Source Settings > Database** apply to every database log, because they are stored per source type. Anything that varies from one database to the next belongs in the profile instead.

| Option | Description |
| --- | --- |
| Row cache budget (MB) | Memory allowed for cached rows before the least recently used pages are discarded. |
| Query timeout (seconds) | Default query timeout, when a profile does not set one. |
| Time Zone Adjustment | Whether timestamps are shown as stored, converted from UTC to local time, or shifted by a custom offset. |
| Custom Time Zone Offset | The offset applied when the adjustment above is set to a custom value. |
| Date Time Format | How timestamps are displayed, using the same tokens as the other log types. |
| Timeline | Enables or disables the timeline band for database logs. |

## Properties

Properties are read-only metadata shown in the **Properties** pane.

| Property | Description |
| --- | --- |
| Profile | Full path of the `.lldb` file. |
| Connection | The connection string, with any password replaced by asterisks. |
| DBMS | Database product and version reported by the server. |
| Driver | ODBC driver name and version. |
| Dialect | The SQL dialect chosen for paging. |
| Query | The query used to read the schema. |
| Row Count | Number of rows indexed. |
| Paging | Keyset or OFFSET, and the page size. See [Paging](#paging). |
| Time Field | The column being used as the timestamp. |
| Message Field | The column being used as the message. |
| Severity Field | The severity column, the scheme in use, and how many values are explicitly mapped. |
| Navigation Field | The column used for navigation and coloring. |
| Script | The Lua script in use, or *(none)*. |
| Row Cache | Pages currently cached and the memory they occupy. |
| Last Error | The most recent problem, if any. |

## Entry Types

Database logs use the standard severity types: **Fatal**, **Error**, **Warn**, **Notice**, **Info**, **Debug**, **Trace**, **Success**, and **Unknown** for a value in the severity column that could not be interpreted. A table with no severity column shows every row as **Info**.

## Troubleshooting

| Symptom | Cause |
| --- | --- |
| Data source name not found | The driver named in the connection string is not installed, or is the wrong architecture. Log Lion is 64-bit and needs a 64-bit driver; check the list in the 64-bit **ODBC Data Sources** administrator. |
| Reserved word errors | A column name is a reserved word in that database. `level` is a common example. Quote it in **columns**, using the quoting that database expects. The role entries such as **severitycolumn** take the plain name and should not be quoted. |
| Scrolling is slow on a large table | Check **Source Properties > Paging**. If it says OFFSET, set **key** to an indexed ordered column, and use **table** rather than a free-form **sql**. |
| Timestamps are empty or wrong | If the column is text rather than a real date type, set **timeformat** to match how it is written. |
| Columns appear in an unexpected order | A saved layout from a previously opened database log is being reused. Set **columnorder** in the profile, which takes precedence. |
| The confirmation prompt no longer appears | The profile has been remembered. It will ask again if its connection, query, filter or script changes. |
| Row counts look approximate | Indexing stopped at **maxrows**. Raise it in the profile. |

---

[All documentation](README.md)
