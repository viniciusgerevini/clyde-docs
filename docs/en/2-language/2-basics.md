<!--
nav_max: 2
custom_page_class: lang_ref
-->
# Basics

## Comments

To ignore a line you can use `--`.

```
-- this line is ignored
this line isn't
```
Output:

```javascript
// get content
{ type: "line", text: "this line isn't" }
```

## Dialogue line

Each line becomes a dialogue line:

```
This is a simple text!
This is another line.
```

Output:
```javascript
// get content
{ type: 'line', text: 'This is a simple text!' }

// get content
{ type: 'line', text: 'This is another line.' }
```

## Grouping lines

If you want to group multiple lines in one call, you just need to indent the subsequent lines. You can choose to use spaces or tabs (or even both, however, I don't recommend that):

```
This is the first dialogue line.
    This is still the first dialogue line.
But this is the second line.
```

Output:
```javascript
// get content
{ type: 'line', text: 'This is the first dialogue line. This is still the first dialogue line.' }

// get content
{ type: 'line', text: 'But this is the second line.' }
```

## Speaker

Use `:` to set a line speaker. Anything from the beginning of the line to `:`(colon) is used as speaker.

```
Hagrid: Harry, yer a wizard.
Harry Potter: I'm a what?
```

Output:
```javascript
// get content
{ type: 'line', speaker: 'Hagrid', text: 'Harry, yer a wizard.' }

// get content
{ type: 'line', speaker: 'Harry Potter', text: "I'm a what?" }
```

When defining multiple lines with same speaker, you can set the same speaker for all lines
by indenting them after the speaker:

```
Vinny:
    Multiple lines can be set with same speaker.
    You just need to indent them after the speaker line.
    Grouping lines also works
        by indenting the line further.
```

Output:
```javascript
// get content
{ type: 'line', speaker: 'Vinny', text: 'Multiple lines can be set with same speaker.' }
// get content
{ type: 'line', speaker: 'Vinny', text: 'You just need to indent them after the speaker line.' }
// get content
{ type: 'line', speaker: 'Vinny', text: 'Grouping lines also works by indenting the line further.' }
```

## Line ID

Use `$` + `[A-Za-z0-9_]` to set a line id:

```
Hagrid: Harry, yer a wizard. $line001
Harry Potter: I'm a what? $line_02
```

Output:
```javascript
// get content
{ type: 'line', id: 'line001', speaker: 'Hagrid', text: 'Harry, yer a wizard.' }

// get content
{ type: 'line', id: 'line_02', speaker: 'Harry Potter', text: "I'm a what?" }
```

IDs are useful for localisation, where you can organise your translations in files with key values (json, csv) and use them to replace dialogue lines with their translated equivalents.

### ID suffixes

Append `&` + `[A-Za-z0-9_]` to a line id to set suffixes.

Id Suffixes aim to improve translations and dynamic lines. They allow you to use
different keys from a dictionary based on values from variables in runtime.

Here is an example. For the given dialogue:
```
Hello, sister! $line001&player_pronoun
```

If the variable `player_pronoun` is set as `F`, the translation lookup will happen in this order: `line001&F`, `line001` and then it falls back to the default line. Multiple suffixes can be set by chaining the variables: `$line001&variable_1&variable_2`.

This can also be used to simplify dialogue files. If you have a dictionary like this:
```
LINE_001;Hello, friend.
LINE_001&F;Hello, sister.
LINE_001&M;Hello, brother.
```
You could change your dialogue from this:
```
Hello, sister.  $LINE_001 { when pronoun is "F" }
Hello, brother. $LINE_002 { when pronoun is "M" }
Hello, friend.  $LINE_003 { when not pronoun }
```
To this:
```
Hello, sister. $LINE_001&pronoun
```

## Tags

Use `#` + `[A-Za-z0-9_\-\.]` to set line tags:

```
WHAT DID YOU DO! #yelling #scared
```

Output:
```javascript
// get content
{ type: 'line', text: 'WHAT DID YOU DO!', tags: ['yelling', 'scared'] }
```

Tags are useful metadata that can be used as you wish. Probably the most obvious usage would be changing dialogue bubble pictures to convey different emotions. (happy, sad, angry)

## Escaping characters

If you need to use special characters in your dialogue, you can scape them by using `\` or surrounding your text with quotes:

```
It will cost you \$100.
"This is an example: ##"
'This is another example: ##'
"You can even escape \" inside \"\""
```

Output:
```javascript
// get content
{ type: 'line', text: 'It will cost you $100.'}

// get content
{ type: 'line', text: 'This is an example: ##'}

// get content
{ type: 'line', text: 'This is another example: ##'}

// get content
{ type: 'line', text: 'You can even escape " inside ""'}
```

