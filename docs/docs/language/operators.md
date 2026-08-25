## Operator precedence

Operator precedence in C2 differs from C/C++. Here are all precedence levels in
C2, from highest (1) to lowest (11):

1. `()`, `[]`, `.`, postfix `++` and `--`
2. prefix `-`, `~`, prefix `*`, `&`, prefix `++` and `--`
3. infix `*`, `/`, `%`
4. `<<`, `>>`
5. `^`, `|`, infix `&`
6. `+`, infix `-`
7. `==`, `!=`, `>=`, `<=`, `>`, `<`
8. `&&`, `||`
9. ternary `?:`
10. `=`, `*=`, `/=`, `%=`, `+=`, `-=`, `<<=`, `>>=`, `&=`, `^=`, `|=`
11. `,`

The main difference from C: bitwise operators and shifts now bind *tighter* than
addition/subtraction, and tighter than the relational operators too. There is
also no difference in precedence between `&&`/`||`, between the three bitwise
operators `&`/`^`/`|`, or between the six relational/equality operators.

This fixes a real, long-standing footgun in C, where `a & b == c` silently means
`a & (b == c)` — almost never what anyone actually wants. It also makes shifts
behave the way most people already assume they do.

### The compiler won't guess for you

Simply changing the precedence table would create a *different* footgun: code
that looks the same as C, but silently means something else. So C2 takes a
stricter approach: whenever two directly-combined operators would be grouped
differently under this table than they would in C, or the combination is one
that's easy to misread regardless (chained comparisons), the compiler doesn't
pick an interpretation for you — it's a compile error, and you have to add
parentheses to say what you mean.

```c
i32 r = a + b >> c + d;
// error: operators '+' and '>>' do not combine without parentheses

i32 r = (a + b) >> (c + d);   // OK - say what you mean
```

The same happens for the other classic ambiguous combinations:

```c
i32 r  = a & b == c;    // error: operators '==' and '&' do not combine
i32 r  = (a & b) == c;  // OK

bool r = x || y && z;       // error: operators '&&' and '||' do not combine
bool r = (x || y) && z;     // OK

bool r = a > b == c < d;      // error: operators '==' and '>' do not combine
bool r = ((a > b) == c) < d;  // OK

i32 r = a | b ^ c & d;        // error: operators '^' and '|' do not combine
i32 r = (a | b) ^ (c & d);    // OK
```

Chained relational/equality operators always need parentheses, even when
repeating the very same operator, since a chain like `a == b == c` reads
plausibly as "are all three equal" but doesn't mean that in any C-family
language:

```c
bool r = a == b == c;    // error: operators '==' and '==' do not combine
bool r = (a == b) == c;  // OK, but almost certainly not what you meant;
                          // (a == b) && (b == c) usually is
```

Operators still combine freely without parentheses when doing so can't change
meaning — chaining the *same* operator, or operators that C and C2 already agree
on the relative order of:

```c
i32 r  = a + b - c;      // OK: + and - already agree between C and C2
i32 r  = a & b & c;      // OK: same operator, chains left-to-right
i32 r  = a << b << c;    // OK: same operator
bool r = x && y && z;    // OK: same operator
i32 r  = a + b * c;      // OK: * binds tighter than +, same as in C
i32 r  = a & b * c;      // OK: * binds tighter than &, same as in C
```

As in C, `,` and the assignment operators are the lowest-precedence and are
left/right-associative as usual; explicit parentheses are always allowed even
where not required, and remain good practice for anything non-trivial.
