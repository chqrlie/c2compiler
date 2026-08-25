# Formatting

## Comments

C2 comments work the same way as C's — both forms are available:

```c
// a one-line comment
/* a
   multi-line
   comment
*/
```

## Semicolons

Every statement in C2 ends with a semicolon, __except__ one that itself ends
with a __right-hand brace__ — a type/function/struct definition, or a statement
whose last token is an initializer list. Trailing attributes never change this
rule; adding a semicolon where one isn't expected is a syntax error, not just
redundant.

```c
type Point struct {   // no semicolon: ends in '}'
    i32 x, y;
}

fn void reset(Point* p) {
    *p = { 0, 0 }      // no semicolon: statement ends in '}'
}

i32 total = 1 + 2;    // semicolon required: ends in an expression, not '}'
```

This means that in C2, you'll never see `};`. It's easier on the eyes ;)
