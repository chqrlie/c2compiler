
C2 is an _evolution_ of C: this page summarizes the _changes_ it makes and the
design philosophy behind each one.

### Language changes

* No header files

    _consequence_: an import statement, and modules

* No forward declarations

    _philosophy_: define each thing in exactly one place, for faster development

    _consequence_: requires a multi-pass compiler

* Member access is always through the dot operator (`.`), never `->`

    _philosophy_: remove clutter, and one less thing to get wrong when refactoring

* Unified syntax for type definitions

* All global variables are initialized by default

* [Standardized attribute syntax](../language/attributes.md)

* Better control over external libraries

* [Improved operator precedence](../language/operators.md)

* [Only explicit fallthrough in switch cases](../language/switch_statement.md#auto-fallthrough)

* [No arrays as function arguments](../language/functions.md)

* `do` .. `while` removed, since without macros it isn't needed and is error-prone


### New features
C2 also introduces a handful of genuinely new features. None of them break with the
spirit of C — they're mostly syntax cleanup, syntactic sugar, or the removal of a
C anti-pattern:

* [Bit offsets](../language/bit_offsets.md)

* [Type functions](../language/type_functions.md)

* [Modules](../language/modules.md)

* [Built-in build system](../build_system/intro.md)

* [Incremental arrays](../language/variables.md#incrementally-declared-arrays)

* [Switch statement on strings](../language/switch_statement.md#string-switch-statement)

* [Auto-arguments](../language/attributes.md#auto-arguments)

* [Multi-condition case statements](../language/switch_statement.md#multi-condition-case-statements)

* [Sane printf format specifiers](../language/printf_specifiers.md)

* [Compiler plugins](../language/plugins.md)

* [Raw strings](../language/raw_strings.md)

* [Init calls](../language/init_calls.md)

* [Init-list assignment](../language/init_list.md)

* More tooling integration, such as generated dependency and cross-reference files


