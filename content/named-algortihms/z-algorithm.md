---
title: Z Algorithm
---

**Purpose:** Compute, for every position in a string, the length of the longest substring starting there that matches a prefix of the string, in O(N) time — commonly used for pattern matching.

### Algorithm
1. Define `Z[i]` as the length of the longest common prefix between the string and its suffix starting at index `i` (`Z[0]` is conventionally undefined or set to the string length).
2. Maintain a window `[L, R]` representing the rightmost Z-box found so far (a substring starting at `L` matching a prefix, extending to `R`).
3. For each position `i` from 1 onward: if `i` is inside the current window (`i ≤ R`), initialize `Z[i]` using the already-known value `Z[i - L]` (mirrored position), capped so it doesn't exceed the window boundary.
4. Extend `Z[i]` by direct character comparison beyond the window boundary if possible.
5. If this extension pushes `i + Z[i] - 1` beyond `R`, update the window `[L, R]` to `[i, i + Z[i] - 1]`.
6. For pattern matching, concatenate `pattern + separator + text` and scan for positions where `Z[i] == pattern.length()`.

### Code
```cpp
vector<int> zFunction(string s) {
    int n = s.size();
    vector<int> z(n, 0);
    int l = 0, r = 0;

    for (int i = 1; i < n; i++) {
        if (i < r) z[i] = min(r - i, z[i - l]);
        while (i + z[i] < n && s[z[i]] == s[i + z[i]]) z[i]++;
        if (i + z[i] > r) { l = i; r = i + z[i]; }
    }
    return z;
}

vector<int> zSearch(string text, string pattern) {
    string combined = pattern + "#" + text;
    vector<int> z = zFunction(combined);
    vector<int> matches;
    int m = pattern.size();

    for (int i = m + 1; i < (int)combined.size(); i++) {
        if (z[i] == m) matches.push_back(i - m - 1);
    }
    return matches;
}
```

### Paradigm
**Dynamic Programming (with amortized/two-pointer reuse).** `Z[i]` is computed by reusing previously computed `Z[i-L]` values whenever possible, avoiding redundant comparisons — a DP-style reuse of overlapping subproblem results, combined with a two-pointer window that never moves backward.

### Complexity
- Time: O(N)
- Space: O(N)

### Proof of Correctness
**Why reusing `Z[i-L]` inside the window is valid:** By definition of the window `[L, R]`, the substring `s[L..R]` matches the prefix `s[0..R-L]` exactly. So for `i` inside this window, `s[i..R]` corresponds to `s[i-L..R-L]` in the prefix. If `Z[i-L]` (the match length starting at the mirrored position `i-L` in the prefix) is strictly less than `R - i + 1` (doesn't reach the window boundary), then the match at `i` must have exactly the same length `Z[i-L]`, since it's bounded by the known mismatch at `s[i-L+Z[i-L]]` inside the already-verified matching region — no further comparison is needed.

**Why extension beyond the window is safe and necessary:** If `Z[i-L]` reaches exactly to the window boundary (`R - i + 1`), the character comparisons beyond `R` haven't been checked yet (since the window only guarantees matching up to `R`), so the algorithm must extend by direct comparison from that point — this is the only case requiring fresh character comparisons.

**Why total work is O(N) (amortized):** Every direct character comparison that succeeds either extends `R` further right (and `R` only ever increases, never decreases, across the whole algorithm), or fails immediately (O(1) work, ending that position's extension). Since `R` advances by at most `N` total over the entire algorithm, the total number of successful extending comparisons is bounded by O(N), and each position does O(1) additional work for the failed comparison and the mirrored lookup — giving O(N) total.

**Correctness of pattern search:** Concatenating `pattern + separator + text` (with a separator not appearing in either) ensures any `Z[i] == pattern.length()` in the combined string's text portion corresponds exactly to a position where the text substring matches the full pattern, since the Z-value can never "leak" across the separator into a false match. ∎

### Variants / Use Cases
- **KMP Algorithm** → alternative O(N + M) exact matching using a failure function instead of Z-values, more space-efficient in practice
- **Pattern occurrence counting, distinct substring counting** → Z-array is a versatile building block for many string statistics
- **String periodicity detection** → smallest period of a string derivable from where `i + Z[i] == n`
- **Multiple pattern matching preprocessing** → Z-function ideas extend to suffix automaton / suffix array construction
- **Longest common prefix queries between arbitrary suffixes** → related to suffix array + LCP array structures

