---
title: General
---

## Array

A contiguous block of memory holding elements of the same type, indexed from $0$ to $n-1$. Because the address of any element can be computed directly from its index ($\text{base} + i \times \text{size}$), access is $O(1)$ — but inserting or deleting anywhere except the end requires shifting every element after it. A **dynamic array** (`std::vector`) wraps a fixed-size array and reallocates to double its capacity whenever it fills up, which is why `push_back` is $O(1)$ **amortized** rather than worst-case.

| Operation | Complexity |
|---|---|
| Access by index | $O(1)$ |
| Search (unsorted) | $O(n)$ |
| Search (sorted, binary search) | $O(\log n)$ |
| Insert/delete at end | $O(1)$ amortized |
| Insert/delete at arbitrary index | $O(n)$ |

Arrays are the backbone of prefix sums, sliding-window techniques, and most indexed DP tables, since contiguous memory also makes them by far the most cache-friendly structure available.

```cpp
vector<int> a = {5, 3, 8, 1};
a.push_back(9);      // O(1) amortized
a.insert(a.begin() + 1, 4); // O(n): shifts everything after index 1
int x = a[2];         // O(1)
```

**Space Complexity:** $O(n)$

## Linked List

A chain of nodes, each storing a value and a pointer to the next node (**singly linked**), or to both the next and previous nodes (**doubly linked**). Unlike an array, memory isn't contiguous, so there's no random access — reaching the $i$-th element means walking $i$ pointers from the head, $O(n)$. The payoff is that once you're already at a node, insertion and deletion there is $O(1)$, since it's just pointer rewiring rather than shifting elements. Doubly linked lists cost an extra pointer per node but allow $O(1)$ deletion given only a pointer to the node itself (no need to track its predecessor) and $O(1)$ traversal in either direction; a **circular** variant links the tail back to the head, useful for round-robin scheduling.

| Operation | Singly | Doubly |
|---|---|---|
| Access / search | $O(n)$ | $O(n)$ |
| Insert/delete at known node | $O(1)$ | $O(1)$ |
| Insert/delete at head | $O(1)$ | $O(1)$ |
| Delete given only the node (no predecessor) | requires predecessor scan, $O(n)$ | $O(1)$ |

Doubly linked lists paired with a hash table are the classic backbone of an **LRU cache** — the list keeps eviction order while the hash table gives $O(1)$ lookup of any node.

```cpp
struct Node {
    int val;
    Node *next, *prev;
    Node(int v) : val(v), next(nullptr), prev(nullptr) {}
};

void insertAfter(Node *node, int v) {
    Node *fresh = new Node(v);
    fresh->next = node->next;
    fresh->prev = node;
    if (node->next) node->next->prev = fresh;
    node->next = fresh;
}
```

**Space Complexity:** $O(n)$, plus one (singly) or two (doubly) pointers of overhead per node

## Stack

A **LIFO** (last-in, first-out) structure exposing only `push`, `pop`, and `top`, all $O(1)$ — the trade-off for that simplicity is that only the most recently inserted element is ever reachable. It can be built on top of either an array (fast, cache-friendly, occasional resize) or a linked list (no resize, one extra pointer per element); `std::stack` uses a `std::deque` as its underlying container by default.

Stacks naturally model any process with a "most recent unmatched thing" — matching brackets, undo history, and the explicit call stack used to convert recursion into iteration.

```cpp
stack<int> st;
st.push(10);
st.push(20);
st.top();  // 20
st.pop();  // removes 20
```

**Time Complexity:** $O(1)$ for push, pop, top
**Space Complexity:** $O(n)$

## Queue & Deque

A **queue** is **FIFO** (first-in, first-out): elements are pushed at the back and popped from the front, both $O(1)$ when backed by a circular buffer or linked list (a plain array would need $O(n)$ pops from the front due to shifting). A **deque** (double-ended queue) generalizes this to $O(1)$ push/pop at **both** ends; `std::deque` implements it as a sequence of fixed-size blocks with an index, giving $O(1)$ random access as well — strictly more powerful than a queue, which is why `std::queue` and `std::stack` both use `std::deque` as their default underlying container.

| Operation | Queue | Deque |
|---|---|---|
| Push/pop front | $O(1)$ | $O(1)$ |
| Push/pop back | — | $O(1)$ |
| Random access | — | $O(1)$ |

Queues drive any breadth-first traversal, while deques are the standard tool behind the "monotonic deque" technique for sliding-window minimum/maximum.

```cpp
deque<int> dq;
dq.push_back(1);
dq.push_front(2);
dq.pop_back();
dq.pop_front();
```

**Space Complexity:** $O(n)$

## Hash Table

Maps keys to values using a **hash function** that converts a key into a bucket index, giving $O(1)$ average-case insert, find, and erase — regardless of how large the table grows. Collisions (two keys hashing to the same bucket) are resolved either by **chaining** (each bucket holds a small list) or **open addressing** (probe for the next free slot); `std::unordered_map` / `std::unordered_set` use chaining internally. The average-case guarantee assumes a reasonably random hash — with a known, fixed hash function (as in most standard libraries), an adversary who knows the judge's hash implementation can craft inputs that collide into a single bucket, degrading every operation to $O(n)$ (an "anti-hash test"). The standard defense in competitive programming is to supply a custom hash that mixes in something unpredictable, such as `chrono::steady_clock`:

```cpp
struct custom_hash {
    static uint64_t splitmix64(uint64_t x) {
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }
    size_t operator()(uint64_t x) const {
        static const uint64_t seed = chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + seed);
    }
};
unordered_map<long long, int, custom_hash> safe_map;
```

