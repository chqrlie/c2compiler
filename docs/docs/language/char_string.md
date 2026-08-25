# Strings and Characters

Strings and characters in C2 work much like they do in C.

## Character literals

Character literals are written like this:

```c
char c = 'a';
char nul = '\0';
```

## String literals

String literals are written like this:

```c
const char* s = "the quick brown fox\n";
```

Note the `const`: a string literal's characters live in read-only memory, so it
can only be pointed to by a `const char*` (or stored in a `const char[]`) — never
by a plain, mutable `char*`.

Adjacent string literals are automatically concatenated, so a long string can be
split across several lines:

```c
const char[] Multi = "the"
    " quick"
    " brown"
    " fox\n";
```

No trailing backslashes are needed to join the lines, unlike in a C macro. For
embedding large blocks of text or code verbatim, see [raw strings](raw_strings.md).

## Escape sequences

C2 supports the same escape sequences as C, inside both character and string
literals:

* `\"`
* `\'`
* `\?`
* `\\`
* `\a` - audible bell
* `\b` - backspace
* `\f` - new page/form feed
* `\n` - newline
* `\r` - carriage return
* `\t` - tab
* `\v` - vertical tab
* `\xXX` - a byte given as a hexadecimal number (eg. `\x12`)
* `\ooo` - a byte given as an octal number (eg. `\177`)
* `\u` plus exactly 4 hex digits - a Unicode code point, encoded into the
  literal as UTF-8
* `\u{` ... `}` - the same, but with a braced, variable-length hex code point
