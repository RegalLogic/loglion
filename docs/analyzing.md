# Tools

Tools in Log Lion are used to analyze and manage logs. They also allow for limited third party integration or access to external resources. From the main menu select *Tools.*

## Tools for Analysis

Log Lion has a number of built in tools available for analyzing logs which are available from the *Tools* menu. Below is a list of tools and their descriptions.

| Tool | Description | Uses |
| --- | --- | --- |
| [Histogram](tools-histogram.md) | A graph depicting the distribution of data frequencies over fixed time intervals. Only available for log sources that define a time stamp attribute. | Quickly determine the distribution shape of logged entries. To identify skews or gaps in log entries. |
| [Subtotals](tools-subtotals.md) | Uses a regular expression to identify subitems and totals their sums over the log. | Count items of a group. To determine how many times particular items occur in the log. |
| [Statistics](tools-statistics.md) | Uses a regular expression to identify numerical samples. Samples are accumulted to determine the total count, sum, min, max, average, median, standard deviation, skewness, and kurtosis. | Calculate numeric statistics for quantities logged. For example, logging the length of a queue can easily yield the calculated min, max, and average queue size from all samples in the log. |
| [Profiler](tools-profiler.md) | Uses regular expressions to identify and match a pair of log entries inwhich to create a timed interval. Statistics are calculated for each time interval and each identifier with its corresponding statistics are displayed in a table. Only available for log sources that define a time stamp attribute. | To profile the duration of operations. An operation can be a function call, a pair of asynchonous events, or any pair of data that share an identifier for the start and end. |
| [Unmatched Pairs](tools-unmatched-pairs.md) | Uses regular expressions to identify and match a pair of log entries where the second entry is missing. Displays all the first log entries that do not have a matching second log entry. | Find leaked resources, incomplete operations, and dead locked function invocations. |
| [Open Scopes](tools-open-scopes.md) | Finds the last log entries of each navigation context which opened a scope but hasn't closed the scope by the end of the log or set range. Only available for log sources that define a navigation context attribute such as a thread. | Find the last statements of an incomplete thread or operation which is probably hung or dormant. Find two or more threads involved in a dead lock. |
| [Word Counter](tools-word-counter.md) | Splits a column into words and counts how often each one occurs. Unlike Subtotals it looks inside the text, so it works on a free-form message column. | Learn what an unfamiliar log talks about. Find the dominant failure, and the repeated noise worth filtering away. |
| [Scripts](scripts.md) | Runs a Lua script you write over every entry in the log, in order, and shows the table of results it produced. The general case of the tools above. | Answer a question none of the fixed tools cover — correlate entries, track a state machine, group messages that differ only by an identifier. |

## External Tools

External tools are programs, web URIs, or scripts that can be invoked from Log Lion via the *Tools* menu. Data from the current log entry selection as well as the file name can be passed to external tools. You can add or modify tools via the *Tools\Customize...\Tools\* option.

The following **macros** are available to pass to external tools as command arguments:

| Tool | Description | Example Value |
| --- | --- | --- |
| %File | Full identifier to a file including the path and file name. | c:\logs\today.log |
| %FileName | Just the file name without the path. | today.log |
| %Path | Just the path without the file name. | c:\logs\ |
| %Extension | Just the file extension. | .log |
| %FirstTimeStamp | The time stamp of the first selected entry. | 17:24:54.545 |
| %LastTimeStamp | The time stamp of the last selected entry in a range. | 17:24:55.052 |
| %FirstEntryNumber | The line/record number of the first selected entry. | 1644 |
| %LastEntryNumber | The line/record number of the last selected entry in a range. | 1728 |

External tools are convenient was to extend Log Lion. You can execute scripts or programs to analyze log files using your own tools or manage log files such as snip, merge, copy, and archival. You can specify web URIs as convenient methods to lookup information from online resources such as error and event codes.

---

[All documentation](README.md)
