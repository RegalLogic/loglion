# Boolean Expression Language (BEL)

Boolean Expression Language (BEL) is a language for expressing boolean queries. BEL in Log Lion is used for filtering or searching. A BEL expression is evaluated against a set of entries to selectively include or exclude each entry into the result set.

An expression always evaluates to a *true* or *false* value against a give entry which indicates whether it should be included or excluded from the result set. Basic expressions are just a list of words or a phrase enclosed in double quotes. Complex expressions may reference particular attributes of the log entry, contain aribtrarily nested sub-expressions, or use a variety of matching operators such as regular expressions.

## Basic Expressions

The basic expression syntax is similar to web searches. You may specify a list of words to find, an exact phrase that can contain spaces and match case, or change how the words are combined. These expressions will do a substring search against the default *Message* attribute for the log source.

### Words

A list of words seprated by white space will search the default *Message* attribute of log entries. Word comparisons are case insensitive (*error* will match "error" or "ERROR"). The *Message* attribute must contain all the words listed for the expression to evaluate to true, but those words can be found in any order. Find all the entries containing *error* and *notfound*.

```
error notfound
```

A word can be excluded from the list of matches by prefixing a *-*. Find all the entries containing *error* but not *fault*:

```
error -fault
```

### Phrases

Enclose text with spaces or symbols in double quotes to find that exact phrase. A list of phrases seprated by white space will search the default *Message* attribute of log entries. Phrase comparisons are case sensitive (*error* will only match "error" not "ERROR"). The *Message* attribute must contain all the phrases listed for the expression to evaluate to true, but those phrases can be found in any order.

```
"error"
"Find this exact phrase."
```

### Combinations

By default, a list of words and phrases use the *AND* logical operator which requires all of the items to match in a log entry to evaluate to true. *OR* may also be used to match either item but not require both.

```
error AND warning
```

is equivelant to

```
error warning
```

`error OR  "Warning"`   ## Complex Expressions

Expressions can go beyond simple string matches in the *Message* column. BEL expressions support complex logical opeator combinations, a variety of comparison operators such as regular expressions, arithmetic operators for basic math calculations, four different literal types, variables to reference any attribute of a log entry, and user specified variables that make it convenient to reuse and apply expressions.

### Logical Operators

Logical operators always evaluate to a boolean *true* or *false* value. They allow for the combination and manipulation of boolean expressions. These are binary operators that require a boolean expression on the left and right sides of the logical operator, except for the unary NOT operator whose operand must follow on the right side.

| Keyword | Alternative | Description |
| --- | --- | --- |
| AND | && | Logical conjunction binary operator. Short-circuited; that is, if the left operand evaluates to false then the right operand is not evaluated. |
| and | && | Logical conjunction binary operator. Short-circuited; that is, if the left operand evaluates to false then the right operand is not evaluated. |
| OR | \|\| | Logical disjunctive binary operator. Short-circuited; that is, if the left operand evaluates to true then the right operand is not evaluated. |
|  or |  \|\| |  Logical disjunctive binary operator. Short-circuited; that is, if the left operand evaluates to true then the right operand is not evaluated. |
|  NOT |  ! |  Logical negation unary operator. |
| not | ! | Logical negation unary operator. |

`error AND NOT ("false positives" || system)`  #### Unary AND

The AND operator may also preceede a BEL expression to effect the default logical operation when combining with other BEL expressions. Normally, if two BEL expressions were to be combined the logical OR operation would be employed. However, this is sometimes undesired especially if an expression is going to be used for exclusion. To get a logical AND operation employed, begin the expression with the unary AND operator. If the expression is not combined with others and is therefore evaluated singularly, then the unary AND operator has no effect.

`AND NOT ("false positives" || system)`

### Comparison Operators

Comparison operators always evaluate to a boolean *true* or *false* value. They allow for the comparison of a log entry's attribute to a literal value or user variable. There are many different comparison operators to afford flexibility and each have different performance characteristics. In general, exact matches are the fastest operators and regular expressions (the most general) are the slowest. Many of the operators have an alternative form that is more compact.

| Keyword |  Alternative |  Description |
| --- | --- | --- |
| eq |  == |  Equals. |
| ne |  != |  Does not equal. |
| ne |  <> |  Does not equal. |
| le |  <= |  Less than or equal to. |
| lt |  < |  Less than. |
|  ge |  >= |  Greater than or equal to. |
|  gt |  > |  Greater than. |
|  contains |  ~ |  Contains substring. |
|   |  !~ |  Does not contain substring. |
|  equals |  == |  Equals. |
|  iequals |   |  Case incensitive equals. |
|  regex_search |  =~ |  Regular expression search. |
|  iregex_search |   |  Case incensitive regular expression search. |
|  regex_match |   |  Regular expression match. |
|  iregex_match |   |  Case incensitive regular expression match. |
|  starts_with |   |  Starts with substring. |
|  istarts_with |   |  Case incensitive starts with substring. |
|  ends_with |   |  Ends with substring. |
|  iends_with |   |  Case incensitive ends with substring. |

