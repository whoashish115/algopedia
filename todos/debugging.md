**Overflow trap:** `1 << 31` overflows a signed 32-bit `int` (undefined
behavior in C++). If you need the 31st bit or higher, use `1LL << i`
or `1U << i` instead. This single mistake causes a huge fraction of
"weird WA" bugs in bitmask DP once `n` approaches 30+.

Careful: right shift on a negative *signed* integer is
implementation-defined/arithmetic (it typically sign-extends, filling
with `1`s from the left rather than `0`s) — this matters for negative
numbers, but not for the unsigned/non-negative case.