---
title: General
---

## Binary Numbers (0-15)

| Dec |  Bin   | Dec |  Bin   | Dec |  Bin   | Dec |  Bin   |
| --: | :----: | --: | :----: | --: | :----: | --: | :----: |
|   0 | `0000` |   4 | `0100` |   8 | `1000` |  12 | `1100` |
|   1 | `0001` |   5 | `0101` |   9 | `1001` |  13 | `1101` |
|   2 | `0010` |   6 | `0110` |  10 | `1010` |  14 | `1110` |
|   3 | `0011` |   7 | `0111` |  11 | `1011` |  15 | `1111` |

## Powers of Two

|  Power   | Value |  Power   |     Value |  Power   |         Value |
| :------: | ----: | :------: | --------: | :------: | ------------: |
|  $2^0$   |     1 | $2^{11}$ |     2,048 | $2^{22}$ |     4,194,304 |
|  $2^1$   |     2 | $2^{12}$ |     4,096 | $2^{23}$ |     8,388,608 |
|  $2^2$   |     4 | $2^{13}$ |     8,192 | $2^{24}$ |    16,777,216 |
|  $2^3$   |     8 | $2^{14}$ |    16,384 | $2^{25}$ |    33,554,432 |
|  $2^4$   |    16 | $2^{15}$ |    32,768 | $2^{26}$ |    67,108,864 |
|  $2^5$   |    32 | $2^{16}$ |    65,536 | $2^{27}$ |   134,217,728 |
|  $2^6$   |    64 | $2^{17}$ |   131,072 | $2^{28}$ |   268,435,456 |
|  $2^7$   |   128 | $2^{18}$ |   262,144 | $2^{29}$ |   536,870,912 |
|  $2^8$   |   256 | $2^{19}$ |   524,288 | $2^{30}$ | 1,073,741,824 |
|  $2^9$   |   512 | $2^{20}$ | 1,048,576 | $2^{31}$ | 2,147,483,648 |
| $2^{10}$ | 1,024 | $2^{21}$ | 2,097,152 |          |               |

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
>
> - `XOR` computes the carry-free sum:
>   - `0 + 0 → 0`
>   - `0 + 1 → 1`
>   - `1 + 0 → 1`
>   - `1 + 1 → 0` (carry generated)
> - `AND` produces `1` only when both bits are `1`, precisely identifying every generated carry.
> - Each carry shifts one position to the left, contributing `2(a & b)` to the final result.
> - Therefore, combining the carry-free sum and the carry contribution reconstructs the ordinary arithmetic sum:
>   `a + b = (a ⊕ b) + 2(a & b)`

---

## Parity Equation

$$
x_1\oplus x_2\oplus\cdots\oplus x_n
$$

equals $1$ iff an odd number of bits are set.

```cpp
void xorSwap(int &a, int &b){
    if(&a==&b) return;
    a^=b;
    b^=a;
    a^=b;
}
```

## MSB-Greedy Principle

$$
2^k>\sum_{i=0}^{k-1}2^i=2^k-1
$$

In any bitwise optimization processed from MSB to LSB, securing a `1` at bit $k$ is always more valuable than any combination of lower bits. Hence greedy decisions at higher bits are never revoked.

---

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

---

## Boolean Identities

#### Identity

$$
a\land1=a,\qquad
a\lor0=a,\qquad
a\oplus0=a
$$

#### Null / Domination

$$
a\land0=0,\qquad
a\lor1=1
$$

#### Idempotent

$$
a\land a=a,\qquad
a\lor a=a
$$

#### Complement

$$
a\land\neg a=0,\qquad
a\lor\neg a=1,\qquad
a\oplus1=\neg a
$$

#### Double Negation

$$
\neg(\neg a)=a
$$

#### Commutative

$$
a\land b=b\land a,\qquad
a\lor b=b\lor a,\qquad
a\oplus b=b\oplus a
$$

#### Associative