#### Regular Expressions

All regular expressions are based on the ECMAScript standard which has a similar syntax to Perl. Special symbols for regular expressions are *.[{()\\*+?|^$ and have special meaning.*

##### Matching Multiple Values

Regular expressions can be used with user variables to make it convenient to specify single or multiple values. Grouping with the alteration pipe operator *|* can be used to delimit a list of IDs to match. For example:

`$(Event ID) =~ %(Event IDs)`  will allow you to set *%(Event IDs)* to match a single value such as *26048* or a list of ids such as *(26048|100|9013|9010|8224)*

### Arithmetic Operators

Arithmetic operators allow for simple mathematical calculations.

| Operator | Description |
| --- | --- |
| + | Add |
| - | Subtract |
| * | Multiply |
| / | Divide |

### Literals

Literals are fixed values that may be specified in an expression. Four different types are provided.

| Type | Description |
| --- | --- |
| String | A sequence of characters or punctuation enclosed in double quotes such as *"The quick brown fox jumps over the lazy dog"*. |
| Boolean | One of the values *true* or *false*. |
| Integer | Set of whole numbers including negatives such as *-3, -2, -1, 0, 1, 2, 3*. |
| Real | Decimal representation of a quantity along a continuum such as *-3.14159, 0.0, 1.1, 2, 3.14159*. |

### Variable Identifiers

Variables are named identifiers that hold a particular value. The identifiers are unique within their respective domain. Sigils, magic symbols, are prefixed to denote a variable and its kind. *$* is used to denote variables that reference attributes of a log entry. *%* is used to denote user defined variables that hold a constant value. All variables are scalars and cannot hold multiple values. To match multiple values at once use regular expressions.

| Sigil + Identifier | Type | Description |
| --- | --- | --- |
| $Name | Attribute word identifier | Attributes are scoped to the log source and values are specific to a particular log entry. Values depends on the entry currently being evaluated. |
| $(Name with spaces) | Attribute phrase identifier | Attributes are scoped to the log source and values are specific to a particular log entry. Values depends on the entry currently being evaluated. |
| %Name | User variable word identifier | User variables have global scope and are constant regardless of the entry being evaluated. User specifies the value. |
| %(Name with spaces) | User variable phrase identifier | User variables have global scope and are constant regardless of the entry being evaluated. User specifies the value. |

### Attribute Variables

All log entries in a log source have a set of attributes. Attributes are referenced in a BEL filter expression by prefixing a *$* followed by its name if it is a single word. If the attribute label contains spaces it must be enclosed in parenthesis such as *$(My Name)*.

`$Category  contains  "Server"`

`$(Event ID) ==  26048`

When a filter or search operation is in progress, the values of each attribute in the filter expression changes depending on the current log entry that is being evaluated.

### User Variables

User variables are declared by the using them in a filter expression and values are set in the UI. User variables are referenced in a BEL filter expression by prefixing a *%* followed by its name if it is a single word. If the attribute label contains spaces it must be enclosed in parenthesis such as *%(My Name)*. *%* may appear in an expression or within a string literal.

`$Category  contains  "%category"`

`$(Event ID) == %(Event ID)`

When a filter or search operation is in progress, the values of each user variable in the filter expression never changes. The value is constant regardless of the current log entry that is being evaluated.

#### Environment Variables

Operating system environment variables can also be reference as constant user variables. These are denoted with a prefixed namespace of env. For example %env.COMPUTERNAME references the OS environment variable COMPUTERNAME that contains the local machine name the filter is being executed on.

`$Message  ~ %env.COMPUTERNAME`  ### Comments

Comments are remarks embedded in the BEL expression and generally used to explain the purpose of statements. Any remarks inside a comment will be ignored by the BEL interpreter.

There are two types of comments supported, *//* and */*...*/*. When *//* is encountered, the following contents until end-of-line is ignored. When */** is encountered, the following contents until **/* is encountered is ingored (/* */ paires may therefore contain line breaks).

`//Comment until end-of-line is ignored

/*Enclosed comment is ignored*/`

Many expressions will begin with a title within a comment.

---

[All documentation](README.md)
