---
title: Sieve of Eratosthenes
---

**Purpose:** Find all prime numbers up to `N`, in O(N log log N) time.
### Algorithm
1. Create a boolean array `isPrime[0..N]`, initialized to true, except `isPrime[0]` and `isPrime[1]` set to false.
2. Starting from `p = 2`, if `isPrime[p]` is still true, mark every multiple of `p` (starting from `p*p`, since smaller multiples were already marked by smaller primes) as not prime.
3. Move to the next number and repeat, stopping once `p*p > N` (no smaller factor could remain unmarked beyond this point).
4. All indices still marked true are prime.

### Code
```cpp
vector<bool> sieveOfEratosthenes(int n) {
    vector<bool> isPrime(n + 1, true);
    isPrime[0] = isPrime[1] = false;

    for (int p = 2; (long long)p * p <= n; p++) {
        if (isPrime[p]) {
            for (int multiple = p * p; multiple <= n; multiple += p) {
                isPrime[multiple] = false;
            }
        }
    }
    return isPrime;
}
```

### Paradigm
**Brute Force (with incremental marking), sometimes classified as Transform and Conquer.** It systematically eliminates all composite numbers by direct multiplication rather than by testing each number for primality individually — an exhaustive but structured elimination process rather than a clever reduction.

### Complexity
- Time: O(N log log N)
- Space: O(N)

### Proof of Correctness
**Why every composite number gets marked:** Any composite number `c ≤ N` has a smallest prime factor `p ≤ √c ≤ √N`. When the outer loop reaches this `p`, since `p` was never marked composite by any smaller prime (it's `c`'s *smallest* factor, so `p` itself is prime and unmarked at that point), the inner loop marks all multiples of `p` starting from `p²`, which includes `c` (since `c = p × (c/p)` and `c/p ≥ p`, because `p` is the smallest factor). So every composite number is guaranteed to be marked by the time its smallest prime factor is processed.

**Why starting the inner loop at `p²` is safe:** Any multiple of `p` smaller than `p²` — i.e., `p × k` for `k < p` — must have `k` as a factor smaller than `p`, meaning it would already have been marked composite by that smaller prime factor `k` (or `k`'s own smallest prime factor) in an earlier iteration. So skipping straight to `p²` doesn't miss marking any composite number; it only avoids redundant work.

**Why stopping at `p × p > N` is safe:** If `p > √N`, then any composite multiple of `p` up to `N` must be `p × k` for some `k < p` — but such a `k` is itself smaller than `p` and would have its own smallest prime factor `≤ k < p`, meaning that composite was already marked in an earlier iteration. So no further marking is needed once `p² > N`.

**Time complexity derivation:** The total work is `Σ (over primes p ≤ √N) of N/p`, which by Mertens' second theorem sums to `Θ(N log log N)`. ∎

### Variants / Use Cases
- **Segmented Sieve** → finds primes in a range `[L, R]` for very large `R` without allocating O(R) memory, by sieving using only primes up to `√R`
- **Sieve of Eratosthenes for smallest prime factor (SPF)** → precomputes SPF for fast factorization queries in O(log N) per number
- **Linear Sieve (O(N) sieve)** → each composite is marked exactly once using its smallest prime factor, achieving true linear time
- **Euler's Totient Sieve** → extends the sieve to compute φ(n) for all n up to N simultaneously
- **Prime counting, twin prime detection, Goldbach-related computations** → all commonly built on top of a base sieve
