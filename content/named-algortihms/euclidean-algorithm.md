---
title: Euclidean Algorithm
---

**Purpose:** Compute the Greatest Common Divisor (GCD) of two integers, in O(log(min(a, b))) time.
### Algorithm
1. Given two integers `a` and `b` (assume `a ≥ b ≥ 0`).
2. If `b` is 0, the GCD is `a` — stop.
3. Otherwise, replace `(a, b)` with `(b, a mod b)`.
4. Repeat until `b` becomes 0.

### Code
```cpp
int gcd(int a, int b) {
    while (b != 0) {
        int temp = b;
        b = a % b;
        a = temp;
    }
    return a;
}
```

### Paradigm
**Decrease and Conquer.** Each step reduces the problem to a strictly smaller instance (`a mod b` is always smaller than `b`), shrinking toward the trivial base case `b = 0` without needing to split into independent subproblems.

### Complexity
- Time: O(log(min(a, b)))
- Space: O(1)

### Proof of Correctness
**Claim:** `gcd(a, b) = gcd(b, a mod b)`.

**Proof:** Let `r = a mod b`, so `a = qb + r` for some integer `q`. Any common divisor `d` of `a` and `b` must also divide `a - qb = r`, so `d` is a common divisor of `b` and `r`. Conversely, any common divisor `d` of `b` and `r` must also divide `qb + r = a`, so `d` is a common divisor of `a` and `b`. Since `a` and `b` have exactly the same set of common divisors as `b` and `r`, they share the same *greatest* common divisor: `gcd(a, b) = gcd(b, r)`.

**Termination:** Since `r = a mod b` always satisfies `0 ≤ r < b`, the second element of the pair strictly decreases every step (as long as it's not already 0), so the sequence of `b` values is strictly decreasing and bounded below by 0 — it must reach 0 in finitely many steps. When `b = 0`, `gcd(a, 0) = a` trivially (every number divides 0, so the greatest common divisor of `a` and 0 is `a` itself).

**Why O(log(min(a,b))) steps suffice:** This follows from a classical result (related to the Fibonacci sequence being the worst case): it can be shown that `a mod b < a / 2` whenever `b ≤ a/2`, and when `b > a/2`, `a mod b = a - b < a/2` directly — either way, after two steps the larger number at least halves. So the number of steps is O(log(min(a,b))). ∎

### Variants / Use Cases
- **Extended Euclidean Algorithm** → additionally finds integers `x, y` such that `ax + by = gcd(a,b)`, used for modular inverses
- **Binary GCD algorithm (Stein's algorithm)** → avoids division/modulo, using only subtraction and bit shifts, useful on hardware where division is slow
- **LCM computation** → `lcm(a,b) = a / gcd(a,b) * b`, built directly on this algorithm
- **Simplifying fractions** → dividing numerator and denominator by their GCD
- **RSA and cryptographic key generation** → GCD checks are used to verify coprimality of chosen numbers