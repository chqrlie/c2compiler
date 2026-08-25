# Bit offsets

__Bit offsets__ are a feature new to C2 — don't confuse them with __bit fields__,
which are struct members that are only *x* bits wide, used to pack data tightly
into memory.

Bit offsets instead let you pull a range of bits directly out of an unsigned
integer value, which is convenient in code that works with hardware registers,
wire protocols, or other bit-packed data.

The syntax is `value[<highest bit>:<lowest bit>]`, so the resulting width is
`highest - lowest + 1`. This mirrors how hardware datasheets typically describe
bit ranges within a register.

```c
fn void demo() {
    u32 value = 0x1234;
    u8 a = value[15:8]; // 0x12: the high byte
    u8 b = value[11:4]; // 0x23
    u8 c = value[4:0];  // the lowest 5 bits

    // The statements below are equivalent: C style, then C2 style
    i32 counter1 = (value >> 10) & 0x1F;
    i32 counter2 = value[14:10];
}
```

## Rules

* The base value must have an *unsigned* integer type (`u8`/`u16`/`u32`/`u64`, or
  a `type` alias of one) — signed integers, `bool`, pointers and functions are all
  rejected with `bitoffsets are only allowed on unsigned integer type`.
* The two indices must themselves be integers; the high index may not be lower
  than the low one (`left bitoffset index is smaller than right index`), and
  neither may be negative or exceed the base value's bit width (`bitoffset index
  value 'N' too large for type 'uN'`).
* A bit offset is a read-only expression: it can't appear on the left-hand side
  of an assignment (`bitoffset cannot be used as left hand side expression`).
* When both indices are compile-time constants, the result gets the smallest
  standard integer type wide enough to hold the range. When either index is only
  known at run time, the compiler can't narrow the type and the result keeps the
  base value's own width instead — which may then need an explicit narrowing
  conversion at the assignment, same as any other implicit narrowing:

```c
fn void demo2(u32 value, u8 lo, u8 hi) {
    u8 fixed   = value[15:8];     // OK: width (8 bits) is known at compile time
    u32 dynamic = value[hi:lo];   // OK: kept at the base type's width, u32
    u8 narrowed = value[hi:lo];   // error: implicit conversion loses integer
                                  // precision: 'u32' to 'u8'
}
```

Implicit narrowing conversions and constant range checks apply here exactly like
they do elsewhere in C2:

```c
u32 value1 = 0xffff;
u8 a = value1[15:0];   // error: implicit conversion loses integer precision:
                        // 'u16' to 'u8'

const u32 Value2 = 0x1234;
i8 b = Value2[6:0] + 100;  // error: constant value 152 out-of-bounds for type
                            // 'i8', range [-128, 127]
```
