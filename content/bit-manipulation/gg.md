
---
title: Base
---

## Binary Numbers
| Decimal | Binary |
| ------: | :----: |
|       0 | `0000` |
|       1 | `0001` |
|       2 | `0010` |
|       3 | `0011` |
|       4 | `0100` |
|       5 | `0101` |
|       6 | `0110` |
|       7 | `0111` |
|       8 | `1000` |
|       9 | `1001` |
|      10 | `1010` |
|      11 | `1011` |
|      12 | `1100` |
|      13 | `1101` |
|      14 | `1110` |
|      15 | `1111` |

> **Memorize the binary representation of `0–15`.** It is one of the most fundamental building blocks of bit manipulation and will make working with masks, shifts, and bitwise operations much more intuitive.


## Operations
| Operation                     | Formula                                       | Meaning / Use                     |
| ----------------------------- | --------------------------------------------- | --------------------------------- |
| Bitwise AND                   | `a & b`                                       | intersection of bits              |
| Bitwise OR                    | `a \| b`                                      | union of bits                     |
| Bitwise XOR                   | `a ^ b`                                       | bits that differ                  |
| Bitwise NOT                   | `~x`                                          | invert every bit                  |
| Bitwise NAND                  | `~(a & b)`                                    | NOT of AND                        |
| Bitwise NOR                   | `~(a \| b)`                                   | NOT of OR                         |
| Bitwise XNOR                  | `~(a ^ b)`                                    | bits that are equal               |
| Left shift                    | `n << k`                                      | multiply by `2^k`                 |
| Right shift                   | `n >> k`                                      | divide by `2^k` (floor)           |
| Two’s complement              | `-x = ~x + 1`                                 | store negative number             |
| Check odd                     | `(n & 1) == 1`                                | last bit is `1`                   |
| Check even                    | `(n & 1) == 0`                                | last bit is `0`                   |
| Bit mask for `i`              | `1 << i`                                      | only `i`-th bit set               |
| Check `i`-th bit              | `(x & (1 << i)) != 0`                         | test if bit is set                |
| Check bit `i` (shift form)    | `((x >> i) & 1)`                              | direct bit test                   |
| Set `i`-th bit                | `x \|= (1 << i)`                              | force bit to `1`                  |
| Clear `i`-th bit              | `x &= ~(1 << i)`                              | force bit to `0`                  |
| Toggle `i`-th bit             | `x ^= (1 << i)`                               | flip bit                          |
| Isolate lowest set bit        | `x & -x`                                      | keep rightmost `1` only           |
| Clear lowest set bit          | `x & (x - 1)`                                 | remove rightmost `1`              |
| Power of 2 check              | `x > 0 && (x & (x - 1)) == 0`                 | exactly one bit set               |
| Count set bits                | `while (x) { x &= x - 1; cnt++; }`            | `O(popcount)` loop                |
| Popcount builtin              | `__builtin_popcount(x)`                       | count `1`s                        |
| Popcount 64-bit               | `__builtin_popcountll(x)`                     | count `1`s in `long long`         |
| Trailing zeros                | `__builtin_ctz(x)`                            | index of lowest set bit           |
| Trailing zeros 64-bit         | `__builtin_ctzll(x)`                          | lowest set bit index in `64`-bit  |
| Leading zeros                 | `__builtin_clz(x)`                            | leading zero count                |
| Leading zeros 64-bit          | `__builtin_clzll(x)`                          | leading zero count in `64`-bit    |
| Highest set bit               | `31 - __builtin_clz(x)`                       | MSB index in `32`-bit             |
| Highest set bit 64-bit        | `63 - __builtin_clzll(x)`                     | MSB index in `64`-bit             |
| Bit length                    | `32 - __builtin_clz(x)`                       | number of bits needed             |
| Next power of 2               | `1 << (32 - __builtin_clz(x - 1))`            | smallest `2^k >= x`               |
| Parity builtin                | `__builtin_parity(x)`                         | `1` if popcount is odd, else `0`  |
| Lowest zero bit value         | `~x & (x + 1)`                                | value of rightmost `0`            |
| Set lowest zero bit           | `x \| (x + 1)`                                | turn rightmost `0` into `1`       |
| Set lowest unset bit          | `x \|= x + 1`                                 | force rightmost `0` to `1`        |
| Clear lowest zero bit         | `x & (x + 1)`                                 | remove rightmost `0` from pattern |
| Clear trailing ones           | `x & (x + 1)`                                 | remove consecutive low `1`s       |
| Toggle lowest zero bit        | `x ^ (x + 1)`                                 | flip bits up to rightmost `0`     |
| All bits set in `k`           | `(1 << k) - 1`                                | mask of lowest `k` bits           |
| Extract lowest `k` bits       | `x & ((1 << k) - 1)`                          | keep last `k` bits                |
| Clear lowest `k` bits         | `x & ~((1 << k) - 1)`                         | zero last `k` bits                |
| Toggle lowest `k` bits        | `x ^ ((1 << k) - 1)`                          | flip last `k` bits                |
| Set lowest `k` bits           | `x \| ((1 << k) - 1)`                         | make last `k` bits `1`            |
| Test subset                   | `(a & b) == a`                                | `a` is subset of `b`              |
| Test disjoint                 | `(a & b) == 0`                                | no common bits                    |
| Remove lowest set bit in loop | `x &= x - 1`                                  | drop one set bit                  |
| Enumerate all subsets         | `for (mask = 0; mask < (1 << n); mask++)`     | iterate over all subsets          |
| Enumerate submasks            | `for (s = m; s; s = (s - 1) & m)`             | all non-empty submasks of `m`     |
| Enumerate supersets           | `for (s = m; s < (1 << n); s = (s + 1) \| m)` | all supersets                     |
| Gray code                     | `x ^ (x >> 1)`                                | binary → Gray code                |
| Range XOR via prefix          | `P[R] ^ P[L - 1]`                             | XOR over subarray `[L, R]`        |

