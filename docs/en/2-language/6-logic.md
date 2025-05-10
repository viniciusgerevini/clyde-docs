<!--
custom_page_class: lang_ref
nav_max: 2
-->
# Logic, conditions, variables and events

Now that you know the basics, you can step up your branching game by using variables and conditions with logic blocks, defined by `{` and `}`.

Logic blocks may contain:


**Logical operators:**: Equals `==` or `is`, Not equals: `!=` or `isnt`, Not: `!` or `not`, Greater, Less, etc: `>`, `<`, `>=`, `<=`.

**Math operators**: sum `+`, subtract `-`,  multiply `*`, divide `/`,  power `^`,  modulo/remainder `%`.

**Assignment operators**: assign `=`, sum `+=`, subtract `-=`, multiply `*=`, divide `/=`, power `^=`, modulo `%=` and init `?=`.

**Literals**: Number (`100`, `1.5`), String (`"some text"`, `'some text'`), Boolean (`true`, `false`), Null (`null`).

**Keywords**: `set`, `trigger`, `when`.

There are three types of logic blocks: assignments, conditions, and triggers.

## Assignments

Besides setting variables using the interpreter method, you can set variables internally with assignment blocks. Assignment blocks need to start with the `set` keyword. Here are some examples:

```
-- standalone
{ set is_happy = true }

-- after line
some text here { set is_happy = true}

-- before line
{ set is_happy = true} some text here

-- both sides
{ set something = 1 } some text here { set something += 1 }

-- multiple assignments
some text here { set is_happy = true, is_naughty = false, a = b, b = 2 }
```

Regardless of the position, the assignment will always be executed when the line is returned.

The initializer assigment `?=` can be used when you wish to only set the variable if it's still unset.

```
{ set count ?= 1 } -- count will be set to 1

{ set count ?= 2 } -- count will remain 1, as it's already set
```

## Conditions

Conditions are used to control which lines should be shown. They do not require any special keyword, but you can optionally use `when` to explicitly show that the block is a condition. Examples:

```
{ set something = true }
{ set gender = "female" }

-- after line
some text here { something }

-- before line
{ not something } some text here

-- with when keyword
some text here { when not something }

-- both sides
{ something } some text here { something_else }

-- complex conditions
some text here { something and a == b or b >= c and not v }
some text here { something && a == b || b >= c && ! v }

-- with options
+ { not something } options a
* { hp < 50 } options b
* options c { when hp == 30 }

-- with variations
( cycle
    - Hello, sister. { when gender == "female" }
    - Hello, brother. { when gender == "male" }
)

-- with multiple lines
{ something }
    one line
    another line
    yet another line

```

As you may have noticed, you can't mix assignments and conditions in the same block. However, you can define multiple blocks in the same line, like this:

```
say something { when not something } { set something = true }
```

Just be aware that order matters. i.e.

```
-- Condition is checked before assignment. This line won't be returned.
say something { when something } { set something = true }

-- Condition is checked after assignment. This line will be returned.
say something { set something = true } { when something }

-- Condition is checked before assignment.  This line won't be returned.
{ when something }  say something { set something = true }

-- Condition is checked after assignment. This line will be returned.
{ set something = true } say something { when something }

```

## Triggers

There may be cases where you'd want your game to be notified that something happened in your dialogue. There are two ways to achieve that: by triggering events or by observing variable changes.

You can trigger events using the `trigger` block.

```
* allow { trigger allowed }
* deny { trigger denied }
```

Events also accept parameters, like this:
```
-- you can pass literal arguments
{ trigger my_event("some text", 1, true) }

-- you can pass variables
{ trigger my_event(this_is_a_variable) }

-- and even expressions
{ trigger my_event(some_important_count + something_else) }
```


Your interpreter will expose a way to listen to these events. This will vary depending on implementation. To use the JavaScript interpreter as example:

```javascript
dialogue.on(Clyde.EVENT_TRIGGERED, (eventName) => {
    if (eventName === 'allowed') {
        console.log("do something");
    }
});
```

You can also listen to variables changes, like this:

```javascript
dialogue.on(Clyde.VARIABLE_CHANGED, (name, value, previousValue) => {
    if (name === 'hp' && previousValue < value) {
        console.log("damage taken");
    }
});
```

## Using variables in text

You can use values from variables in your text by referencing them with `%` `%`.

```
{ set playerName = 'Vini' }
Hello, %playerName%! Long time no see.
```

This should print `Hello, Vini! Long time no see!`

This can be used with variables defined internally or externally.

## External variables

External variables can be accessed using the `@` prefix. They are saved outside the dialogue data and the interpreter should provide callbacks to fetch and update these variables.

They are useful when dealing with data that belongs to your game and shouldn't be persisted within the dialogue data. They can be set and used in the dialogue like this:

```
-- set
{ set @hp = 10 }

-- use
{ @hp > 10 }

-- interpolation
Hello, %@player_name%!
```

Here is an example on how the callbacks to fetch and update the data can be used:

```typescript
dialogue.onExternalVariableFetch((name: string): any => {
    return my_persistence_object[name];
});

dialogue.onExternalVariableUpdate((name: string, value: any): void => {
    my_persistence_object[name] = value;
});

```

## Special variables

### OPTIONS_COUNT

`OPTIONS_COUNT` contains the number of options available in an option list.

For example:

```
{ set hp = 50, mp = 30 }

You have %OPTIONS_COUNT% available
    + { hp < 30 } Give me health!
    + { mp == 100 } I'm fully loaded!
    + { mp < 50 } Give me mana!
    + { OPTIONS_COUNT > 1 } I'm fine. Thanks!
Ok
```
In the example above, due to the conditional options, `OPTIONS_COUNT` is 2. If mp were between 50 and 99, `OPTIONS_COUNT` would be 1, making the last condition false, and skipping all options altogether.
