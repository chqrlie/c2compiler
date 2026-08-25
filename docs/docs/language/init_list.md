## Init-list assignment

Init-list assignment is a syntactic-sugar feature.

In C and C2, *init-lists* are used during *initialization*, like:

```c
type Point struct {
    i32 x, y;
}

Point p1 = { 1, 2 }
Point p2 = { .x=1, .y=2 }
```

To assign a new value to an already-declared point, C requires setting each
member individually:

```c
fn void test1(Point p) {
    p.x = 1;
    p.y = 2;
}
```

Since an init-list is unambiguous and more compact, C2 also allows it directly on
the right-hand side of an *assignment*:

```c
fn void test1(Point p) {
    p = { 1, 2 }
    p = { .x=3, .y=4 }
}
```

Note there's no semicolon after either statement above — like any other statement
ending in a right-hand brace, it doesn't take one (see [Formatting](formatting.md));
adding one is a syntax error here, since the parser would then expect a further
statement to start right after it.

This isn't allowed on a sub-struct member specifically — `f.b = { .. }` for a
nested `struct b { .. }` member `b` still needs to be assigned member-by-member.

When a `Type` is expected *by value* (not by pointer), the same init-list syntax
can be used directly as a call argument, without naming a temporary variable
first:

```c
fn void set(Point p) {
    // ...
}

fn void test() {
    set({ 3, 4 });
}
```

This only works for by-value parameters — passing an init-list where a pointer is
expected (eg. `Point*`) is rejected, since there's no addressable value for the
pointer to point to.
