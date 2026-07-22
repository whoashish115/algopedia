---
title: Knuth-Morris-Pratt (KMP) Algorithm
---

**Purpose:** Find all occurrences of a pattern string in a text string in O(N + M) worst-case time, using a precomputed failure function to avoid re-examining matched characters.
### Algorithm
1. Build the **failure function** (`lps[]`, "longest proper prefix that is also a suffix") for the pattern: `lps[i]` is the length of the longest proper prefix of `pattern[0..i]` that is also a suffix of it.
2. To build `lps[]`, use two pointers: one tracking the current prefix-length candidate, and extend it when characters match, or fall back using `lps[]` itself when they don't (self-referential construction).
3. To search the text, maintain a pointer into the pattern while scanning the text character by character.
4. On a character match, advance both text and pattern pointers.
5. On a mismatch, instead of restarting the pattern pointer from 0, jump it back using `lps[]` — skipping re-comparison of characters already known to match.
6. Whenever the pattern pointer reaches the pattern's length, record a match and continue searching using `lps[]` to find the next potential overlap.

### Code
```cpp
vector<int> buildLPS(string& pattern) {
    int m = pattern.size();
    vector<int> lps(m, 0);
    int len = 0, i = 1;

    while (i < m) {
        if (pattern[i] == pattern[len]) {
            lps[i++] = ++len;
        } else if (len > 0) {
            len = lps[len - 1];
        } else {
            lps[i++] = 0;
        }
    }
    return lps;
}

vector<int> kmpSearch(string text, string pattern) {
    int n = text.size(), m = pattern.size();
    vector<int> lps = buildLPS(pattern);
    vector<int> matches;

    int i = 0, j = 0;
    while (i < n) {
        if (text[i] == pattern[j]) {
            i++; j++;
            if (j == m) {
                matches.push_back(i - j);
                j = lps[j - 1];
            }
        } else if (j > 0) {
            j = lps[j - 1];
        } else {
            i++;
        }
    }
    return matches;
}
```

### Paradigm
**Dynamic Programming.** `lps[i]` is the optimal solution to the subproblem "longest proper prefix-suffix match ending at position `i`," built directly from smaller-index solutions via the fallback recurrence — the failure function is a classic 1D DP over the pattern.

### Complexity
- Time: O(N + M)
- Space: O(M)

### Proof of Correctness
**Why `lps[]` is computed correctly:** By induction on `i`: `lps[0] = 0` trivially (a single character has no proper prefix). Assume `lps[0..i-1]` are correct. To extend to position `i`, if `pattern[i] == pattern[len]` (where `len = lps[i-1]`), the previous longest matching prefix can be extended by one character, so `lps[i] = len + 1` is correct. If they don't match, the algorithm falls back to `lps[len-1]`, which by the inductive hypothesis gives the next-best candidate prefix length to try extending — this fallback is safe because any valid prefix-suffix match of length `len` at position `i-1` that fails to extend must itself have a valid shorter prefix-suffix match (exactly `lps[len-1]`) that might still extend, and no longer candidate could possibly work (since the longest one already failed).

**Why the search phase never needs to backtrack the text pointer `i`:** On a mismatch, instead of restarting `j` from 0 and re-scanning text characters, jumping `j` to `lps[j-1]` preserves correctness because the text characters already matched (`text[i-j..i-1]`) are known to equal `pattern[0..j-1]`, and `lps[j-1]` identifies the longest prefix of the pattern that is guaranteed to already match the suffix of what's been scanned — so resuming comparison from this point never skips over a valid match starting position (any earlier restart point would necessarily fail sooner, by the maximality of `lps[]`).

**Why all matches are found:** Since `i` only ever increases (never decreases) and every text character is examined against some pattern position before `i` advances past it, no valid alignment is skipped — the failure-function jumps only skip *redundant* comparisons that are provably guaranteed to fail or already known to match, never skip potential match positions themselves. ∎

### Variants / Use Cases
- **Z-Algorithm** → alternative O(N + M) string matching using a different auxiliary array (Z-array), often considered more intuitive
- **Pattern period detection** → `m - lps[m-1]` gives the smallest period of the pattern directly from the failure function
- **Automaton-based KMP** → precompute a full transition table for O(1) worst-case per-character search (trades space for guaranteed no-backtrack lookups)
- **Multiple pattern matching (Aho-Corasick)** → generalizes KMP's failure function idea to a trie of many patterns simultaneously
- **DNA sequence matching, plagiarism detection** → classic real-world exact string matching applications
