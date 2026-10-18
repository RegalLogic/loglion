# Filter Samples

The following samples describe many of the common features available in the Boolean Expression Language. These samples are created automatically when no other filter groups are defined in the Filter Settings pane.

## Word Search

```
// Word Search searches the default Message
// column for this particluar set of words.
Error Warning
//Comment until end-of-line is ignored  

/*Enclosed comment across multiple lines
is ignored*/
    
```

## Phrase Search

```
// Phrase Search
// Use double quotes to qualify an exact phrase
"[Error]"
```

## Word Search w/OR

```
// Word Search w/OR
Error OR Warning
    
```

## Word Search w/AND

```
// Word Search w/AND (AND is the default and therefore unnecessary)
Error AND Warning
    
```

## Column Search

```
// Column Search
/* $ is a sigil that indicates a constant
   variable.  Each column/attribute can be
   specified this way.
*/
$Message contains "Error"
 
```

## Column Search - case insensitive

```
// Column Search - case insensitive
/* $ is a sigil that indicates a constant
   variable.  Each column/attribute can be
   specified this way.
*/
$Message icontains "error"
    
```

## Regular Expression

```
// Regular Expression

$Message regex_search "Error(s)?"
//$Message iregex_search "error(s)?"
//$Message regex_match "Error(s)?"
//$Message iregex_match "error(s)?"
    
```

## Boolean OR

```
// Boolean OR
$Message contains "[Error]" OR
$Message contains "[Warning]"
    
```

## Boolean AND

```
// Boolean AND
$Message contains "[Error]" AND
$Level contains "Error"
    
```

## Variables

```
// Variables
// % is a sigil for user variables.
// Specify values in the "Variables" tab
// below.
$Message contains %callid OR
$Message contains "callid=%callid" //Can be used within a string literal
    
```

## Environment Variables

```
// Environment Variables
// %env is a namespace for environment variables.
$Message contains %env.COMPUTERNAME
    
```

## Variables phrases

```
// Variables phrases
// Enclose names with whitespace in ()
$(Interaction ID) == %(interaction id)
    
```

---

[All documentation](README.md)
