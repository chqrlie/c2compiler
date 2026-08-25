# Loops + Flow control

C2 keeps C's `for`, `while` and `if` — `do`..`while` is the one loop construct
C2 removes (see below).

## For

For statements are very similar to C:

```c
for (u32 i=0; i<10; i++) printf("%d\n", i);
```

## While

While statements are very similar to C as well:

```c
while (i<10) i++;

while (Point* p = get_point()) { .. }
```

C2 allows a variable declaration to be used directly as the condition, as shown
in the second example: the loop runs as long as the initializing expression
(`get_point()`) doesn't evaluate to zero/`nil`, and `p` is in scope for the loop
body. The same is allowed for `if`, further below.

## Do-while

`do`..`while` has been removed from C2: it was error-prone (the body always runs
at least once, easy to forget), and its main real-world use — expanding to a
single statement inside a macro — doesn't apply in a language without macros.

## If

If statements work just like in C, with or without braces:

```c
if (x < 10 && y >= 10 && ptr != nil) { .. }
```

Just like `while`, the condition of an `if` can also be a variable declaration:

```c
if (Point* p = get_point()) {
    // p is only in scope inside this if (and any else)
}
```

## Label / Goto

Labels and goto exist just like in C.

```c
start:
    i++;
    if (i < 10) goto start;
```

Labels *must* start with a lower-case character.

## Labelled break/continue

To jump out of or continue an outer loop __labelled break/continue__ exists:
```c
outer: for (i32 i=0; i<100; i++) {
  inner: for (i32 j=0; j<100; j++) {
      if (..) break outer;
      if (..) continue outer;
  }
}
```

Note that these labels can also be used for regular goto statements.


## Switch statement

For the *switch* statement, see the dedicated page [Switch statement](switch_statement.md).
