---
title: Huffman Coding
---

**Purpose:** Build an optimal prefix-free binary encoding for a set of symbols based on their frequencies, minimizing total encoded length, in O(N log N) time.
### Algorithm
1. Compute the frequency of every symbol.
2. Create a leaf node for each symbol, and insert all nodes into a min-priority queue keyed by frequency.
3. While more than one node remains in the queue:
   - Pop the two lowest-frequency nodes.
   - Create a new internal node with these two as children, with frequency equal to their sum.
   - Push this new node back into the queue.
4. The last remaining node is the root of the Huffman tree.
5. Assign codes by traversing the tree from the root: each left branch appends `0`, each right branch appends `1`; the code for a symbol is the bit string from root to its leaf.
### Code
```cpp
struct Node {
    int freq;
    char ch;
    Node *left, *right;
    Node(int f, char c = 0, Node* l = nullptr, Node* r = nullptr) : freq(f), ch(c), left(l), right(r) {}
};

struct Compare { bool operator()(Node* a, Node* b) { return a->freq > b->freq; } };

void assignCodes(Node* node, string code, unordered_map<char, string>& codes) {
    if (!node) return;
    if (!node->left && !node->right) { codes[node->ch] = code.empty() ? "0" : code; return; }
    assignCodes(node->left, code + "0", codes);
    assignCodes(node->right, code + "1", codes);
}

unordered_map<char, string> huffmanCoding(unordered_map<char,int>& freq) {
    priority_queue<Node*, vector<Node*>, Compare> pq;
    for (auto& [ch, f] : freq) pq.push(new Node(f, ch));

    while (pq.size() > 1) {
        Node* a = pq.top(); pq.pop();
        Node* b = pq.top(); pq.pop();
        pq.push(new Node(a->freq + b->freq, 0, a, b));
    }

    unordered_map<char, string> codes;
    assignCodes(pq.top(), "", codes);
    return codes;
}
```

### Paradigm
**Greedy.** At every step it merges the two globally lowest-frequency nodes, relying on an exchange-argument proof that this local choice never prevents reaching the global optimum.

### Complexity
- Time: O(N log N)
- Space: O(N)

### Proof of Correctness
**Claim:** Huffman's greedy merging produces a tree minimizing total weighted path length (`Σ freq(symbol) × depth(symbol)`), which equals the minimum possible total encoded length among all prefix-free binary codes.

**Exchange argument:** Consider any optimal prefix code tree `T`. In `T`, the two lowest-frequency symbols can always be shown to be siblings at the maximum depth without loss of optimality: if they weren't already siblings at the deepest level, swapping them with whichever symbols currently occupy the two deepest sibling positions cannot increase total cost, since moving a lower-frequency symbol to a deeper position and a higher-frequency symbol to a shallower one only decreases (or keeps equal) the weighted path length (a standard exchange/rearrangement inequality argument: pairing the smallest frequency with the largest depth is always at least as good).

**Inductive optimality:** Given this, merging the two lowest-frequency symbols into a single combined "super-symbol" with frequency equal to their sum, and solving the smaller problem (`N-1` symbols) optimally, is equivalent to solving the original problem optimally — because the cost of the original tree equals the cost of the smaller tree plus exactly `freq(a) + freq(b)` (the one extra bit each of the two merged symbols requires beyond the depth of their combined placeholder). By induction on the number of symbols (base case: 1 or 2 symbols is trivially optimal), repeatedly applying this greedy merge produces a globally optimal tree.

**Prefix-free property:** Since every symbol is placed at a distinct leaf of a binary tree, and codes are read as root-to-leaf paths, no code can be a prefix of another (a prefix relationship would require one leaf to be an ancestor of another, which is impossible in a tree where every symbol is a leaf). ∎

### Variants / Use Cases
- **Adaptive Huffman coding** → updates the tree dynamically as data streams in, without needing frequencies upfront
- **Canonical Huffman codes** → reorders codes for compact transmission of the code table itself
- **Arithmetic coding / range coding** → alternative entropy coding achieving even closer-to-optimal compression, especially for skewed distributions
- **JPEG, DEFLATE (ZIP), MP3 compression** → Huffman coding is a core component of many real-world compression formats
- **Optimal merge patterns (external sorting)** → the same greedy min-merge strategy applies to minimizing total merge cost of sorted runs