$$
(a\land b)\land c=a\land(b\land c)
$$

$$
(a\lor b)\lor c=a\lor(b\lor c)
$$

$$
(a\oplus b)\oplus c=a\oplus(b\oplus c)
$$

#### Distributive

$$
a\land(b\lor c)=(a\land b)\lor(a\land c)
$$

$$
a\lor(b\land c)=(a\lor b)\land(a\lor c)
$$

#### Absorption

$$
a\lor(a\land b)=a,\qquad
a\land(a\lor b)=a
$$

#### De Morgan

$$
\neg(a\land b)=\neg a\lor\neg b,\qquad
\neg(a\lor b)=\neg a\land\neg b
$$

#### XOR / Mixed Identities

$$
a\oplus a=0,\qquad
a\oplus0=a,\qquad
a\oplus b\oplus b=a
$$

$$
a\oplus b=(a\land\neg b)\lor(\neg a\land b)
$$

$$
a\oplus b=(a\lor b)\land\neg(a\land b)
$$

$$
a\oplus(a\land b)=a\land\neg b
$$

$$
a\oplus(a\lor b)=\neg a\land b
$$

$$
a\land(a\oplus b)=a\land\neg b
$$

$$
a\lor(a\oplus b)=a\lor b
$$

#### Arithmetic Identity

$$
a+b=(a\oplus b)+2(a\land b)
$$

#### Reversibility

$$
a\oplus b=c
\iff
a\oplus c=b
\iff
b\oplus c=a
$$

#### Cancellation

$$
x\oplus x=0,\qquad
x\oplus y=x\oplus z\iff y=z
$$

## Operator Law Map

| Law                    | Follows for              |
| ---------------------- | ------------------------ |
| Identity               | AND, OR, XOR             |
| Null / Domination      | AND, OR                  |
| Idempotent             | AND, OR                  |
| Complement             | AND, OR, XOR             |
| Double Negation        | NOT                      |
| Commutative            | AND, OR, XOR             |
| Associative            | AND, OR, XOR             |
| Distributive           | AND over OR, OR over AND |
| Absorption             | AND, OR                  |
| De Morgan              | NOT with AND / OR        |
| XOR / Mixed Identities | XOR, AND, OR             |
| Arithmetic Relation    | XOR, AND                 |
| Reversibility          | XOR                      |
| Cancellation           | XOR                      |

---

## Submask Enumeration

For a fixed `mask`, all of its submasks can be generated in **descending order** using

$$
\text{submask}=(\text{submask}-1)\,\&\,\text{mask}.
$$

Subtracting `1` clears the rightmost set bit and sets all lower bits to `1`. Applying `& mask` removes bits not present in `mask`, yielding the next valid submask.

Starting from `mask`, the sequence is strictly decreasing, remains a submask of `mask`, and eventually reaches `0`. Thus every submask is generated exactly once.

If

$$
k=\operatorname{popcount}(\text{mask}),
$$

then the algorithm enumerates exactly

$$
2^k
$$

submasks.

```cpp
// Enumerate all non-zero submasks of mask
for (int submask = mask; submask; submask = (submask - 1) & mask) {
    // process submask
}

// Process the empty submask if needed
// submask = 0;
```

To include `0` in the same loop:

```cpp
for (int submask = mask;; submask = (submask - 1) & mask) {
    // process submask
    if (submask == 0) break;
}
```

## Tricks

- Prefix XOR for Range Queries
  Just like a prefix-sum array gives `Sum(L, R) = P[R] - P[L-1]`, and because XOR is its own inverse (`x ^ x = 0`):
  1. Build a prefix XOR array: `P[i] = P[i-1] ^ A[i]`
  2. XOR of range `[L, R]` = `P[R] ^ P[L-1]`
     Elements from `0` to `L-1` appear in both `P[R]` and `P[L-1]`, so XORing cancels them, leaving only the elements from `L` to `R`.
