---
title: Algorithmic Paradigms
---

- An algorithmic paradigm is a high-level, reusable strategy for designing algorithms, distinct from any single concrete algorithm (Quicksort is an algorithm; Divide and Conquer is the paradigm behind it).
- Paradigms dictate how a problem is decomposed, how the solution space is explored, and how correctness and efficiency are argued.
- Paradigms are not mutually exclusive — most non-trivial algorithms blend several (e.g. Kruskal's MST is Greedy + Union-Find; A\* is State Space Search + Heuristic).
- The field grew alongside [[complexity-theory|Complexity Theory]]: sorting motivated Divide and Conquer, optimization motivated Greedy and Dynamic Programming, and NP-Hardness motivated Approximation, Randomization, and Heuristics.

**Core questions driving paradigm choice**

- Does the problem have optimal substructure (an optimal solution is built from optimal solutions to subproblems)?
- Do subproblems overlap, or are they independent?
- Does a locally optimal choice provably lead to a globally optimal one (the greedy-choice property)?
- Is the problem NP-Hard, and if so, is an exact small-$n$ solution, an approximation, or a heuristic acceptable?
- Does the algorithm see the whole input at once, or must it commit to decisions online, one item at a time?
- Does the input fit in memory, or must it be processed in a bounded number of passes?

**Problem-structure signals and the paradigm they suggest**

- Optimal substructure + overlapping subproblems → Dynamic Programming / Memoization.
- Optimal substructure + independent subproblems → Divide and Conquer.
- Greedy-choice property (provable via exchange argument or matroid structure) → Greedy Algorithms.
- Problem reduces to one smaller instance of itself → Decrease and Conquer.
- A change of representation simplifies the problem → Transform and Conquer.
- Constraints prune most of the search tree → Backtracking.
- An optimization problem with a computable bound on partial solutions → Branch and Bound.
- Small search space (all subsets/permutations feasible) → Brute Force.
- $n$ too large for $O(2^n)$ but small enough for $O(2^{n/2})$ → Meet in the Middle.
- NP-Hard, only small/medium $n$ needed exactly → Branch and Bound, Meet in the Middle, or Parameterized (FPT) Algorithms.
- NP-Hard, large $n$, near-optimal acceptable with guarantees → Approximation Algorithms.
- NP-Hard, large $n$, no guarantees required → Heuristic / Metaheuristic Algorithms.
- Input arrives sequentially with no knowledge of the future → Online Algorithms.
- Input too large to store, one or few passes allowed → Streaming Algorithms.
- Computation must be split across cores or machines → Distributed and Parallel Algorithm Design.

## Exhaustive Search Paradigms

- **Brute Force**
  - Core idea: enumerate every candidate solution and test each for validity; correctness is guaranteed by exhaustiveness.
  - Mechanism: iterate over all subsets ($O(2^n)$), all permutations ($O(n!)$), all pairs ($O(n^2)$), or all starting positions.
  - When to use: small input sizes, correctness baselines for validating faster algorithms, when no better structure is apparent.
  - Trade-offs: trivial to implement and verify, but scales catastrophically; almost never the final production solution.

  ```cpp
  bool subsetSum(vector<int>& arr, int target) {
      int n = arr.size();
      for (int mask = 0; mask < (1 << n); mask++) {
          int sum = 0;
          for (int i = 0; i < n; i++) if (mask & (1 << i)) sum += arr[i];
          if (sum == target) return true;
      }
      return false;
  }
  ```

- **Backtracking**
  - Core idea: incrementally build a candidate solution and abandon ("backtrack" from) a branch as soon as it is known to be invalid.
  - Mechanism: depth-first search over partial solutions with constraint checks at every node; effective pruning turns exponential enumeration into a tractable search in practice.
  - Iconic problems: N-Queens, Sudoku, graph coloring, permutation and subset generation with constraints.
  - When to use: constraint satisfaction problems, combinatorial search, puzzles.
  - Trade-offs: worst case remains exponential, but real-world pruning (constraint propagation, ordering heuristics) is often dramatic.

  ```cpp
  void solveNQueens(vector<vector<string>>& result, vector<string>& board, int row, int n) {
      if (row == n) { result.push_back(board); return; }
      for (int col = 0; col < n; col++) {
          if (isSafe(board, row, col, n)) {
              board[row][col] = 'Q';
              solveNQueens(result, board, row + 1, n);
              board[row][col] = '.';
          }
      }
  }
  ```

- **Branch and Bound**
  - Core idea: extend backtracking to optimization problems by maintaining a bound on the best achievable value from any partial solution.
  - Mechanism: explore a search tree; compute an upper (max) or lower (min) bound per node; prune any branch whose bound cannot beat the current best.
  - Iconic problems: Traveling Salesman (exact), 0/1 Knapsack (exact), Integer Programming.
  - When to use: NP-Hard optimization problems where an exact answer is required and a reasonably tight bound function exists.
  - Trade-offs: worst-case exponential; bound quality determines practical performance — a weak bound degenerates to brute force.

  ```cpp
  int bound(Node u, int n, int W, vector<int>& p, vector<int>& w) {
      if (u.weight >= W) return 0;
      int profitBound = u.profit, j = u.level + 1, totalWeight = u.weight;
      while (j < n && totalWeight + w[j] <= W) { totalWeight += w[j]; profitBound += p[j]; j++; }
      if (j < n) profitBound += (W - totalWeight) * p[j] / w[j];
      return profitBound;
  }
  ```

- **Meet in the Middle**
  - Core idea: split the input into two halves, solve each half independently, then combine results — reducing $O(2^n)$ to $O(2^{n/2})$.
  - Mechanism: enumerate all subset sums (or states) of each half, sort one half, and binary-search or two-pointer across the other.
  - Iconic problems: Subset Sum for $n \leq 40$, Knapsack variants, 4-SUM.
  - When to use: exponential problems where $n$ is too large for full brute force but small enough that $2^{n/2}$ is tractable.
  - Trade-offs: needs the problem to decompose into two independently combinable halves; extra space for the enumerated half-solutions.

  ```cpp
  bool meetInMiddle(vector<int>& arr, int target) {
      int n = arr.size();
      vector<int> left(arr.begin(), arr.begin() + n / 2), right(arr.begin() + n / 2, arr.end());
      vector<int> sumsLeft = generateSums(left), sumsRight = generateSums(right);
      sort(sumsRight.begin(), sumsRight.end());
      for (int s : sumsLeft) if (binary_search(sumsRight.begin(), sumsRight.end(), target - s)) return true;
      return false;
  }
  ```

- **State Space Search**
  - Core idea: model the problem as states and transitions, then search for a path from a start state to a goal state.
  - Mechanism: BFS (shortest path, unweighted), DFS (any path / exhaustive), Iterative Deepening DFS — IDDFS (memory-bounded depth-first), A\* (heuristic-guided best-first search using $f(n) = g(n) + h(n)$), bidirectional search.
  - Iconic problems: pathfinding, 15-puzzle, Rubik's Cube, game-playing AI, robot motion planning.
  - When to use: any problem whose solution is a sequence of moves or decisions through a well-defined state graph.
  - Trade-offs: state-space explosion is the main enemy; heuristics (in A\*) or transposition tables mitigate it.

  ```cpp
  bool bfs(vector<vector<int>>& graph, int start, int target) {
      queue<int> q; vector<bool> visited(graph.size(), false);
      q.push(start); visited[start] = true;
      while (!q.empty()) {
          int node = q.front(); q.pop();
          if (node == target) return true;
          for (int nb : graph[node]) if (!visited[nb]) { visited[nb] = true; q.push(nb); }
      }
      return false;
  }
  ```

- **Bitmask Enumeration and Bit Manipulation Techniques**
  - Core idea: represent subsets, states, or configurations as integers and use bitwise operations for fast enumeration and transition.
  - Mechanism: iterate `mask` from $0$ to $2^n-1$ to enumerate subsets; iterate submasks of a mask in $O(3^n)$ total via `for (int sub = mask; sub; sub = (sub-1) & mask)`; use `__builtin_popcount`, XOR tricks, and Gray codes for efficient state transitions.
  - Iconic problems: Traveling Salesman via DP over subsets (Held–Karp), Sum over Subsets (SOS) DP, assignment problems, bitmask DP on small $n$ (typically $n \leq 20$–$25$).
  - When to use: DP or search where the state includes "which elements have been used" and $n$ is small.
  - Trade-offs: exponential in $n$ by construction; fast constant factors make it practical exactly where asymptotically "better" methods are overkill.
  ```cpp
  // dp[mask] = best value using exactly the elements in mask
  for (int mask = 0; mask < (1 << n); mask++)
      for (int i = 0; i < n; i++)
          if (mask & (1 << i))
              dp[mask] = max(dp[mask], dp[mask ^ (1 << i)] + cost(i, mask));
  ```

## Divide-Based Paradigms

- **Divide and Conquer**
  - Core idea: split a problem into independent subproblems, solve each recursively, and combine the results.
  - Mechanism: divide → conquer (recurse) → combine; running time typically follows $T(n) = aT(n/b) + f(n)$, solvable by the Master Theorem.
  - Iconic problems: Merge Sort ($\Theta(n \log n)$), Quick Sort, Closest Pair of Points, Karatsuba Multiplication, Fast Fourier Transform, binary exponentiation.
  - When to use: sorting, searching, computational geometry, numerical algorithms — anywhere subproblems don't share state.
  - Trade-offs: recursion overhead and stack depth; requires subproblems to be genuinely independent, or the paradigm degenerates into redundant recomputation.

  ```cpp
  void mergeSort(vector<int>& arr, int left, int right) {
      if (left >= right) return;
      int mid = left + (right - left) / 2;
      mergeSort(arr, left, mid);
      mergeSort(arr, mid + 1, right);
      merge(arr, left, mid, right);
  }
  ```

- **Decrease and Conquer**
  - Core idea: reduce the problem to a single smaller instance of itself (unlike Divide and Conquer's multiple subproblems).
  - Mechanism: decrease by a constant (Insertion Sort), decrease by a constant factor (Binary Search), or decrease by a variable amount (Euclid's Algorithm).
  - Iconic problems: Binary Search ($O(\log n)$), Insertion Sort, Euclid's GCD, Selection Sort, topological sort via DFS.
  - When to use: problems that shrink cleanly toward a base case with a single active recursive/iterative thread.
  - Trade-offs: simpler recurrences and lower overhead than Divide and Conquer, but less parallelizable since only one subproblem is active at a time.

  ```cpp
  int binarySearch(vector<int>& arr, int target) {
      int left = 0, right = arr.size() - 1;
      while (left <= right) {
          int mid = left + (right - left) / 2;
          if (arr[mid] == target) return mid;
          else if (arr[mid] < target) left = mid + 1;
          else right = mid - 1;
      }
      return -1;
  }
  ```

- **Transform and Conquer**
  - Core idea: change the problem's representation, structure, or domain so that solving the transformed version is easier or faster than solving the original directly.
  - Mechanism: instance simplification (presorting), representation change (Heapify), or problem reduction (mapping one problem onto another with a known algorithm).
  - Iconic problems: Heap Sort (array → heap), coordinate compression, Gaussian elimination (matrix → row echelon form), reducing a graph problem into a flow problem.
  - When to use: preprocessing steps, or when a known efficient algorithm exists for a related representation.
  - Trade-offs: transformation cost must be amortized by savings in the solving phase; adds an extra correctness burden — the transform must be answer-preserving.
  ```cpp
  void heapSort(vector<int>& arr) {
      make_heap(arr.begin(), arr.end());
      sort_heap(arr.begin(), arr.end());
  }
  ```

## Optimization Paradigms

- **Greedy Algorithms**
  - Core idea: make the locally optimal choice at every step, trusting (and proving) that local optimality composes into global optimality.
  - Mechanism: sort or prioritize choices by some criterion, then commit to each choice irrevocably; correctness typically proven via an exchange argument or matroid structure.
  - Iconic problems: Activity Selection, Huffman Coding, Kruskal's / Prim's MST, Dijkstra's Algorithm (non-negative weights), fractional Knapsack.
  - When to use: problems exhibiting the greedy-choice property, often (but not always) when the underlying structure is a matroid.
  - Trade-offs: fast and simple when it works, but silently wrong when the greedy-choice property doesn't hold (0/1 Knapsack is not solvable greedily).

  ```cpp
  vector<pair<int,int>> activitySelection(vector<pair<int,int>>& activities) {
      sort(activities.begin(), activities.end(), [](auto& a, auto& b) { return a.second < b.second; });
      vector<pair<int,int>> result; int lastFinish = -1;
      for (auto& [start, finish] : activities)
          if (start >= lastFinish) { result.push_back({start, finish}); lastFinish = finish; }
      return result;
  }
  ```

- **Matroid-Based Greedy**
  - Core idea: a greedy algorithm is provably optimal whenever the underlying combinatorial structure is a matroid (a set system satisfying the exchange property).
  - Mechanism: sort elements by weight, then greedily add an element if it keeps the current set independent within the matroid.
  - Iconic problems: MST (graphic matroid), maximum weight forest, scheduling with deadlines (transversal matroid).
  - When to use: as a certificate that a greedy strategy is correct rather than merely empirically good, before implementing it.
  - Trade-offs: recognizing matroid structure is itself nontrivial; not every "greedy-feeling" problem is actually a matroid problem.

- **Dynamic Programming**
  - Core idea: solve optimization/counting problems with optimal substructure and overlapping subproblems by solving each distinct subproblem once and reusing the result.
  - Mechanism: define the state, define the transition (recurrence), fix base cases, choose iteration order (bottom-up table) or recursion with caching (top-down / Memoization).
  - Iconic problems: 0/1 Knapsack, Longest Common Subsequence, Edit Distance, Matrix Chain Multiplication, digit DP, bitmask DP (Held–Karp TSP), tree DP.
  - When to use: optimization over sequences, subsets, intervals, or trees where naive recursion recomputes the same subproblem exponentially many times.
  - Trade-offs: state design is the crux of the paradigm — a poorly chosen state either misses transitions or blows up in dimensionality; space is often optimizable by keeping only the last one or two DP layers.

  ```cpp
  int knapsack(vector<int>& weights, vector<int>& values, int W) {
      int n = weights.size();
      vector<vector<int>> dp(n + 1, vector<int>(W + 1, 0));
      for (int i = 1; i <= n; i++)
          for (int w = 1; w <= W; w++)
              dp[i][w] = weights[i-1] <= w ? max(dp[i-1][w], dp[i-1][w-weights[i-1]] + values[i-1]) : dp[i-1][w];
      return dp[n][W];
  }
  ```

- **Memoization**
  - Core idea: the top-down realization of Dynamic Programming — write the natural recursion, then cache each subproblem's result the first time it is computed.
  - Mechanism: a hash map or array keyed by state; check the cache before recursing, populate it after.
  - Iconic problems: Fibonacci, Edit Distance, tree DP, any DP whose recursive structure is more natural than its iteration order.
  - When to use: sparse state spaces (only a small fraction of all possible states are actually reached), or when the bottom-up iteration order is awkward to determine.
  - Trade-offs: recursion overhead (function calls, stack depth) versus the simplicity of directly mirroring the recurrence.

  ```cpp
  unordered_map<int, long long> memo;
  long long fib(int n) {
      if (n <= 1) return n;
      if (memo.count(n)) return memo[n];
      return memo[n] = fib(n - 1) + fib(n - 2);
  }
  ```

- **Network Flow Paradigm**
  - Core idea: model a problem as flow through a capacitated directed graph and exploit the Max-Flow Min-Cut theorem to solve seemingly unrelated combinatorial problems.
  - Mechanism: construct a flow network (source, sink, capacities); run Ford–Fulkerson / Edmonds–Karp ($O(VE^2)$) or Dinic's Algorithm ($O(V^2E)$); read the answer off the max flow or min cut.
  - Iconic problems: Bipartite Matching, Project Selection, image segmentation (min-cut), edge/vertex disjoint paths, circulation with lower bounds.
  - When to use: whenever a problem can be phrased as "how much can flow from A to B" or "what's the cheapest way to separate A from B."
  - Trade-offs: the hardest part is usually the modeling (constructing the right graph), not the flow algorithm itself.

## Approximate and Probabilistic Paradigms

- **Randomized Algorithms**
  - Core idea: use random choices during execution to avoid worst-case adversarial inputs, simplify design, or achieve good expected performance.
  - Mechanism: **Las Vegas** algorithms are always correct with randomized running time (randomized QuickSort); **Monte Carlo** algorithms have bounded running time with randomized correctness (Miller–Rabin primality).
  - Iconic problems: randomized QuickSort ($O(n \log n)$ expected), randomized hashing, Karger's Min-Cut algorithm, skip lists.
  - When to use: to defeat adversarial worst-case inputs, or when a simple randomized algorithm is far easier to design than a matching deterministic one.
  - Trade-offs: correctness or running time becomes probabilistic — error must be quantified and, if needed, amplified by repetition. See [[complexity-theory|Complexity Theory]] for BPP, RP, ZPP.

  ```cpp
  int partition(vector<int>& arr, int low, int high) {
      int pivotIdx = low + rand() % (high - low + 1);
      swap(arr[pivotIdx], arr[high]);
      // standard Lomuto partition using arr[high] as pivot
  }
  ```

- **Approximation Algorithms**
  - Core idea: for NP-Hard optimization problems, trade guaranteed optimality for a provable closeness bound, computed in polynomial time.
  - Mechanism: approximation ratio $\alpha$ — for minimization, returned cost $\leq \alpha \cdot OPT$; for maximization, returned value $\geq \alpha \cdot OPT$.
  - Iconic problems: Vertex Cover ($2$-approximation), metric TSP ($3/2$-approximation via Christofides), Set Cover ($O(\log n)$-approximation), Max Cut ($0.878$-approximation, Goemans–Williamson).
  - When to use: NP-Hard problems at scale where an exact algorithm is infeasible but a certified quality bound is required.
  - Trade-offs: designing a good approximation is often as hard as the original problem's theory; see [[complexity-theory|Complexity Theory]] for PTAS/FPTAS and inapproximability results.

  ```cpp
  vector<int> approximateVertexCover(vector<vector<int>>& graph) {
      vector<bool> covered(graph.size(), false);
      vector<int> cover;
      for (int u = 0; u < (int)graph.size(); u++)
          if (!covered[u])
              for (int v : graph[u])
                  if (!covered[v]) { covered[u] = covered[v] = true; cover.push_back(u); cover.push_back(v); break; }
      return cover;
  }
  ```

- **Heuristic and Metaheuristic Algorithms**
  - Core idea: practical strategies that find good (not necessarily optimal, not necessarily bounded) solutions quickly, when exact and approximation methods are too slow or lack useful guarantees.
  - Mechanism: **local search** (Hill Climbing), **simulated annealing** (accept worse moves with decreasing probability to escape local optima), **genetic algorithms** (selection, crossover, mutation over a population), **tabu search** (forbid recently visited states).
  - Iconic problems: large-scale TSP, VLSI placement, hyperparameter tuning, scheduling with complex real-world constraints.
  - When to use: large-scale NP-Hard problems where even approximation algorithms are unavailable or too slow, and "good enough" is acceptable.
  - Trade-offs: no guarantees on solution quality or running time; tuning (cooling schedule, population size, mutation rate) is itself an engineering problem.

  ```cpp
  void geneticAlgorithm() {
      auto population = initializePopulation();
      while (!terminationCondition()) {
          auto fitness = evaluatePopulation(population);
          auto parents = selectParents(population, fitness);
          auto offspring = crossover(parents);
          mutate(offspring);
          population = replace(population, offspring);
      }
  }
  ```

- **Parameterized (Fixed-Parameter Tractable) Algorithms**
  - Core idea: for NP-Hard problems, isolate a parameter $k$ (often solution size) and design an algorithm running in $f(k) \cdot n^{O(1)}$ time — polynomial in $n$, with the combinatorial explosion confined to $k$.
  - Mechanism: kernelization (polynomial-time preprocessing shrinking the instance to size $f(k)$), bounded search trees, color-coding, treewidth-based DP.
  - Iconic problems: Vertex Cover ($O(1.2738^k + kn)$), $k$-Path, Feedback Vertex Set.
  - When to use: NP-Hard problems where real-world instances have a small parameter even though $n$ is large.
  - Trade-offs: not every NP-Hard problem is FPT (see the W-hierarchy in [[complexity-theory|Complexity Theory]]); the "$f(k)$" constant can be astronomical even when the exponent is technically polynomial.

## Query and Range Paradigms

- **Search Paradigm**
  - Core idea: efficiently locate a target (or extremum) in structured data by repeatedly narrowing a candidate range.
  - Mechanism: Binary Search on sorted data ($O(\log n)$), Ternary Search on unimodal functions ($O(\log n)$), Interpolation Search on uniformly distributed data ($O(\log \log n)$ expected), binary search on the answer for monotonic predicates.
  - Iconic problems: finding an element, finding the minimum of a convex function, "smallest $x$ such that predicate($x$) is true."
  - When to use: any monotonic or unimodal structure over an ordered domain.
  - Trade-offs: requires exploitable structure (sortedness, unimodality); doesn't apply to unordered or non-monotonic data.

  ```cpp
  int ternarySearch(vector<int>& arr, int target) {
      int low = 0, high = arr.size() - 1;
      while (high - low >= 3) {
          int m1 = low + (high - low) / 3, m2 = high - (high - low) / 3;
          if (arr[m1] == target) return m1;
          if (arr[m2] == target) return m2;
          if (target < arr[m1]) high = m1 - 1;
          else if (target > arr[m2]) low = m2 + 1;
          else { low = m1 + 1; high = m2 - 1; }
      }
      for (int i = low; i <= high; i++) if (arr[i] == target) return i;
      return -1;
  }
  ```

- **Sliding Window and Two Pointers**
  - Core idea: maintain a contiguous window (or a pair of pointers) over the input and incrementally adjust its boundaries instead of recomputing from scratch, turning an $O(n^2)$ scan into $O(n)$.
  - Mechanism: expand the right pointer to include new elements; shrink the left pointer while a constraint is violated; maintain a running aggregate (sum, count, frequency map) incrementally.
  - Iconic problems: longest substring without repeating characters, minimum window substring, maximum sum subarray of fixed size, two-sum on a sorted array, three-sum via fixing one element and two-pointering the rest.
  - When to use: contiguous-range or pairwise problems over arrays/strings where the window's "cost" changes incrementally as boundaries move.
  - Trade-offs: only works when the monotonicity assumption holds (shrinking never needs to re-grow past where it already was); doesn't apply to non-contiguous or non-monotonic constraints.

  ```cpp
  int minWindowSum(vector<int>& arr, int target) {
      int n = arr.size(), sum = 0, left = 0, best = INT_MAX;
      for (int right = 0; right < n; right++) {
          sum += arr[right];
          while (sum >= target) { best = min(best, right - left + 1); sum -= arr[left++]; }
      }
      return best == INT_MAX ? 0 : best;
  }
  ```

- **Sqrt Decomposition and Mo's Algorithm**
  - Core idea: partition an array or the query set into blocks of size roughly $\sqrt{n}$ to balance update and query costs, or to batch-order offline range queries for amortized efficiency.
  - Mechanism: **Sqrt Decomposition** precomputes per-block aggregates so range queries and point updates both run in $O(\sqrt{n})$. **Mo's Algorithm** sorts offline range queries by block index (and endpoint, for even blocks) so moving between consecutive queries touches $O(n\sqrt{q})$ elements in total, instead of recomputing each query from scratch.
  - Iconic problems: range sum/min with updates, offline "distinct elements in range $[l,r]$" queries, range mode queries.
  - When to use: range queries hard to maintain with a simpler structure (Fenwick/segment tree) but where all queries are known in advance (offline).
  - Trade-offs: $O(\sqrt{n})$ is asymptotically worse than $O(\log n)$ structures where those apply, but Mo's algorithm handles query types (like "distinct count") that segment trees cannot easily support; strictly offline.

- **Persistent Data Structures**
  - Core idea: preserve every previous version of a data structure as updates are made, allowing queries against any historical version.
  - Mechanism: **Fully persistent** structures allow modifying any past version; **partially persistent** structures allow querying any past version but only updating the latest. Implemented via path copying (persistent segment trees, persistent tries), where each update creates $O(\log n)$ new nodes instead of copying the whole structure.
  - Iconic problems: $k$-th smallest in a range (persistent segment tree / "merge sort tree"), version-controlled arrays, undo/redo systems, offline queries reduced to "value at time $t$."
  - When to use: whenever a problem requires querying or comparing multiple historical states of a structure, not just the current one.
  - Trade-offs: extra $O(\log n)$ space per update; more complex to implement correctly than the non-persistent counterpart.

- **Amortized and Incremental Algorithms**
  - Core idea: bound the _total_ cost of a sequence of operations, even when individual operations can occasionally be expensive, by showing expensive operations are rare enough to be "paid for" by cheap ones.
  - Mechanism: aggregate analysis (bound total cost directly), the accounting method (assign amortized costs/credits to operations), the potential method (define a potential function $\Phi$ over the structure's state and bound $\text{cost} + \Delta\Phi$).
  - Iconic problems: dynamic array push_back ($O(1)$ amortized despite occasional $O(n)$ resizes), Union-Find with path compression ($O(\alpha(n))$ amortized), splay trees, incremental/decremental connectivity.
  - When to use: analyzing data structures where costs are inherently uneven across operations, to get a tight bound instead of an overly pessimistic worst-case-per-operation bound.
  - Trade-offs: amortized bounds guarantee total cost over a sequence, not any single operation — unsuitable for hard real-time constraints where one expensive operation is unacceptable.

## Data-Scale Paradigms

- **Online Algorithms**
  - Core idea: process input piece by piece, committing to each decision immediately, with no knowledge of future input.
  - Mechanism: performance measured by the **competitive ratio** — the worst-case ratio between the online algorithm's cost and the optimal offline algorithm's cost, had it seen the whole input in advance.
  - Iconic problems: LRU/FIFO caching (paging), online bipartite matching, the ski-rental problem, online scheduling.
  - When to use: caching, real-time scheduling, financial trading — any setting where decisions cannot be deferred until all data is seen.
  - Trade-offs: optimality is fundamentally limited by the lack of foresight; the goal shifts from "optimal" to "provably competitive."

  ```cpp
  int lruCache(vector<int>& requests, int capacity) {
      unordered_set<int> cache; list<int> order; int hits = 0;
      for (int page : requests) {
          if (cache.count(page)) { hits++; order.remove(page); order.push_front(page); }
          else {
              if ((int)cache.size() == capacity) { cache.erase(order.back()); order.pop_back(); }
              cache.insert(page); order.push_front(page);
          }
      }
      return hits;
  }
  ```

- **Streaming Algorithms**
  - Core idea: process massive data streams using memory far smaller than the stream itself, typically in a single pass, often trading exactness for probabilistic guarantees.
  - Mechanism: reservoir sampling (uniform random sample of an unknown-length stream), Count-Min Sketch (approximate frequency counting), HyperLogLog (approximate distinct-element counting), Bloom filters (approximate set membership).
  - Iconic problems: heavy hitters in network traffic, distinct IP counting, real-time analytics dashboards.
  - When to use: data too large to store in full — big data pipelines, network monitoring, telemetry.
  - Trade-offs: results are approximate with quantifiable error bounds; algorithms must be carefully designed since data cannot be revisited. Related to sublinear algorithms and communication complexity — see [[complexity-theory|Complexity Theory]].

  ```cpp
  vector<int> reservoirSample(istream& data, int k) {
      vector<int> reservoir(k);
      int i = 0; for (; i < k && (data >> reservoir[i]); i++);
      int n = k, value;
      while (data >> value) { n++; int j = rand() % n; if (j < k) reservoir[j] = value; }
      return reservoir;
  }
  ```

- **Sublinear and Property-Testing Algorithms**
  - Core idea: decide (with high probability) whether the input has a global property, or is "far" from having it, by examining only a sublinear fraction of the input — sometimes $O(1)$ or $O(\log n)$ queries regardless of $n$.
  - Mechanism: random sampling combined with local consistency checks; a property tester accepts inputs with the property with high probability and rejects inputs "$\varepsilon$-far" from it with high probability, with no guarantee in between.
  - Iconic problems: testing if a list is sorted, testing bipartiteness of a graph via sampling, testing linearity of a function.
  - When to use: massive datasets where even reading the full input once is prohibitive, and an approximate "close enough" decision suffices.
  - Trade-offs: says nothing about inputs near the property boundary; requires a well-defined distance metric ("$\varepsilon$-far") to be meaningful.

- **Cache-Oblivious and External-Memory Algorithms**
  - Core idea: design algorithms efficient in a two-level memory hierarchy (fast cache, slow disk/RAM) either by explicitly accounting for block transfers (external memory model) or, more elegantly, without any knowledge of cache/block size at all (cache-oblivious model).
  - Mechanism: measure cost in I/Os (block transfers of size $B$) rather than raw operation count; recursive divide-and-conquer layouts (van Emde Boas layout for trees) achieve optimal cache behavior at every level of the memory hierarchy simultaneously.
  - Iconic problems: external merge sort ($O(\frac{n}{B}\log_{M/B}\frac{n}{B})$ I/Os), cache-oblivious B-trees, matrix multiplication with blocked/recursive layouts.
  - When to use: datasets far larger than RAM, or performance-critical code where cache-miss cost dominates raw instruction count.
  - Trade-offs: external-memory algorithms need explicit tuning to hardware parameters ($B$, $M$); cache-oblivious algorithms avoid this but are harder to design and reason about.

- **Distributed and Parallel Algorithm Design**
  - Core idea: decompose computation across multiple processors (parallel, shared or accessible memory) or multiple machines (distributed, message-passing over a network).
  - Mechanism: data-parallel models (same operation on different data — SIMD, GPU kernels), task-parallel models (different functions on different processors), the MapReduce paradigm (map, shuffle, reduce over a cluster), consensus and coordination protocols for distributed correctness.
  - Iconic problems: parallel prefix sum, parallel sorting networks, distributed shortest paths, large-scale machine learning training.
  - When to use: large-scale computation where single-threaded, single-machine performance is insufficient.
  - Trade-offs: Amdahl's Law bounds achievable speedup by the sequential fraction of the program; communication overhead, load balancing, and fault tolerance (distributed case) introduce complexity absent from sequential design.
  ```cpp
  #pragma omp parallel for reduction(+:sum)
  for (int i = 0; i < n; i++) sum += array[i];
  ```

## Game-Theoretic Paradigms

- **Minimax and Alpha-Beta Pruning**
  - Core idea: in a two-player zero-sum game, recursively evaluate positions assuming each player plays optimally — the maximizing player picks the move maximizing the outcome, the minimizing player picks the move minimizing it.
  - Mechanism: build the game tree to some depth (or fully, for small games); Alpha-Beta Pruning cuts off branches that cannot influence the final decision, reducing the effective branching factor without changing the result.
  - Iconic problems: Tic-Tac-Toe, Chess/Go engines (with heuristic evaluation at bounded depth), Nim-like combinatorial games.
  - When to use: adversarial two-player perfect-information games.
  - Trade-offs: full game trees are feasible only for small games; large games require heuristic evaluation functions and bounded-depth search, sacrificing exactness for tractability.

- **Game DP — Sprague–Grundy Theory**
  - Core idea: reduce combinatorial games (under the normal play convention) to Nim via Grundy numbers (nimbers), enabling $O(1)$ analysis of compound games.
  - Mechanism: compute the Grundy number of a position as $\text{mex}$ (minimum excludant) of the Grundy numbers of positions reachable from it; a position is losing for the player to move iff its Grundy number is $0$; combine independent games via XOR of their Grundy numbers (Sprague–Grundy Theorem).
  - Iconic problems: Nim, Grundy's Game, Turning Turtles, most impartial combinatorial games seen in competitive programming.
  - When to use: impartial games (both players have the same moves available) under normal play (last player to move wins).
  - Trade-offs: applies only to impartial games; partisan games (like Chess) require different tools from combinatorial game theory more broadly.

## Paradigm Comparison Table

| Paradigm                      | Core Idea                         | Typical Complexity                         | Best For                                | Limitations                         |
| ----------------------------- | --------------------------------- | ------------------------------------------ | --------------------------------------- | ----------------------------------- |
| Brute Force                   | Enumerate all solutions           | $O(2^n)$, $O(n!)$                          | Small inputs, baselines                 | Infeasible at scale                 |
| Backtracking                  | Pruned DFS with constraints       | Exponential, often much better in practice | CSPs, puzzles                           | Worst case still exponential        |
| Branch and Bound              | Backtracking + bounding           | Exponential, bound-dependent               | NP-Hard exact optimization              | Needs a tight bound                 |
| Meet in the Middle            | Split, enumerate, combine         | $O(2^{n/2})$                               | Exponential problems, $n \lesssim 40$   | Needs two independent halves        |
| State Space Search            | Explore states via BFS/DFS/A\*    | Exponential (state-dependent)              | Pathfinding, puzzles, AI                | State-space explosion               |
| Bitmask Enumeration           | Subsets as integers               | $O(2^n)$ to $O(3^n)$                       | Small-$n$ subset DP                     | Limited to small $n$                |
| Divide and Conquer            | Split, solve independently, merge | $O(n \log n)$ typical                      | Sorting, searching, FFT                 | Requires independence               |
| Decrease and Conquer          | Reduce to one smaller instance    | $O(\log n)$ to $O(n^2)$                    | Binary search, GCD                      | Limited applicability               |
| Transform and Conquer         | Change representation             | Depends on transform                       | Preprocessing, heaps                    | Transform overhead                  |
| Greedy                        | Locally optimal choices           | $O(n \log n)$ typical                      | Matroid-structured problems             | Correctness must be proven          |
| Dynamic Programming           | Table of overlapping subproblems  | Polynomial (state-dependent)               | Sequence/interval/tree optimization     | State design is critical            |
| Memoization                   | Recursive DP with caching         | Polynomial                                 | Sparse or irregular state spaces        | Recursion overhead                  |
| Network Flow                  | Max-flow / min-cut modeling       | $O(V^2E)$ (Dinic)                          | Matching, cuts, disjoint paths          | Modeling is the hard part           |
| Randomized                    | Random choices                    | Expected polynomial                        | Avoiding adversarial inputs             | Probabilistic guarantees            |
| Approximation                 | Near-optimal with a ratio bound   | Polynomial                                 | NP-Hard problems, guarantees needed     | Ratio limited by hardness           |
| Heuristic/Metaheuristic       | Practical, no guarantees          | Polynomial (per iteration)                 | Large-scale, real-world optimization    | No correctness/quality bound        |
| Parameterized (FPT)           | Exponential confined to $k$       | $O(f(k) \cdot n^{O(1)})$                   | NP-Hard with small structural parameter | Not all problems are FPT            |
| Search Paradigm               | Narrow candidate range            | $O(\log n)$                                | Sorted/unimodal/monotonic data          | Needs ordered structure             |
| Sliding Window / Two Pointers | Incremental window adjustment     | $O(n)$                                     | Contiguous-range array/string problems  | Needs monotonic cost                |
| Sqrt Decomposition / Mo's     | Block-based batching              | $O((n+q)\sqrt n)$                          | Offline range queries                   | Offline only                        |
| Persistent Structures         | Preserve all past versions        | $O(\log n)$ extra per update               | Versioned/historical queries            | Extra space per update              |
| Amortized/Incremental         | Bound cost over a sequence        | Varies (amortized)                         | Dynamic structures, rare expensive ops  | No single-operation guarantee       |
| Online Algorithms             | Decisions without future          | Varies, competitive ratio                  | Caching, scheduling                     | Bounded by lack of foresight        |
| Streaming Algorithms          | One/few-pass, limited memory      | Sublinear space                            | Big data, network monitoring            | Approximate results                 |
| Sublinear/Property Testing    | Query a small sample              | $O(1)$ to $O(\log n)$ queries              | Massive datasets, approximate decisions | No guarantee near the boundary      |
| Cache-Oblivious/External      | Optimize for memory hierarchy     | $O(\frac{n}{B}\log_{M/B}\frac{n}{B})$ I/Os | Datasets larger than RAM                | Hardware-aware tuning (external)    |
| Distributed/Parallel          | Concurrent execution              | Bounded by Amdahl's Law                    | HPC, ML training, large-scale data      | Communication/coordination overhead |
| Minimax/Alpha-Beta            | Adversarial optimal play          | $O(b^d)$, $O(b^{d/2})$ pruned              | Two-player perfect-information games    | Bounded depth needed at scale       |
| Game DP (Sprague–Grundy)      | Reduce to Nim via Grundy numbers  | Polynomial in game states                  | Impartial combinatorial games           | Impartial games only                |

## Problem-to-Paradigm Mapping

- **Sorting and order statistics** → Divide and Conquer (Merge/Quick Sort), Decrease and Conquer (Insertion Sort), Transform and Conquer (Heap Sort).
- **Graph shortest paths / connectivity** → Greedy (Dijkstra, Prim), Dynamic Programming (Bellman–Ford, Floyd–Warshall), State Space Search (BFS/A\*).
- **Graph matching / cuts** → Network Flow Paradigm.
- **Sequence optimization (LCS, edit distance, alignment)** → Dynamic Programming / Memoization.
- **Subset/combinatorial search, $n \leq 20$** → Bitmask Enumeration, Brute Force.
- **Subset/combinatorial search, $20 < n \leq 40$** → Meet in the Middle.
- **NP-Hard exact for small instances** → Branch and Bound, Parameterized Algorithms.
- **NP-Hard at scale, guarantees required** → Approximation Algorithms.
- **NP-Hard at scale, guarantees not required** → Heuristic/Metaheuristic Algorithms.
- **Range queries with updates** → Segment Tree / Fenwick Tree (Transform and Conquer), Sqrt Decomposition for irregular query types.
- **Range queries, all known in advance, no updates** → Mo's Algorithm.
- **Range queries against historical versions** → Persistent Data Structures.
- **Contiguous subarray/substring problems** → Sliding Window / Two Pointers.
- **Real-time decisions, unknown future** → Online Algorithms.
- **Data too large for memory** → Streaming Algorithms, Sublinear/Property Testing.
- **Data too large for RAM but on disk** → Cache-Oblivious / External-Memory Algorithms.
- **Two-player games** → Minimax/Alpha-Beta (partisan), Sprague–Grundy (impartial).
- **Large-scale computation across machines** → Distributed and Parallel Algorithm Design.

## Choosing the Right Paradigm

- For small inputs or as a correctness baseline, Brute Force is always acceptable and often underrated as a sanity check.
- Constraint satisfaction problems naturally suit Backtracking; optimization problems with a computable bound suit Branch and Bound.
- Sorting and searching problems are well served by Divide and Conquer or Decrease and Conquer.
- Optimization with overlapping subproblems calls for Dynamic Programming; optimization with the greedy-choice property is best solved greedily — but the property must be proven, not assumed.
- Transform and Conquer can simplify problems through a clever change of representation before applying a standard technique.
- State Space Search is essential for AI-style problems; Meet in the Middle is invaluable for exponential problems with $n \leq 40$; Parameterized Algorithms help when $n$ is large but a structural parameter $k$ is small.
- Range and window problems over arrays should default to Sliding Window / Two Pointers or a Fenwick/segment tree before reaching for anything more exotic; Mo's Algorithm and Sqrt Decomposition are the fallback when the query type resists a simpler structure.
- Online and Streaming Algorithms are critical when input arrives over time or data is too large to store in full.
- Distributed and Parallel Algorithms are necessary once single-machine, single-threaded performance is exhausted.
- When exact solutions are too expensive, choose Randomized, Approximation, or Heuristic approaches — roughly in that order of preference, since each gives progressively weaker guarantees.
- In competitive programming, identifying the paradigm from the constraints ($n$, time limit, memory limit) is usually the first and most valuable step; see [[complexity-theory|Complexity Theory]] for the asymptotic vocabulary this reasoning depends on.

## Common Pitfalls

- Assuming the greedy-choice property holds without proving it (0/1 Knapsack is the classic counterexample to naive greedy reasoning).
- Applying Dynamic Programming without first verifying overlapping subproblems exist — if subproblems are independent, plain Divide and Conquer is simpler and just as fast.
- Using Backtracking without meaningful pruning, which silently degenerates into Brute Force.
- Reaching for Meet in the Middle when $n$ is small enough for direct DP, or too large even for $2^{n/2}$.
- Treating Approximation ratios as if they were error percentages — a $2$-approximation means the answer is at most twice optimal, not "within 2%."
- Forgetting that amortized bounds describe a sequence of operations, not any individual one — unsuitable where a single slow operation is unacceptable (hard real-time systems).
- Applying Mo's Algorithm to problems requiring online (interactive) answers — it is inherently an offline technique.
- Ignoring cache/memory-hierarchy effects in performance-critical code, where an asymptotically "better" algorithm can lose to a cache-friendlier one in practice.

## Useful Resources

**Books**

- Cormen, T.H., Leiserson, C.E., Rivest, R.L. & Stein, C. - _Introduction to Algorithms_ (CLRS)
- Kleinberg, J. & Tardos, E. - _Algorithm Design_
- Skiena, S.S. - _The Algorithm Design Manual_
- Sedgewick, R. & Wayne, K. - _Algorithms_
- Dasgupta, S., Papadimitriou, C. & Vazirani, U. - _Algorithms_
- Halim, S. & Halim, F. - _Competitive Programming 4_
- Laaksonen, A. - _Guide to Competitive Programming_

**Online resources**

- CP-Algorithms - https://cp-algorithms.com
- USACO Guide - https://usaco.guide
- VisuAlgo (algorithm visualizations) - https://visualgo.net
- Competitive Programmer's Handbook (free PDF) - Antti Laaksonen
- GeeksforGeeks Algorithms section

**GitHub repositories**

- [cp-algorithms/cp-algorithms](https://github.com/cp-algorithms/cp-algorithms)
- [kth-competitive-programming/kactl](https://github.com/kth-competitive-programming/kactl)
- [TheAlgorithms/C-Plus-Plus](https://github.com/TheAlgorithms/C-Plus-Plus)
- [keon/algorithms](https://github.com/keon/algorithms)
