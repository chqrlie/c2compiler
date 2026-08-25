# Printf specifiers

Format specifiers for `printf` are a classic C anti-pattern: getting them right
across 32-bit and 64-bit platforms (`%d` vs `%ld` vs `%lld`, `%zu`, `PRIu64`, ...)
takes real effort and is easy to get subtly wrong. C2 removes that effort by
checking format strings against their arguments at compile time, and by having
the compiler pick the right length modifier for the target itself.

### Base type specifiers
C2 narrows the set of specifiers down to these:

* `%c` - a character
* `%d` - a decimal number (any integer or `bool` type, signed or unsigned)
* `%x`/`%X` - a hexadecimal number
* `%o` - an octal number
* `%b`/`%B` - a binary number (a C2-specific, non-standard extension)
* `%f`/`%e`/`%g`/`%a` (and their uppercase forms `%F`/`%E`/`%G`/`%A`) - a
  floating-point number
* `%p` - a pointer (or function)
* `%n` - number of characters written so far, into an integer pointer
* `%s` - a string (`char*`/`const char*`)
* `%%` - a literal `%`

A single specifier works for every width of a category: `%d` covers __i8__
through __i64__, __u8__ through __u64__, __bool__ and enums, and the compiler
inserts whatever length modifier the target C compiler actually needs (`l` for
anything wider than 32 bits, and switching `%d` to `%u` for unsigned 32/64-bit
types) when it generates the underlying C code. Likewise `%f` (and its floating
variants) works for both __f32__ and __f64__.

Because of that, length modifiers (`h`, `hh`, `l`, `ll`, `z`, `j`, `t`, `L`) and
the C `%i`/`%u` specifiers are not just unnecessary in C2, they're rejected
outright, with a diagnostic pointing at what to use instead:

```c
printf("%ld\n", big);   // error: format length modifier 'l' should be omitted
printf("%i\n", small);  // error: invalid format specifier '%i', should use '%d'
```

Never worry about `%llu`, `%ld` or `PRIu64` again — just write `%d`.

The argument's actual type is checked too, not just its category:

```c
fn void demo(const char* name, i32 count) {
    printf("%s has %d items\n", name, count);  // OK
    printf("%d has %s items\n", name, count);  // error: format '%d' expects
                                                // an integer argument
}
```

### Other options
Besides the base type, the printf specifier format still supports the usual C
options for width, precision and alignment — the full form is:

__%[flags][width][.precision]type__

for example:

```c
printf("%-4s  %06d  %7.3f\n", text, number, float_number);
```

### Attribute
For the compiler to check a format string against its arguments this way, the
function taking it must be marked with the
[printf_format attribute](attributes.md#printf_format).
