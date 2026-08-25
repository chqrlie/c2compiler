
# Switch statement

The switch statement is similar to C's, except for the changes below:

* no auto-fallthrough
* auto-scoping per case
* the `default` label, if present, must come last

## Auto-fallthrough

Unexpected fallthrough is a big source of bugs in C, so C2 removes the implicit
behavior: every case must end in one of *break*, *fallthrough*, *return*,
*continue*, or a call to a `noreturn` function. `fallthrough` may only appear at
the top level of a case body, not nested in a sub-statement (eg.
`if (x) { fallthrough; }` is not allowed). The one exception is an *empty* case
— falling through from an empty case into the next one is still implicit, since
there's nothing else it could reasonably mean.

```c
switch (i) {
case 0:          // error: last statement of a case must be one of
                 // break/fallthrough/continue/return
case 2:
    fallthrough; // explicit: falls through into case 3
case 3:          // implicit fallthrough allowed: this case is empty
case 4:
    break;
default:
    fallthrough; // error: not allowed in the last case
}
```

## Case auto-scope

Each `case` gets its own scope automatically, so declarations in one case can't
clash with same-named declarations in another — no need for the extra `{ }` a C
programmer would have to add to get the same effect:

```c
switch (i) {
case 1:
    i32 a = 10;      // scoped to this case
    return calc(a);
case 2:
    i32 a = 20;      // OK: a different scope, no clash with the 'a' above
    return a;
}
```

## Default last

The `default` label, if present, must be the last one in the switch, to keep
switch statements uniform to read.

## Automatic enum scoping

C2 places enum constants inside their enum type's own namespace, so they're
normally reached through it:

```c
State s = State.Begin;
State s2 = Begin;   // shorthand: also allowed, but only where the target
                     // type is already known, as in this initialization
```

A `switch` on an enum-typed value is one of those places, so its `case` labels
never need the `State.` prefix:

```c
fn void demo(State s) {
    switch (s) {
    case Begin:
        break;
    case Middle:
        break;
    case End:
        break;
    }
}
```

## Multi-condition case statements

C2 adds a feature C doesn't have: multiple conditions in a single `case`. This
reduces how often *fallthrough* is needed and reads more clearly. It's only
available when switching on an *enum* type.

Rules for multi-condition cases:

* A multi-condition case has one or more single conditions, comma-separated
* A single condition is either an *identifier*, or a range written
  *identifier* `...` *identifier*
* `default` can't be combined with other conditions in the same label
* This also works with [incremental enums](variables.md)

Example:

```c
type Foo enum u8 { A, B, C, D, E, F, G, H, I }

fn void test(Foo f) {
    switch (f) {
    case A:            // single case
        break;
    case B...C, H:     // a range plus a single case
        break;
    case D...F, G, I:  // a range plus two single cases
        break;
    }
}
```

## String switch statement

C2 also lets a `switch` operate directly on strings:

```c
fn i32 handleCommand(const char* cmd) {
    switch (cmd) {
    case nil:
        return 1;
    case "":
        break;
    case "start":
        return 2;
    case "stop":
    case "move":
        break;
    default:
        return 10;
    }
    return 0;
}
```

Rules for switching on strings:

* each case condition must be `nil` or a string literal
* a case string can be at most 255 bytes long, including its terminating nul
* case values (including `nil`) must all be distinct — no duplicates

## Declaration in the condition

Just like `if` and `while` (see [Flow control](flow_control.md)), a `switch`'s
condition can be a variable declaration, with the switch running over its
initial value:

```c
switch (i32 a = getNum()) {
    default:
        break;
}
```
