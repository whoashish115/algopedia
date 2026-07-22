---
title: Aho-Corasick Algorithm
---

**Purpose:** Search a text for occurrences of many patterns simultaneously, in O(N + M + Z) time (N = text length, M = total pattern length, Z = number of matches), using a trie augmented with failure links.

### Algorithm
1. Build a trie (prefix tree) from all pattern strings, marking nodes where a pattern ends.
2. Compute a **failure link** for every trie node: the longest proper suffix of the current node's string that is also a prefix of some pattern (i.e., also a node in the trie) — computed via BFS, analogous to KMP's failure function but on a tree.
3. Also compute **output links** (or merge failure-linked "end of pattern" markers) so that a node "knows" about all patterns ending at any of its failure-link ancestors.
4. Scan the text once, moving through the trie: for each character, follow the trie edge if it exists; otherwise, follow failure links until an edge exists (or the root is reached).
5. At each step, report all patterns ending at the current node (via output links) as matches ending at this text position.

### Code
```cpp
struct AhoCorasick {
    struct Node {
        map<char,int> next;
        int fail = 0;
        vector<int> patternIdx; // indices of patterns ending here (via output links)
    };
    vector<Node> trie{Node()};

    void addPattern(string& s, int idx) {
        int cur = 0;
        for (char c : s) {
            if (!trie[cur].next.count(c)) {
                trie[cur].next[c] = trie.size();
                trie.push_back(Node());
            }
            cur = trie[cur].next[c];
        }
        trie[cur].patternIdx.push_back(idx);
    }

    void buildFailureLinks() {
        queue<int> q;
        for (auto& [c, v] : trie[0].next) { trie[v].fail = 0; q.push(v); }

        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (auto& [c, v] : trie[u].next) {
                int f = trie[u].fail;
                while (f && !trie[f].next.count(c)) f = trie[f].fail;
                trie[v].fail = trie[f].next.count(c) ? trie[f].next[c] : 0;
                if (trie[v].fail == v) trie[v].fail = 0;
                auto& failPatterns = trie[trie[v].fail].patternIdx;
                trie[v].patternIdx.insert(trie[v].patternIdx.end(), failPatterns.begin(), failPatterns.end());
                q.push(v);
            }
        }
    }

    vector<pair<int,int>> search(string& text) { // {position, patternIdx}
        vector<pair<int,int>> matches;
        int cur = 0;
        for (int i = 0; i < (int)text.size(); i++) {
            char c = text[i];
            while (cur && !trie[cur].next.count(c)) cur = trie[cur].fail;
            if (trie[cur].next.count(c)) cur = trie[cur].next[c];
            for (int idx : trie[cur].patternIdx) matches.push_back({i, idx});
        }
        return matches;
    }
};
```

### Paradigm
**Dynamic Programming (failure link construction) combined with Transform and Conquer.** Failure links are built via a DP-like BFS reuse of shorter prefixes' failure information (mirroring KMP), and the trie itself transforms many independent pattern searches into a single automaton traversal.

### Complexity
- Time: O(M) to build the trie and failure links (alphabet-dependent), O(N + Z) to search, where Z is the number of matches reported
- Space: O(M · alphabet size) for the trie

### Proof of Correctness
**Why failure links are analogous to KMP and correctly computed:** `fail[v]` is defined as the trie node corresponding to the longest proper suffix of `v`'s string that is also some prefix in the trie (i.e., a valid trie node). Building this via BFS in order of increasing depth ensures that when computing `fail[v]` for a child `v` of `u` via character `c`, `fail[u]` is already correctly computed, so the algorithm can correctly walk up `u`'s failure chain (exactly like KMP's fallback) to find the longest suffix that can be extended by `c` — this is a direct generalization of KMP's failure function construction from a single string to a trie of many strings.

**Why the automaton traversal correctly finds every occurrence:** At every text position, the current trie node after following edges/failure-links represents the longest suffix of the text-so-far that matches some prefix in the trie (this is the trie-generalization of the KMP invariant). Any pattern that ends at the current text position must be a suffix of this longest match, so it appears either at the current node directly or at one of its failure-link ancestors — checking all `patternIdx` accumulated via output links (which merge failure-ancestor pattern lists at construction time) correctly reports every such match without needing to walk the failure chain again at query time.

**Why merging patternIdx along failure links at build time is valid:** Since `fail[v]`'s represented string is always a suffix of `v`'s represented string, any pattern ending at `fail[v]` is automatically also "ending" (as a suffix match) whenever the automaton is at node `v` — so precomputing this union once during construction correctly captures all matches reachable from `v` without repeated failure-chain walks during the search phase. ∎

### Variants / Use Cases
- **Multi-pattern text filtering (spam/profanity filters)** → classic real-world use, checking many banned words simultaneously
- **Intrusion detection systems / DPI (deep packet inspection)** → matching many known attack signatures against network traffic in real time
- **Aho-Corasick + DP on automaton states** → common technique for counting strings avoiding/containing certain patterns (string counting with forbidden substrings)
- **Bioinformatics (multi-motif search in DNA)** → searching for many known sequences within a genome simultaneously
- **KMP Algorithm** → the single-pattern special case that Aho-Corasick generalizes
