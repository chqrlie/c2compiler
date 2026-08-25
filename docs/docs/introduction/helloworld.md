
Enough philosophy — let's talk code. Let's talk Hello World!

`hello.c2`
```c
module hello_world;

import stdio as io;

public fn i32 main(i32 argc, char** argv) {
    io.printf("Hello World!\n");
    return 0;
}
```

Spot the __six__ differences from C. Scroll down for the answer.

.

.

.

.

.

.

.

.

Here they are:

1. the __module__ keyword — see [modules](../language/modules.md)
2. __import__ replaces `#include` — see [importing modules](../language/modules.md#importing-modules)
3. the __fn__ keyword precedes every function
4. __i32__ instead of `int` — in C2 you always spell out the size
5. `char** argv` instead of `char* argv[]` — array types aren't allowed as function arguments, only pointers
6. __io.printf__ — symbols live inside modules, and are reached through them