**Time Complexity:** $O(1)$ average, $O(n)$ worst case per operation
**Space Complexity:** $O(n)$

## Ordered Map / Set

`std::map` and `std::set` are implemented as a **self-balancing binary search tree** (a red-black tree), which keeps keys in sorted order at the cost of $O(\log n)$ for every insert, find, and erase — slower than a hash table's average case, but with two abilities a hash table doesn't have: iterating keys in sorted order, and answering "closest key" queries via `lower_bound` / `upper_bound` in $O(\log n)$.

| Operation | Complexity |
|---|---|
| Insert / erase / find | $O(\log n)$ |
| `lower_bound` / `upper_bound` | $O(\log n)$ |
| In-order traversal | $O(n)$ total |

Common uses: coordinate compression, maintaining a sorted multiset of "active" values (e.g. the current window in a sliding-window problem), and — via the GNU policy-tree extension `__gnu_pbds::tree` (an "order-statistics tree") — answering "how many elements are less than $x$" or "what's the $k$-th smallest element" in $O(\log n)$, which plain `std::set` cannot do.

```cpp
map<int, int> freq;
freq[5]++;
auto it = freq.lower_bound(3); // first key >= 3
```

**Space Complexity:** $O(n)$

## Heap / Priority Queue

A **complete binary tree** (every level full except possibly the last, filled left to right) stored implicitly in an array, satisfying the heap property: in a min-heap, every parent is $\le$ its children (max-heap: $\ge$). Completeness means the tree's height is always $O(\log n)$, so push and pop — which each involve moving one element up or down a root-to-leaf path — are both $O(\log n)$; peeking at the minimum (or maximum) is $O(1)$ since it always sits at the root. Building a heap from $n$ existing elements is $O(n)$, not $O(n \log n)$, because most nodes near the bottom need little to no adjustment.

`std::priority_queue` is a max-heap by default; pass `greater<int>` as the comparator for a min-heap.

```cpp
priority_queue<int> maxHeap;
priority_queue<int, vector<int>, greater<int>> minHeap;
maxHeap.push(5); maxHeap.push(1); maxHeap.push(9);
maxHeap.top(); // 9
```

Priority queues are the go-to structure whenever you repeatedly need "the current best/smallest/largest item" — Dijkstra's shortest path and merging $k$ sorted lists both lean on exactly this.

**Time Complexity:** $O(\log n)$ push/pop, $O(1)$ peek, $O(n)$ build
**Space Complexity:** $O(n)$

## Trie (Prefix Tree)

A tree specialized for storing strings, where each edge represents one character and every root-to-node path spells out a prefix shared by all strings beneath it. Inserting or searching a string of length $L$ costs $O(L)$, **independent of how many other strings are stored** — unlike scanning a list of strings one by one. Each node typically holds an array (or map) of child pointers, one per possible next character.

```cpp
struct TrieNode {
    TrieNode* children[26] = {};
    bool isEnd = false;
};

void insert(TrieNode* root, const string &word) {
    TrieNode* cur = root;
    for (char c : word) {
        int idx = c - 'a';
        if (!cur->children[idx]) cur->children[idx] = new TrieNode();
        cur = cur->children[idx];
    }
    cur->isEnd = true;
}
```

A **binary trie** over the bits of each number (instead of characters) is the standard structure for maximum-XOR-pair queries: insert every number's binary representation, then for each query greedily walk down the opposite bit at every level to maximize the XOR.

**Time Complexity:** $O(L)$ per insert/search, for a string of length $L$
**Space Complexity:** $O(\text{total characters} \times \text{alphabet size})$ worst case

## Disjoint Set Union

Maintains a partition of elements into disjoint sets, exposing `find(x)` (which set is $x$ in?) and `union(x, y)` (merge $x$'s set with $y$'s). Two optimizations make both operations run in $O(\alpha(n))$ amortized — effectively constant for any $n$ that could ever appear in practice, since $\alpha$ (the inverse Ackermann function) is at most $4$ for any remotely realistic input size:

- **Path compression:** every node visited during `find` is re-pointed directly at the root, flattening future lookups.
- **Union by rank/size:** always attach the smaller/shallower tree under the root of the larger one, keeping trees shallow to begin with.

```cpp
vector<int> parent, rank_;

int find(int x) {
    if (parent[x] != x) parent[x] = find(parent[x]); // path compression
    return parent[x];
}

void unite(int x, int y) {
    x = find(x); y = find(y);
    if (x == y) return;
    if (rank_[x] < rank_[y]) swap(x, y);
    parent[y] = x;
    if (rank_[x] == rank_[y]) rank_[x]++;
}
```

DSU is the standard structure behind Kruskal's MST, cycle detection in an undirected structure, and offline connectivity queries.

**Time Complexity:** $O(\alpha(n))$ amortized per operation
**Space Complexity:** $O(n)$

## Bitset

A fixed-size array of bits packed into machine words, where operations that would take $O(n)$ on a plain boolean array (AND, OR, XOR, count, shift, over all $n$ bits) instead run in $O(n / 64)$ — the CPU processes a full 64-bit word per instruction rather than one bit at a time. `std::bitset<N>` fixes $N$ at compile time and supports all the usual bitwise operators directly.

```cpp
bitset<1000> a, b;
a[5] = 1;
a |= b;                 // O(n / 64)
int ones = a.count();   // O(n / 64)
```

This roughly 64x constant-factor speedup is what makes bitset-optimized brute force (e.g. for subset-sum reachability, or counting pairwise string differences) fast enough in problems where the "intended" complexity is barely out of reach.

**Time Complexity:** $O(n / 64)$ per bitwise operation, for an $n$-bit bitset
**Space Complexity:** $O(n / 8)$ bytes