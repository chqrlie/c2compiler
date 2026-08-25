## Init calls

An *init call* is syntactic sugar that combines a local variable's *declaration*
with a call that initializes it, using a [type-function](type_functions.md). It
saves writing the variable's name twice — once to declare it, once to pass its
address to the initializing call.

```c
type Point struct {
    i32 x, y;
}

fn void Point.init(Point* p) {
    p.x = p.y = 0;
}

fn void test1() {
    // without an init call
    Point p;
    p.init();

    // equivalent, as an init call
    Point q.init();
}
```

`Type name.func(args)` declares `name` as a variable of type `Type`, then calls
`Type.func(&name, args)` — any non-static type-function works this way, not just
one named `init`, and it can take extra arguments beyond the receiver:

```c
type Foo struct {
    i32 v;
}

fn void Foo.init(Foo* f, i32 v) { f.v = v; }

public fn i32 main() {
    Foo foo.init(1);   // declares foo, then calls Foo.init(&foo, 1)
    return foo.v;
}
```

The parentheses are required, even with no arguments (`Type name.init;` is
rejected as a missing argument list), and the referenced function must be an
instance type-function — a `static` type-function has no receiver to call it
through.

### Restrictions
An init call can only appear where its generated call can meaningfully run as a
separate statement right after the declaration, so it's not allowed:

* on a global variable
* on a `static` local variable
* inside the condition of an `if` or `while`
* in the init-clause of a `for` loop (accepted by the parser, but not yet
  supported by the code generator)

```c
Foo f.init(1);              // error: global variables cannot have an init call

fn void test() {
    static Foo g.init(1);   // error: static local variables cannot have an
                             // init call

    if (Foo h.init(1))      // error: cannot use an init call inside a condition
        return;
}
```
