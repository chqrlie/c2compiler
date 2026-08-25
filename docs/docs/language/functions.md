# Functions

Functions declarations always start with the `fn` keyword; otherwise they look very similar
to C. Since there are no forward declarations of any kind in C2, there is just one form
of a function declaration, which is the definition:

```c
public fn i32 main(i32 argc, char** argv) {
    return 0;
}
```

Functions may also have attributes. More information on attributes can be found [here](attributes.md).

## Arrays
In C2 arrays cannot be used as function arguments, so pointers must be used instead. This is done
because in C passing 'int numbers[20]' is not a copy, but a pointer to an array, which is confusing
and could lead to bugs.


## Arguments

### Default arguments
Default arguments are also allowed in C2.

```c
fn void test(i32 a = 10, i32 b = 20) {}
```

### Named arguments
When a function takes several arguments of the same type, a positional call site
gets easy to misread — it's not obvious which `bool` is which:

```c
fn void foo(bool a, bool b, bool c, bool d) { .. }

fn void bar() {
    foo(true, false, true, false);
}
```

Naming the arguments at the call site makes this self-documenting:

```c
foo(a: true, b: false, c: true, d: false);
```

Named arguments still have to appear in the same order they were declared in —
this isn't a way to reorder a call, it's a label the compiler cross-checks
against the parameter at that position, so a typo'd or misplaced name is a
compile error rather than a silently-swapped value:

```c
foo(b: false, a: true, c: true, d: false);
// error: unexpected named argument 'b'
// note: expected argument 'a' instead
```

Combined with __default arguments__, naming also lets a call skip straight to a
later parameter without repeating the defaults in between:

```c
fn void connect(const char* host, i32 port = 80, i32 timeout = 30) { .. }

fn void test() {
    connect("example.com", timeout: 5);  // port keeps its default of 80
}
```


### Function pointer arguments

Function pointer types can be defines inline as function arguments:

```c
fn void my_func(void* arg,
        fn i32(void* arg, bool b) a,
        // even nested
        fn void(void* arg, fn bool(i32, i32), i32 a) b,
        i32 c) {
}
```

