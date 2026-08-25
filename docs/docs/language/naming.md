
# Naming

Unlike many other languages, C2 enforces how the elements of the language are
named — this is a compile error, not a linter suggestion. Unifying casing this way
makes code easier to read across projects, since a name's casing alone already
tells you something about what kind of thing it is. Only the *first* character of
a name is checked, as either upper- or lowercase, depending on the kind of element.

Elements that must start with a *lower*-case character:

* module names
* functions
* variables and function arguments
* local constants (see [Constants vs. variables](#constants-vs-variables) below)
* struct/union members (including sub-structs/unions)

Elements that must start with an *Upper*-case character:

* user-defined types
* enum constants
* global constants (see below)

## Libraries

Since external (C) libraries may require different names, no name checks are
performed on external libraries (or `.c2i` files).

## Constants vs. variables

Since C2 borrows its type syntax from C, it isn't always obvious at a glance
whether a declaration is a *constant* or a *variable* — this matters, because it
decides whether the name must start upper- or lowercase. The rule only applies at
**module (global) scope**: a global declaration counts as a constant, and must
start uppercase, if its type is truly immutable — that is, `const`-qualified all
the way down, not just a `const` pointer to mutable data. Everything else at
global scope is a variable, and must start lowercase:

```c
Point p = { 3, 4 };         // variable

const Point P = { 5, 6 };   // constant: the whole struct value is const
const i32 Max = 5;          // constant: a const scalar

char* cp1 = "foo";          // variable
const char* cp2 = "foo";    // variable: the pointer itself isn't const,
                             // only what it points to
const char* const Cp3 = "foo";  // constant: both the pointer and its target
                                 // are const

const i32[] Numbers = { 1, 2, 3 };  // constant: an array counts as const
                                     // if its element type is const
```

**Local** variables and constants are a special case: regardless of whether their
type is `const`, they must *always* start lowercase. So the very same declaration
that is a valid, uppercase global constant becomes a naming error once it's moved
inside a function body:

```c
const Point P = { 5, 6 };   // OK at module scope

fn void demo() {
    const Point P = { 5, 6 };  // error: a variable name must start
                                // with a lower case character
    const Point p = { 5, 6 };  // OK: local names are always lowercase
}
```

Beyond this single required first letter, it's up to the developer whether to use
CamelCase or UPPERCASE for the rest of a constant's name.

Putting all of the rules above together, in a single valid example (struct/union
members always stay lowercase, regardless of const-ness):

```c
type Struct struct {
    char* a;
    const char* b;
    i32 c;
    const i32 d;

    struct inner {
        i32 x;
    }
}

type Enum enum u8 { Foo, Bar, Faa }

i32 a;
const i32 B = 1;

char* c;
const char* d;
const Struct* e;

char[] f = "abcd";
const char[] G = "efgh";
const Struct[] H = { { nil, nil, 1, 2 }, { nil, nil, 3, 4 } }

fn void test1(i32 arg1, const i32 arg2, const char* arg3) {
    const i32 local1 = 2;
    const char* local2 = "";

    i32[4] local3;
    const i32[] local4 = { 1, 2, 3, 4 }
}
```

## Maximum identifier length

All identifiers (module, variable, function and type names) are limited to 31
characters. This keeps a fully-qualified name (`module_name.item`) within 64
characters — handy for fixed-size buffers and generated C identifiers.

