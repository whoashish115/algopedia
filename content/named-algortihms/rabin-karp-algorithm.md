---
title: Rabin-Karp Algorithm
---

**Purpose:** Find all occurrences of a pattern string in a text string using rolling hashes, in O(N + M) average time (O(NM) worst case with hash collisions).

### Algorithm

1. Compute a hash of the pattern (length `M`).
2. Compute a hash of the first `M`-length window of the text.
3. Compare hashes: if they match, verify with a direct character comparison (to guard against hash collisions), and record a match if confirmed.
4. Slide the window one character to the right: update the hash in O(1) using the rolling hash formula (remove the leftmost character's contribution, add the new rightmost character's contribution).
5. Repeat steps 3-4 until the window reaches the end of the text.

### Code

```cpp
vector<int> rabinKarp(string text, string pattern) {
    int n = text.size(), m = pattern.size();
    if (m > n) return {};

    const long long base = 131, mod = 1e9 + 7;
    long long patternHash = 0, windowHash = 0, power = 1;

    for (int i = 0; i < m; i++) {
        patternHash = (patternHash * base + pattern[i]) % mod;
        windowHash = (windowHash * base + text[i]) % mod;
        if (i > 0) power = (power * base) % mod;
    }

    vector<int> matches;
    for (int i = 0; ; i++) {
        if (windowHash == patternHash && text.substr(i, m) == pattern) matches.push_back(i);
        if (i + m >= n) break;
        windowHash = ((windowHash - text[i] * power % mod + mod) % mod * base + text[i + m]) % mod;
    }
    return matches;
}
```

### Paradigm

**Transform and Conquer.** The core idea is instance simplification: transforming substring comparison into integer hash comparison, so most windows can be rejected in O(1) instead of an O(M) character comparison, only falling back to direct comparison on hash matches.

### Complexity

- Time: O(N + M) average; O(NM) worst case if many spurious hash collisions occur
- Space: O(1) beyond input

### Proof of Correctness

**Why the rolling hash update is correct:** The hash of a window is computed as a polynomial in `base`: `hash = c_0·base^(m-1) + c_1·base^(m-2) + ... + c_{m-1}`. Sliding the window right by one removes the leading term's contribution (`c_0 · base^(m-1)`, subtracted using the precomputed `power = base^(m-1)`) and shifts the remaining terms up by one power of `base` (achieved via multiplying by `base`), then adds the new trailing character. This exactly reconstructs the polynomial hash for the new window, verified directly by algebraic expansion of the polynomial definition.

**Why hash matches are verified before reporting:** Since the hash function maps a large space of strings into a smaller modular space, distinct substrings can theoretically collide to the same hash value. Confirming with a direct character comparison whenever hashes match guarantees zero false positives are reported, so every reported match is a genuine occurrence.

**Why every true occurrence is found:** The algorithm checks every possible starting position from `0` to `n - m`, and any window whose actual content equals the pattern will also have a matching hash (hashes are deterministic functions of content), so it will pass the hash check and then be confirmed by the direct comparison — no true match is skipped.

**Expected linear time:** With a well-chosen base and modulus (or double hashing to reduce collision probability), the expected number of hash collisions across all windows is small (analyzable via a standard birthday-bound argument on random hash functions), so the total cost of verification steps is O(N) in expectation, on top of O(N) for the rolling hash updates — giving O(N + M) expected total. ∎

### Variants / Use Cases

- **Double hashing (two independent mod/base pairs)** → drastically reduces worst-case collision probability, used to defeat adversarial inputs
- **Multiple pattern search** → hash all patterns into a set, then check each text window's hash against the set in O(1) average
- **Plagiarism detection / duplicate substring detection** → rolling hashes are the basis of many document-similarity and duplicate-detection systems
- **String matching with wildcards** → adaptations exist using polynomial hashing over restricted alphabets
- **KMP / Z-algorithm** → alternative deterministic O(N + M) string matching algorithms without any collision risk
