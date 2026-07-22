---
title: Proofs
---

## Why This Matters

Proofs are not optional for top competitors. They transform guessing into certainty. They reveal hidden edge cases, justify optimizations, and make you immune to "it works on samples" syndrome. Jiangly, tourist, and every red coder internalize proofs before they code. This document distills everything from beginner intuition to research‑level reasoning — the equivalent of reading CLRS, Kleinberg‑Tardos, Concrete Mathematics, and advanced papers, condensed for competitive programming.

---

## Foundations

### Direct Reasoning
The simplest form: trace your algorithm step by step, justifying each operation. If you sort and then binary search, the sorted order ensures the search predicate is monotonic. Always state the property your operation preserves (e.g., "after sorting, all elements ≤ current are before it").

### Proof by Contradiction
Assume the negation of what you want. Derive a logical impossibility. This is the backbone of optimality proofs. Example: to prove Dijkstra’s correctness, assume a vertex gets a wrong distance; then there must be a shorter path, contradicting the choice of the minimum unsettled vertex.

### Mathematical Induction
Works for any recursive structure. You must identify the measure (size, depth, bits, etc.). Prove the base (smallest measure). Then assume the statement holds for all smaller measures, and prove it for the current one. Strong induction is often needed when the recursion branches asymmetrically.

### Well‑Ordering Principle
Every non‑empty set of non‑negative integers has a least element. Use it to pick a minimal counterexample and derive a smaller one. This is equivalent to induction but feels more natural for certain combinatorial proofs (e.g., Euclidean algorithm termination).

### Pigeonhole Principle
If n items are placed into m boxes and n > m, at least one box has ≥2 items. Extremely useful for existence proofs and for lower bounds (e.g., any comparison sort must have at least n! leaves, hence Ω(n log n) comparisons).

### Invariants
A condition that holds at specific points (e.g., loop entry, function entry). Prove by initialization, maintenance, and termination. Invariants are not just for loops - data structures (heap property, BST order, balance factors) rely on them. Always design your operations to preserve them.

---

## Algorithm‑Family Proof Patterns

### Greedy Algorithms
The golden rule: **exchange argument**. Steps:
1. Take any optimal solution O.
2. If O already uses your greedy choice G, done.
3. Otherwise, find the first position where O differs from the greedy solution. Swap G into that position and show the objective does not worsen (use monotonicity or dominance).
4. The new solution is also optimal and now contains G. Reduce the remaining problem (remove G) and apply induction.
This proves that the greedy choice is safe. Examples: activity selection, Huffman coding, Kruskal’s MST, Dijkstra’s (with non‑negative edges), and many scheduling problems.

**Advanced**: Matroid theory provides a unifying framework. If the family of feasible sets forms a matroid (exchange property), then the greedy algorithm gives a maximum‑weight independent set. This covers MST, maximum‑weight matching in bipartite graphs (but not general) and others. Learn the matroid axioms to instantly recognize greedy problems.

### Dynamic Programming
Two essential properties:
- **Optimal substructure**: an optimal solution to the whole contains optimal solutions to its subproblems. You must prove that any suboptimal solution to a subproblem cannot lead to a globally optimal solution (e.g., shortest paths: subpaths of shortest paths are shortest).
- **Overlapping subproblems**: the same subproblems recur, allowing memoization/tabulation.

The proof template:
1. Define dp[state] clearly - what exact value does it store?
2. Write the recurrence - express dp[state] in terms of smaller states.
3. Show that the recurrence correctly models the problem’s decision process (e.g., either take this item or skip it).
4. Prove the recurrence by induction on the state space (topological order). For each state, the recurrence enumerates all possible choices, so it cannot miss the optimal one.
5. Validate base cases.

Also handle state compression, bitmask DP, digit DP, tree DP - the same reasoning applies.

### Divide and Conquer
Prove correctness by induction on the input size. The divide step splits the problem into independent subproblems, the conquer step solves them recursively, and the combine step merges their results. You need to prove that combining subproblem solutions yields the global solution. For divide‑and‑conquer DP optimizations (Knuth, divide‑and‑conquer DP), you additionally prove the monotonicity of the optimal decision point (quadrangle inequality).

### Amortized Analysis
When individual operations are expensive but rare, we bound the total cost over a sequence. Three methods:
- **Aggregate**: sum all costs explicitly and divide by n.
- **Accounting**: assign each operation a “credit” (amortized cost). Cheap operations save credit; expensive ones spend it. Show that the credit balance never goes negative.
- **Potential**: define a potential function Φ(state). The amortized cost = actual cost + Φ(after) - Φ(before). Show that Φ remains bounded (e.g., non‑negative and O(1) growth), then total amortized cost bounds total actual cost.
Common applications: dynamic arrays (doubling strategy), splay trees, DSU with path compression and union by rank, and Fibonacci heaps.

