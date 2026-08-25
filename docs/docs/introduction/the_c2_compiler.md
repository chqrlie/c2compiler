
Every language needs a _compiler_ to turn source code into something that runs. For
C2 that compiler is __c2c__, the C2 Compiler.

### Implementation

Since 2024, c2c has been written in C2 itself, making it _self-hosting_. Building it
therefore requires a _bootstrap_ step: a small C-source bootstrap compiler builds the
real c2c, which then rebuilds itself. See the
[GitHub repository](http://github.com/c2lang/c2compiler) for build instructions.

### Performance

c2c typically parses somewhere between 3 and 5 million lines of code per second,
depending on the hardware. Semantic analysis of that same code runs at a comparable
speed — usually noticeably faster than parsing, since it reuses the AST built during
parsing instead of re-reading source text.

c2c itself is around 250 files and 57,000 lines of C2 code. Parsing and analysing
that with c2c (via `c2c -t c2c`, which prints timing information) on a mac-mini M4 takes:

```bash
parsing took 11677 usec
analysis took 6835 usec
```

So it is _fast_ — fast enough that, during day-to-day development, diagnostics feel
instantaneous even on the compiler's own, fairly large, codebase.