## Principle of Bit Independence

For bitwise operations (`&`, `|`, `^`), each bit position is **independent** of every other bit - there are no carries or borrows between bits. This allows problems to be solved **one bit at a time**, making it a fundamental technique for per-bit contribution and bitmask algorithms etc.

## Fundamental Equation of Bitwise Addition

The **Fundamental Equation of Bitwise Addition** is a fundamental identity in bit manipulation that expresses ordinary integer addition entirely using bitwise operations. It decomposes binary addition into two independent components: the **carry-free sum**, computed by `XOR`, and the **carry contribution**, identified by `AND`. Together, these components exactly reconstruct the arithmetic sum.

$$
a+b=(a\oplus b)+2(a\&b)
$$

where

- `a + b` - **Arithmetic Sum**
- `a ⊕ b` - **Carry-Free Sum**
- `a & b` - **Carry Mask**
- `2(a & b)` (equivalently `(a & b) << 1`) - **Carry Contribution**

**Converse.** `a ⊕ b = (a + b) - 2(a & b)`, expressing the carry-free sum in terms of the arithmetic sum and the carry contribution.

**Corollary.** `a + b = (a | b) + (a & b)`, obtained from the identity `a | b = (a ⊕ b) + (a & b)`.

> [!abstract]- Mathematical Proof
> Binary addition consists of two independent operations: computing the **sum bits** and generating the **carry bits**. The `XOR` operation produces the sum of each bit while ignoring carries, whereas the `AND` operation identifies exactly the positions where carries are generated. Since every carry contributes to the next higher bit, its value is shifted left by one position, which is equivalent to multiplying by `2`.
> - `XOR` computes the carry-free sum:
>   - `0 + 0 → 0`
>   - `0 + 1 → 1`
>   - `1 + 0 → 1`
>   - `1 + 1 → 0` (carry generated)
> - `AND` produces `1` only when both bits are `1`, precisely identifying every generated carry.
> - Each carry shifts one position to the left, contributing `2(a & b)` to the final result.
> - Therefore, combining the carry-free sum and the carry contribution reconstructs the ordinary arithmetic sum:
>   `a + b = (a ⊕ b) + 2(a & b)`

## XOR Algebra

- **Self-Inverse (Nilpotency):** `x ^ x = 0`
  (Used constantly to find "the single non-repeating element" in an
  array.)
- **Identity Element:** `x ^ 0 = x`
- **Commutativity & Associativity:** `a ^ b = b ^ a`,
  `(a ^ b) ^ c = a ^ (b ^ c)` - order never matters, so an XOR sum can
  be rearranged however you like.
- **The Reversibility Rule:** if `a ^ b = c`, then `a ^ c = b` and
  `b ^ c = a`. (Given two pieces of the triangle, you can always find
  the third - heavily used for missing elements / reversing ops.)
  
#### Tricks

- Prefix XOR for Range Queries
    
    Just like a prefix-sum array gives `Sum(L, R) = P[R] - P[L-1]`, and
    because XOR is its own inverse (`x ^ x = 0`):
    1. Build a prefix XOR array: `P[i] = P[i-1] ^ A[i]`
    2. XOR of range `[L, R]` = `P[R] ^ P[L-1]`
    Elements from `0` to `L-1` appear in both `P[R]` and `P[L-1]`, so
    XORing cancels them, leaving only the elements from `L` to `R`.


## Bitmasking 

In competitive programming, you often encounter problems with small
constraints, like `N <= 20`. Whenever you see `N` hovering around
`15` to `22`, your brain should immediately scream: **Bitmasking**.

A bitmask is just an integer, but instead of caring about its decimal
value, we care about its **binary representation**.

Imagine an array of items: `['Apple', 'Banana', 'Cherry']`. Any
subset can be represented with a 3-bit number:
- bit 0 → `Apple`, bit 1 → `Banana`, bit 2 → `Cherry`.
- `1` = item is in the subset, `0` = item is out.

```
000 (0): {}
101 (5): {Apple, Cherry}
111 (7): {Apple, Banana, Cherry}
```

## Bitset
For problems involving huge boolean arrays and set operations at
scale (e.g. reachability, subset-sum, string matching via bitset),
`std::bitset<N>` packs `N` bits into words and lets `&`, `|`, `^`,
`<<`, `>>`, and `.count()` run `~64x` faster than a `bool[]` loop
because the CPU processes 64 bits per instruction. This is a
frequent way to push an `O(N^2)` DP down to `O(N^2 / 64)`, which
often is the difference between TLE and AC.



## Submask Enumeration

For a fixed `mask`, all submasks can be generated in descending order by repeatedly doing

$$
\text{submask} = (\text{submask} - 1)\ &\ \text{mask}.
$$

`submask - 1` clears the rightmost set bit and turns all lower bits into `1`; `& mask` removes the illegal bits, so the result is the next valid submask. Starting from `mask`, this sequence is strictly decreasing, stays inside the set of valid submasks, and reaches `0`, so every submask appears exactly once.

If `k = \operatorname{popcount}(\text{mask})`, the loop runs `2^k` times.

```cpp
for (int submask = mask; submask; submask = (submask - 1) & mask) {
    // process submask
}
// handle submask = 0 if needed
```