### Backtracking / Branch and Bound
Proofs here focus on pruning. Show that if a branch cannot improve the current best, it is safe to discard. This relies on a bounding function that is a true upper/lower bound. Also prove that the search space is exhaustive (the recursion visits all candidate solutions) - hence if a solution exists, it will be found.

### Randomized Algorithms
Two kinds:
- **Las Vegas**: always correct, running time is random (expected). Prove expected time using linearity of expectation and bounding the expected number of trials (e.g., randomized quicksort).
- **Monte Carlo**: may return incorrect with bounded probability. Use concentration inequalities (Chernoff, Hoeffding, Markov) to show failure probability ≤ ε. In CP, we often accept “with high probability” (w.h.p.) meaning 1 - 1/poly(n). Example: treap, randomized hashing.

### Number Theory Proofs
- **Modular arithmetic**: all proofs rely on the fact that (a mod m) ≡ a (mod m), and operations are well‑defined. For inverses, use Bézout’s identity (proved by extended Euclidean) - existence iff gcd=1.
- **Primality tests**: Miller-Rabin - proof uses Fermat’s little theorem and the fact that non‑trivial square roots of 1 mod prime are only ±1. The deterministic variant uses the Generalized Riemann Hypothesis (unproven) but for CP we rely on probabilistic.
- **Sieve algorithms**: correctness follows from marking composites when their smallest prime factor is found. The linear sieve maintains each composite is marked exactly once, proven by the property that each number is marked by its smallest prime factor.
- **Chinese Remainder Theorem**: construction proof via pairing, then induction; uniqueness by difference divisible by all moduli.
- **Lucas theorem**: for prime p, nCk mod p = product of digits’ binomials - proved via generating functions ( (1+x)^n over GF(p) ).

### Graph Algorithms
- **BFS/DFS**: BFS computes shortest paths in unweighted graphs - prove by induction on distance; DFS finds reachability and produces a spanning forest.
- **Topological sort**: existence iff DAG; proof by repeatedly removing source or via DFS finish times.
- **Strongly Connected Components (Kosaraju/Tarjan)**: prove that the condensation graph is a DAG, and that the algorithms correctly partition vertices that are mutually reachable - using invariants like lowlink values (Tarjan) or order of finish times.
- **Shortest paths**:
  - Dijkstra: proof via cut property and relaxation - once a vertex is settled, its distance is final.
  - Bellman‑Ford: invariant that after k iterations, all paths with ≤k edges are relaxed; hence after V‑1 passes, all shortest paths are found; negative cycles detected when relaxation continues.
  - Floyd‑Warshall: invariant after k iterations, dist[i][j] uses intermediate vertices only from {1..k}.
- **Minimum Spanning Tree**: cut property and cycle property. Kruskal (greedy by weight) - prove by cut property: the lightest edge crossing a cut is safe. Prim - analogous.
- **Maximum Flow**:
  - Ford‑Fulkerson: if residual graph has no augmenting path, the current flow is max (via max‑flow min‑cut theorem).
  - Dinic: prove that each BFS phase increases the level graph; number of phases O(V) for unit capacities, O(V²E) general.
- **Matching**: Hall’s theorem for bipartite matching, proof of correctness via augmenting paths (Kuhn, Hopcroft‑Karp). Blossom algorithm for general graphs - uses shrinking odd cycles, proof of correctness relies on the fact that an odd cycle can be contracted to a single vertex without changing the existence of an augmenting path.
- **2‑SAT**: SCC condensation - a variable and its negation must not be in the same SCC; assignment built by topological order. Proof via implication graph properties.

### Advanced Data Structures
- **Segment Tree with lazy propagation**: invariant that each node’s value is correct for its segment, and pending lazy tags are not pushed. Operations (range add, range query) preserve this invariant by pushing before descending and updating after recursion.
- **Fenwick Tree**: prove that prefix sum query uses the binary representation to cover the range exactly, and update affects exactly the necessary nodes.
- **Sparse Table**: idempotent operation (like min, max, gcd) allows overlapping intervals, proven by covering query [l,r] with two disjoint intervals of length 2^k.
- **Treap / Randomized BST**: priority heap property + BST order - proof of expected O(log n) height via randomized priorities (analysis similar to quicksort).
- **Splay Tree**: amortized O(log n) via potential function - the proof uses the access lemma and a careful choice of potential (logarithm of subtree sizes).
- **Link‑Cut Tree**: maintains a forest of rooted trees with path operations; correctness relies on representing paths as splay trees, and the expose operation maintaining the represented tree.
- **Segment Tree Beats**: maintains max/min and second max; proof of complexity via potential function - the potential decreases by at least 1 for each “beating” operation.

### Geometry Algorithms
- **Convex Hull (Graham scan / Andrew)**: maintain a stack with the invariant that the last three points form a left turn. When a right turn occurs, pop - this ensures the final polygon is convex. Proof by orientation tests.
- **Rotating Calipers**: use the monotonicity of the angle to prove that each antipodal pair is visited O(n) times.
- **Sweep Line algorithms**: sort events and maintain an active set; proof that the sweep processes events in the correct order, and each event triggers the needed updates without missing intersections.

### Combinatorial Counting
- **Bijective proofs**: show a one‑to‑one correspondence between two sets (e.g., Catalan numbers count Dyck paths and binary trees).
- **Double counting**: count the same quantity in two ways to derive identities.
- **Generating functions**: prove by formal power series - operations like convolution correspond to combinatorial choices.
- **Burnside’s lemma / Pólya counting**: group actions on colorings - number of orbits = average fixed points; proof via counting pairs (g, coloring fixed by g).

### Game Theory
- **Impartial games and Sprague‑Grundy**: define mex of Grundy numbers of moves. Prove that the XOR of Grundy numbers determines the winner (Bouton’s theorem) by induction on the game tree.
- **Minimax**: evaluate game tree; proof that the minimax value is the optimal outcome under optimal play - by induction on depth.
- **Alpha‑beta pruning**: proves that pruning does not change the result because skipped branches cannot affect the value (they are outside the window).

### Lower Bounds
- **Adversary arguments**: construct an adversary that answers comparisons to force many operations. For sorting, adversary maintains a partial order and answers comparisons to keep the number of linear extensions large until enough comparisons are made.
- **Decision tree model**: any comparison‑based algorithm corresponds to a binary tree, whose leaves are permutations. There are n! permutations, so height ≥ log₂(n!) = Ω(n log n).
- **Information‑theoretic lower bounds**: if the output has K possibilities, any algorithm needs at least log₂ K bits - applies to many decision problems.

### NP‑Hardness Reductions
Though rare in CP, understand them for advanced topics. To prove problem A is hard, reduce a known hard problem B to A (polynomial‑time reduction). Preserve yes/no answers. For optimization problems, show approximation hardness via gap reductions.

---
## Resources

**Books**
- **CLRS (Introduction to Algorithms)** – read every proof in sorting, graphs, DP, amortized analysis, Fibonacci heaps, MST; do proof‑based exercises.
- **Algorithm Design (Kleinberg & Tardos)** – best for greedy exchange arguments and DP proof patterns; each chapter has a “Proof of Correctness” section – study thoroughly.
- **Concrete Mathematics (Graham, Knuth, Patashnik)** – builds mathematical toolkit: recurrences, sums, binomial identities, generating functions – essential for combinatorial proofs.
- **Proofs from THE BOOK (Aigner & Ziegler)** – trains elegant reasoning in number theory, geometry, and combinatorics.
- **The Art of Computer Programming (Knuth)** – rigorous and deep; invaluable for lower bounds and combinatorial algorithms.

**Online Courses & Lecture Series**
- **MIT 6.006 (Introduction to Algorithms)** and **MIT 6.046 (Design and Analysis of Algorithms)** – video lectures with clear proofs for fundamental and advanced topics (randomized algorithms, amortized analysis, NP‑completeness).
- **Stanford CS161** – algorithms course with proof emphasis.

**Websites with Proofs**
- **CP‑Algorithms** – each article includes a correctness proof; read them.
- **USACO Guide** – Platinum sections discuss proof techniques.

**Problem Sets**
- **CLRS exercises** and **Kleinberg‑Tardos exercises** – many proof‑based; do all that ask “prove” or “show”.
- **LeetCode / Codeforces editorials** – never skip the proof part; compare your mental proof with the editorial.
- **Project Euler** – many problems require number‑theoretic proofs before coding.

**Research Papers**
- **Tarjan’s papers** (splay trees, DSU, graph algorithms) – original amortized analyses.
- **Frederickson & Johnson** – lower bound proofs.
- **Randomized Algorithms (Motwani & Raghavan)** – probabilistic proof techniques.
---

## Developing Your Proof Instinct

1. **For every problem**, after you design a solution, force yourself to write a 3‑sentence proof sketch. Do it in your head or on paper.
2. **When reading editorials**, focus on the “Why this works” paragraph - highlight it.
3. **Pair up** with a friend and explain your proof aloud. Teaching exposes gaps.
4. **Practice edge‑case reasoning**: always ask “what if n=1, n=0, all equal, negative, huge?” - your proof should cover them.
5. **Study known counterexamples**: learn why common greedy algorithms fail (e.g., coin change with non‑canonical denominations) - this strengthens your skepticism.
