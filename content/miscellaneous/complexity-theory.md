---
title: Complexity Theory
---

- Complexity Theory is the branch of theoretical computer science studying the intrinsic difficulty of computational problems.
- Unlike Computability Theory (asks _can this be solved at all?_), Complexity Theory asks _how efficiently can it be solved?_
- It quantifies the minimum time and space required by any algorithm solving a problem, as a function of input size $n$.
- It establishes lower bounds (no faster algorithm exists) and upper bounds (an algorithm exists).
- It classifies problems into complexity classes by resource requirements, and studies reductions to compare problem difficulty and identify the hardest problems in a class (completeness).

**Core questions driving the field**

- Can every problem whose solution is easily verified (NP) also be easily solved (P)?
- Are there problems that are inherently exponential, requiring time that grows astronomically with input size?
- How do randomness, quantum mechanics, or parallel computation change what is efficiently solvable?
- Can unconditional lower bounds be proven, or must the field rely on unproven conjectures?

**Models of computation**

- **Deterministic Turing Machine (DTM)** - standard model; single computation path per input; time = number of transitions, space = number of tape cells used.
- **Nondeterministic Turing Machine (NTM)** - multiple computation paths, accepts if at least one path accepts; defines NP (polynomial time on an NTM).
- **Randomized Turing Machine** - equipped with a random bit source; captures probabilistic algorithms (BPP, RP, ZPP).
- **Quantum Turing Machine (QTM) / Quantum Circuit Model** - uses qubits and unitary transformations; defines BQP (bounded-error quantum polynomial time).
- **Boolean Circuits** - acyclic networks of logic gates; measured by circuit size (gate count) and depth (longest path); used for circuit complexity and lower bounds.

**Resource measures**

- **Time complexity** - number of elementary operations executed.
- **Space complexity** - number of memory cells used.
- **Alternations** - number of quantifier switches in a logical definition (Polynomial Hierarchy).
- **Communication** - number of bits exchanged between parties (Communication Complexity).
- **Circuit size and depth** - number of gates or layers in a Boolean circuit.

## Asymptotic Notation

- **$O(f(n))$ - Big-O (upper bound)**
  - Definition: $\exists\, c>0,\ n_0>0 : \forall n \geq n_0,\ T(n) \leq c \cdot f(n)$
  - Meaning: worst-case growth, not necessarily tight.
  - Example: Bubble Sort is $O(n^2)$.
- **$\Omega(f(n))$ - Big-Omega (lower bound)**
  - Definition: $\exists\, c>0,\ n_0>0 : \forall n \geq n_0,\ T(n) \geq c \cdot f(n)$
  - Meaning: best-case or inherent difficulty.
  - Example: comparison sorting is $\Omega(n \log n)$.
- **$\Theta(f(n))$ - Big-Theta (tight bound)**
  - Definition: $\exists\, c_1, c_2 > 0,\ n_0 : c_1 \cdot f(n) \leq T(n) \leq c_2 \cdot f(n)$
  - Meaning: upper and lower bounds match.
  - Example: Merge Sort is $\Theta(n \log n)$.
- **$o(f(n))$ - little-o (strict upper bound)**
  - Definition: $\forall c>0,\ \exists n_0 : \forall n \geq n_0,\ T(n) < c \cdot f(n)$
  - Meaning: $T$ grows strictly slower than $f$.
  - Example: $2n = o(n^2)$.
- **$\omega(f(n))$ - little-omega (strict lower bound)**
  - Definition: $\forall c>0,\ \exists n_0 : \forall n \geq n_0,\ T(n) > c \cdot f(n)$
  - Meaning: $T$ grows strictly faster than $f$.
  - Example: $2n^2 = \omega(n)$.

**Common complexity hierarchy (increasing order)**

$$O(1) \subset O(\log n) \subset O(n) \subset O(n \log n) \subset O(n^2) \subset O(n^3) \subset O(2^n) \subset O(n!)$$

- Polynomial time: any $O(n^k)$ for constant $k$.
- Exponential time: $2^{n^\varepsilon}$ for $\varepsilon > 0$, or more broadly $2^{\text{poly}(n)}$.

**Space complexity examples**

- In-place algorithms use $O(1)$ extra space (Heap Sort, Insertion Sort).
- Logarithmic space: $O(\log n)$ (binary search, some graph traversal).
- Linear space: $O(n)$ (Merge Sort, BFS with visited array).
- Polynomial space: $O(n^k)$, defines PSPACE.
- Exponential space: $O(2^n)$, used in brute-force SAT solvers storing all assignments.

## Complexity Classes - Core Taxonomy

- **P - Polynomial Time**
  - Machine/resource: DTM in $O(n^k)$ time.
  - Solvable efficiently in deterministic polynomial time; considered "tractable."
  - Iconic problems: sorting, shortest path, MST, 2-SAT, matching, primality (AKS).
- **NP - Nondeterministic Polynomial Time**
  - Machine/resource: NTM in $O(n^k)$ time.
  - Solutions can be verified in polynomial time by a DTM.
  - Iconic problems: SAT, Hamiltonian Cycle, TSP (decision), Clique, Vertex Cover.
- **NPC - NP-Complete**
  - Hardest problems in NP; all of NP reduces to them. If any NPC problem is in P, then P = NP.
  - Iconic problems: 3-SAT, Clique, Vertex Cover, Set Cover, Knapsack (decision).
- **NPH - NP-Hard**
  - At least as hard as any NP problem (may not itself be in NP); typically optimization versions.
  - Iconic problems: TSP minimization, Integer Programming, the Halting Problem (if unconstrained).
- **PSPACE - Polynomial Space**
  - Machine/resource: DTM using $O(n^k)$ space.
  - Solvable with polynomial memory (may take exponential time); contains NP.
  - Iconic problems: QBF, generalized games.
- **PSPACE-C - PSPACE-Complete**
  - Hardest problems in PSPACE.
  - Iconic problems: QBF, Generalized Geography, Sokoban, planning with full information.
- **EXPTIME - Exponential Time**
  - Machine/resource: DTM in $O(2^{n^k})$ time.
  - Contains PSPACE.
  - Iconic problems: generalized Chess/Go/Shogi on $n \times n$ boards.
- **EXPTIME-C - EXPTIME-Complete**
  - Hardest problems in EXPTIME.
  - Iconic problems: generalized Chess ($n \times n$), regular expression equivalence (with exponentiation).
- **NEXPTIME - Nondeterministic Exponential Time**
  - Machine/resource: NTM in $O(2^{n^k})$ time.
  - Contains EXPTIME.
  - Iconic problems: succinct 3-SAT, succinct Circuit SAT.
- **BPP - Bounded-error Probabilistic Polynomial Time**
  - Randomized DTM with error $\leq 1/3$.
  - Efficient poly-time algorithms using randomness, two-sided error.
  - Iconic problems: Miller–Rabin primality test, Polynomial Identity Testing.
- **RP - Randomized Polynomial Time (one-sided error)**
  - If the answer is NO, always correct; if YES, correct with probability $\geq 1/2$.
  - Iconic problems: some primality tests, polynomial identity.
- **ZPP - Zero-error Probabilistic Polynomial Time**
  - Randomized DTM (Las Vegas); always correct, expected polynomial time.
  - Iconic problems: randomized QuickSort (expected), certain factoring algorithms.
- **BQP - Bounded-error Quantum Polynomial Time**
  - Quantum circuit in poly-time, error $\leq 1/3$.
  - Iconic problems: Shor's factoring algorithm, discrete logarithm, Grover's search (quadratic speedup).
- **QMA - Quantum Merlin-Arthur**
  - Quantum verification; quantum analogue of NP.
  - Iconic problems: Local Hamiltonians, QSAT.
- **PH - Polynomial Hierarchy**
  - Alternating TMs with $O(1)$ alternations.
  - Generalization of NP and co-NP with alternating quantifiers; contains $\Sigma_i$, $\Pi_i$.
  - Iconic problems: QBF with bounded quantifier alternations.
- **#P - Counting Problems**
  - Counts the number of accepting paths of an NTM (not just existence).
  - Iconic problems: #SAT, the Permanent.
- **PP - Probabilistic Polynomial Time (unbounded error)**
  - Randomized TM with success probability $> 1/2$; very broad, contains NP.
  - Iconic problems: Majority-SAT, approximate counting.

## Inclusion Relationships Between Classes

- $P \subseteq NP \subseteq PSPACE \subseteq EXPTIME \subseteq NEXPTIME \subseteq EXPSPACE$
- $P \subseteq BPP \subseteq BQP \subseteq PSPACE$
- $NP \subseteq PP \subseteq PSPACE$
- $NP \subseteq PH \subseteq PSPACE$
- $P \neq PSPACE$ and $PSPACE \neq EXPTIME$ are known (by diagonalization).
- $P = NP$ is open, and widely believed false.
- $NP \neq \text{co-NP}$ is open.
- $BPP = P$ is believed but unproven (derandomization results exist under plausible assumptions).
- $BQP \neq P$ is believed, due to Shor's algorithm (factoring is not known to be in P).
- $EXPTIME \neq NEXPTIME$ is open.
- $PSPACE \neq NEXPTIME$ is known (since $PSPACE \subseteq EXPTIME \subset NEXPTIME$ if $EXPTIME \neq NEXPTIME$).

## Reductions and Completeness

- **Polynomial-Time Reduction (Karp Reduction)** - $A \leq_p B$ if there exists a polynomial-time computable function $f$ such that $x \in A \iff f(x) \in B$. Maps instances of $A$ to instances of $B$, preserving the answer; used to define NP-Completeness.
- **Cook Reduction (Turing Reduction)** - $A \leq_T B$ if $A$ can be solved in polynomial time using an oracle for $B$. More general than Karp reduction; allows multiple oracle calls.
- **Log-Space Reduction** - $A \leq_L B$ if the reduction uses only $O(\log n)$ space. Finer-grained; used for classes like NL.

**Completeness definitions**

- A problem $C$ is **NP-Complete** if (1) $C \in NP$, and (2) every problem in NP reduces to $C$ via $\leq_p$.
- A problem $H$ is **NP-Hard** if every problem in NP reduces to $H$ ($H$ need not be in NP).
- Similar definitions apply to other classes: PSPACE-Complete, EXPTIME-Complete, etc.

**Proving NP-Completeness, step by step**

- Show the problem is in NP: a certificate can be verified in polynomial time.
- Choose a known NP-Complete problem $K$ (typically 3-SAT or SAT).
- Construct a polynomial-time reduction from $K$ to the target problem $C$.
- Prove correctness: $K$ has a solution iff $C$ has a solution for the constructed instance - this establishes $C$ is NP-Hard.
- Since $C \in NP$ already, $C$ is NP-Complete.

**Common reduction sources and targets**

- **SAT / 3-SAT** → logic, constraints, circuit problems → targets: Circuit SAT, NAE-SAT, Max-SAT, graph coloring, scheduling.
- **Clique** → graph existence problems → targets: Independent Set, Vertex Cover, Dominating Set.
- **Vertex Cover** → covering problems in graphs → targets: Set Cover, Hitting Set, Dominating Set.
- **Hamiltonian Cycle** → path and tour problems → targets: TSP, Longest Path, Steiner Tree.
- **Set Cover** → set systems and covering → targets: Hitting Set, Dominating Set, Facility Location.
- **Subset Sum / Partition** → number and arithmetic problems → targets: Knapsack, Scheduling (3-Partition), Bin Packing.
- **3-Dimensional Matching** → matching and packing with triple constraints → targets: Set Packing, Exact Cover, Partition into Triangles.

**Recommended reduction paths**

- Logic problems → use SAT or 3-SAT.
- Graph problems → use Clique, Vertex Cover, or Hamiltonian Cycle.
- Set problems → use Set Cover or Exact Cover.
- Number problems → use Partition or Subset Sum.
- Geometry problems → use Euclidean TSP or Geometric Set Cover.
- Scheduling problems → use 3-Partition or Job Shop Scheduling.

## Tractable Problems - Class P

**Sorting and searching**

- Comparison Sort - $O(n \log n)$
- Counting Sort - $O(n+k)$
- Radix Sort - $O(d(n+k))$
- Bucket Sort - $O(n+k)$
- Binary Search - $O(\log n)$
- Dictionary Lookup - $O(\log n)$
- Order Statistics (selection) - $O(n)$
- Range Search - $O(\log n + k)$

**Graph algorithms**

- Breadth-First Search (BFS), Depth-First Search (DFS), Topological Sort.
- Strongly Connected Components (Tarjan/Kosaraju), Bridge Finding / Articulation Points.
- Dijkstra's Algorithm - $O(E \log V)$
- Bellman–Ford - $O(VE)$
- Floyd–Warshall - $O(V^3)$
- Maximum Flow (Dinic) - $O(EV^2)$; Push-Relabel Algorithm
- Minimum Spanning Tree (Kruskal/Prim)
- Bipartite Matching (Hopcroft–Karp), General Matching (Edmonds' Blossom)
- Minimum Cost Flow
- Assignment Problem (Hungarian) - $O(n^3)$

**String algorithms**

- Knuth–Morris–Pratt (KMP) - $O(n+m)$
- Z-Algorithm - $O(n)$
- Rabin–Karp - $O(n+m)$ expected
- Suffix Array - $O(n \log n)$
- Suffix Automaton - $O(n)$
- Suffix Tree - $O(n)$
- Aho–Corasick - $O(n+m+z)$
- Trie Operations - $O(\text{length})$
- Longest Common Substring - $O(n)$

**Computational geometry**

- Convex Hull (Graham Scan / Andrew's Monotone Chain) - $O(n \log n)$
- Closest Pair (Divide and Conquer) - $O(n \log n)$
- Line Sweep Algorithms - $O(n \log n)$
- Half-Plane Intersection - $O(n \log n)$
- Point in Polygon (Ray Casting / Winding Number) - $O(n)$

**Number theory and algebra**

- GCD / Extended GCD - $O(\log n)$
- Modular Exponentiation - $O(\log n)$
- Miller–Rabin Primality - $O(k \log^3 n)$
- Pollard's Rho - $O(n^{1/4})$
- Chinese Remainder Theorem
- Sieve of Eratosthenes - $O(n \log \log n)$
- Gaussian Elimination - $O(n^3)$
- Matrix Multiplication - $O(n^{2.37286})$
- Fast Fourier Transform - $O(n \log n)$
- Number Theoretic Transform - $O(n \log n)$
- Linear Programming (interior point) - polynomial
- Polynomial Multiplication (FFT)

**Other tractable problems**

- 2-SAT - linear time (implication graph + SCC)
- Transportation Problem - Network Simplex / Hungarian
- Union-Find (Disjoint Set) - almost $O(1)$ with path compression + union by rank
- Lowest Common Ancestor (LCA) - $O(1)$ with preprocessing (Euler tour + RMQ)
- Heavy-Light Decomposition - $O(\log^2 n)$ per query
- Segment Tree / Fenwick Tree - $O(\log n)$ per operation
- Range Minimum Query (RMQ) - $O(1)$ with sparse table or Cartesian tree

## NP Problems - Verifiable in Polynomial Time

- A problem is in NP if a proposed solution (certificate) can be verified in polynomial time by a DTM.
- NP includes all problems in P and all NP-Complete problems.

**Notable NP problems (decision versions), by domain**

- Logic & satisfiability: SAT, 3-SAT, 2-SAT, 1-in-3 SAT, Exact 3-SAT, Circuit SAT, NAE-SAT
- Graph problems: Hamiltonian Cycle, Hamiltonian Path, Clique, Vertex Cover, Independent Set, Graph Coloring, Dominating Set
- Set & subset problems: Set Cover, Exact Cover, Hitting Set, Set Packing, Set Splitting, 3-Dimensional Matching, Maximum Coverage
- Number & arithmetic: Subset Sum, Partition, 3-Partition, Equal Sum Partition, 0-1 Integer Programming, Quadratic Programming, Linear Ordering
- Other: Knapsack (Decision), Bin Packing, Longest Path, Steiner Tree, Chromatic Number

## NP-Complete Problems - The Hardest Problems in NP

- Every problem in NP reduces to each NP-Complete problem in polynomial time.
- No polynomial-time algorithm is known for any of them.
- If any NP-Complete problem were solved in polynomial time, then P = NP.

**Logic and satisfiability**

- SAT (Cook–Levin theorem: first problem proven NP-Complete).
- Variants, all NPC: 3-SAT, 1-in-3 SAT, Exact 3-SAT, NAE-SAT, Circuit SAT, Max-SAT (decision), Weighted Max-SAT, Boolean Formula Minimization.

**Graph theory**

- Clique & covers: Clique / Maximum Clique, Independent Set (maximum), Vertex Cover (minimum), Edge Dominating Set, Clique Cover / Partition.
- Domination & coloring: Dominating Set (minimum), Total Dominating Set, Connected Dominating Set, Graph Coloring (k-color), Chromatic Number (min), k-Colorability, Domatic Number, Perfect Code, Power Domination.
- Paths & cycles: Hamiltonian Cycle, Hamiltonian Path, Longest Path (length $k$), Steiner Tree (minimum), Feedback Vertex Set, Feedback Arc Set, Disjoint Paths (k-disjoint).
- Layout & cuts: Cutwidth, Bandwidth, Minimum Linear Arrangement, Minimum Bisection, Max Cut, Maximum Acyclic Subgraph.
- Subgraphs & structure: Subgraph Isomorphism, Induced Subgraph Isomorphism, Minimum Fill-In, Chordal Completion, Planarity Testing Variants, Minimum Equivalent Digraph, Graph Motif, Efficient Dominating Set, Rainbow Connection.

**Set and subset problems**

- Set Cover (minimum) - fewest sets covering all elements.
- Hitting Set (minimum) - dual of Set Cover.
- Exact Cover (by 3-sets) - disjoint sets exactly covering all elements.
- Set Packing - maximum pairwise-disjoint sets.
- Set Splitting - 2-color elements so no set is monochromatic.
- Maximum Coverage (decision form), Minimum Test Set, k-Set Packing.

**Number and arithmetic problems**

- Subset Sum - subset summing to a target.
- Partition - split into two equal-sum subsets.
- 3-Partition - split a multiset into equal-sum triples.
- Equal Sum Partition (variant).
- Knapsack (decision) - value $\geq V$ with weight $\leq W$.
- 0-1 Integer Programming (decision), Quadratic Programming (nonconvex), Quadratic Assignment Problem, Linear Ordering Problem, Number Partitioning variants, Simultaneous Diophantine Approximation.

**Scheduling**

- Job Shop / Open Shop / Flow Shop Scheduling - makespan $\leq K$? ($m$ machines, $n$ jobs).
- Multiprocessor Scheduling - makespan $\leq K$? (identical/uniform machines).
- Scheduling with Deadlines, Resource-Constrained Project Scheduling, Nurse Scheduling, Course/Exam Timetabling - feasibility questions.
- Makespan Minimization (preemption allowed?), Precedence-Constrained Scheduling, Batch / Parallel Machine Scheduling, Interval Scheduling with Conflicts, Sequencing with Setup Times.

**Packing and covering**

- Bin Packing (decision: fits into $\leq k$ bins?), Bin Packing with Conflicts, Strip Packing, 2D/3D Packing, Cutting Stock, Multiple Knapsack, Quadratic Knapsack, Partition into Triangles, Rectangle/Circle/Shelf Packing.

**Routing and location**

- Routing: Vehicle Routing Problem (VRP), Capacitated VRP, Traveling Repairman Problem, Rural Postman Problem, Chinese Postman (mixed graph), Orienteering Problem, Prize-Collecting TSP, Bottleneck TSP.
- Location: Facility Location (Uncapacitated/Capacitated), p-Median, p-Center, k-Center, k-Median, Hub Location, Location-Routing Problem.

**Geometric**

- Euclidean TSP, Rectilinear TSP, Rectilinear Steiner Tree, Euclidean Steiner Tree, Geometric Set Cover, Maximum Independent Set in Geometric Graphs, Geometric Hitting Set / Dominating Set, Art Gallery Problem, Guard Placement, Visibility Graph Problems.

**String and sequence**

- Closest String, Closest Substring, Longest Common Subsequence (multiple sequences), Shortest Common Supersequence (multiple), Bounded Post Correspondence Problem, Multiple Sequence Alignment, Consensus String, DNA Mapping, Sequence Assembly variants, Grammar-Based Minimization.

**Games and puzzles (generalized to $n \times n$ boards)**

- All NPC: Generalized Sudoku, Kakuro, Nonogram (Picross), Minesweeper Consistency/Solving, Slither Link, Hashiwokakero, Heyawake, Nurikabe, Masyu, Fillomino, Battleship, Numberlink, Shakashaka, Light Up (Akari), LITS, Tatamibari, Tentai Show, Corral/Bag Puzzle, Sliding Puzzles with Obstacles, Edge-Matching Puzzles, Instant Insanity, $n$-Queens Completion, Generalized FreeCell, Peg Solitaire, Rubik's Cube Optimal Solving, SameGame, Tetris (optimization), Clickomania, Lemmings variants.

**Karp's 21 NP-Complete problems (1972)**

- SAT, 3-SAT, Clique, Vertex Cover, Independent Set, Hamiltonian Cycle, Hamiltonian Path, Traveling Salesman (Decision), Set Cover, Exact Cover, Hitting Set, Partition, Subset Sum, Knapsack (Decision), 3-Dimensional Matching, Dominating Set, Graph Coloring, Chromatic Number, Clique Cover, Feedback Vertex Set, Feedback Arc Set, Steiner Tree.

## NP-Hard Problems - Optimization Versions and Beyond

- At least as hard as any problem in NP, but need not be in NP themselves.
- Most are optimization versions of NP-Complete decision problems.
- No polynomial-time algorithm is known for any of them.

**Optimization versions**

- Traveling Salesman Problem (minimum tour length), Maximum Clique, Maximum Independent Set, Minimum Vertex Cover, Chromatic Number (minimum colors), Minimum Dominating Set, Minimum Set Cover, Minimum Steiner Tree, Minimum Feedback Vertex Set, Maximum Cut, Minimum Cutwidth / Bandwidth / Linear Arrangement / Bisection.

**Routing and logistics optimization**

- Vehicle Routing Problem, Capacitated VRP, Traveling Repairman Problem, Rural Postman Problem, Chinese Postman (mixed), Orienteering Problem, Prize-Collecting TSP, Bottleneck TSP, Steiner Traveling Salesman, Minimum Latency Problem, Traveling Tournament Problem.

**Scheduling optimization**

- Job Shop / Open Shop / Flow Shop / Multiprocessor Scheduling - minimize makespan.
- Scheduling with Release Dates - minimize makespan.
- Weighted Completion Time - minimize $\sum w_j C_j$.
- Precedence-Constrained / Batch / Parallel Machine Scheduling - minimize makespan.
- Berth Allocation - minimize service time.
- Airport Gate Assignment - minimize delays.
- Crew Scheduling - minimize crew cost.

**Location and facility optimization**

- Facility Location (Uncapacitated/Capacitated) - minimize opening + assignment costs.
- p-Median - minimize sum of distances to $p$ centers.
- p-Center - minimize maximum distance to $p$ centers.
- k-Center, k-Median - similar objectives.
- Hub Location - minimize routing through hubs.

**Packing and cutting optimization**

- Bin Packing (minimize bins), Strip Packing (minimize height), Cutting Stock (minimize waste), Knapsack (maximize value), Multiple Knapsack, Quadratic Knapsack, 2D/3D Packing, Shelf Packing.

**Algebra and arithmetic optimization**

- General Integer Programming, Mixed Integer Programming, Quadratic Assignment Problem, Sparse PCA, Tensor Rank, Matrix Completion, Minimum Rank Problem, Boolean Matrix Factorization, Simultaneous Diophantine Approximation.

**Data mining and learning optimization**

- Optimal Decision Tree - minimize tree size/depth.
- Bayesian Network Structure Learning - maximize score (BIC, AIC).
- Sparse Feature Selection - minimize error with $\ell_0$ penalty.
- Rule List Minimization - minimize rules while maintaining accuracy.
- Boolean Function Learning - minimize formula size.
- Sparse Regression with Subset Constraints - minimize residual with limited features.
- Clustering with Combinatorial Constraints - minimize within-cluster distances.
- Minimum Description Length Optimization.

**Computational biology and chemistry optimization**

- Protein Folding (many formulations), RNA Secondary Structure with Constraints, Genome Assembly (optimization variants), Haplotyping, Phylogeny Reconstruction, Maximum Parsimony, Molecular Docking, Hartree–Fock Global Optimization, Ising Model and Spin Glass Optimization.

**VLSI and hardware optimization**

- VLSI Layout, Circuit Layout, Wire Routing, Floorplanning, Physical Design Partitioning, Circuit Minimization, Boolean Formula Minimization, Register Allocation, Frequency Assignment.

**Graph optimization**

- Maximum Common Subgraph / Induced Subgraph, Minimum Clique Cover, Cluster Editing / Deletion, Correlation Clustering, Split Graph Completion, Threshold Graph Editing, Perfect Graph Recognition, Minimum Fill-In, Minimum Chain Decomposition, Maximum Weight Induced Forest, Minimum Cycle Cover, Group Steiner Tree, Directed Steiner Tree, Prize-Collecting Steiner Tree, Survivable Network Design.

**Other NP-Hard optimization problems**

- Quadratic Assignment (general), Minimum Linear Arrangement, Graph Embedding Variants, Minimum Equivalent Digraph, Disjoint Paths with optimization, k-Dominating Set Optimization, Rainbow Connection Optimization, Symbolic Regression, Optimal Control with Integer Constraints.

## PSPACE-Complete and EXPTIME-Complete Problems

**PSPACE-Complete**

- Quantified Boolean Formula (QBF) / True QBF - the canonical PSPACE-Complete problem; generalizes SAT with $\exists$ and $\forall$ quantified variables.
- Generalized Geography, Generalized Hex, Generalized Reversi/Othello, Generalized Sokoban, Generalized Rush Hour.
- Constraint Logic; LTL and CTL Model Checking (satisfiability).
- Generalized Chess, Checkers, Go, Shogi ($N \times N$ boards) - PSPACE-Complete or EXPTIME-Complete depending on formulation.
- Infinite-Horizon Games, Alternating Turing Machine Acceptance, Planning with Complete Information.

**EXPTIME-Complete**

- Generalized Chess, Checkers, Go, Shogi on $N \times N$ boards under optimal play (game-theoretic winning).
- Infinite-Horizon Games; Alternating Turing Machine Acceptance with exponential time bound.
- Regular Expression Equivalence (with exponentiation and complement).
- Presburger Arithmetic (with quantifiers) - EXPTIME-Complete, actually double-exponential in some formulations.

## Undecidable Problems - No Algorithm Exists

- Not computable by any Turing Machine that always halts correctly; lie beyond the entire complexity-class hierarchy.
- **Halting Problem** - given a program and input, does it halt? The classic undecidable problem.
- **Busy Beaver** - for $n$, the maximum steps (or 1s written) an $n$-state Turing Machine can take before halting, while still eventually halting. Uncomputable.
- **Post Correspondence Problem** - can a set of dominoes (strings on top/bottom) be arranged, with repetition, so the concatenations match? Undecidable.
- **Entscheidungsproblem** - is a first-order logic formula valid? Undecidable (Church–Turing).
- **Rice's Theorem** - any nontrivial property of the language recognized by a Turing Machine is undecidable; implies program equivalence is undecidable.
- **First-Order Logic Validity** - undecidable (Church's theorem).
- **Hilbert's Tenth Problem** - does a Diophantine equation with integer coefficients have integer solutions? Undecidable (Matijasevich's theorem).
- **Word Problem for Semigroups** - are two words equivalent under a semigroup presentation? Undecidable.
- **Mortality Problem / Matrix Mortality** - does some product of a set of matrices equal the zero matrix? Undecidable for sufficiently large matrices.
- **Tiling Problem** - can the infinite plane be tiled with a given set of Wang tiles? Undecidable.

## Best Known Exact Algorithms for NP-Hard Problems

- Traveling Salesman (Held–Karp DP) - time $O(n^2 2^n)$, space $O(n \cdot 2^n)$
- Maximum Clique (improved Branch & Bound) - time $O(1.1996^n)$, space $O(n)$
- Independent Set (improved branching) - time $O(1.1996^n)$, space $O(n)$
- Vertex Cover (FPT branching) - time $O(1.2738^k)$, space $O(n)$
- Graph Coloring (exponential backtracking/DP) - time $O(2^n n)$, space $O(2^n)$
- Hamiltonian Cycle (inclusion–exclusion/DP) - time $O(2^n n^2)$, space $O(2^n)$
- Steiner Tree (Dreyfus–Wagner DP) - time $O(3^k n + 2^k n^2)$, space $O(2^k n)$
- Dominating Set (branch & reduce) - time $O(1.4969^n)$, space $O(n)$
- Set Cover (DP/Branch & Bound) - time $O(2^n)$, space $O(2^n)$
- Knapsack 0/1 (DP pseudo-poly or meet-in-the-middle) - time $O(nW)$ or $O(2^{n/2})$, space matching
- Bin Packing / Job Shop Scheduling / Quadratic Assignment (Branch-and-Bound/ILP) - exponential time and space

## The P vs NP Question (Millennium Prize Problem)

- One of the seven Millennium Prize Problems, and the most famous open problem in computer science.
- **Statement**: is $P = NP$? Can every problem whose solution is quickly verifiable also be quickly solved?
- **If $P = NP$**: every NP-Complete problem gets a polynomial-time algorithm - revolutionizing mathematics, cryptography, AI, and operations research. Factoring/discrete-log-based cryptography breaks; optimization becomes tractable; automated theorem proving becomes efficient.
- **If $P \neq NP$**: some problems are easy to verify but inherently hard to solve - the widely believed scenario. It confirms computational difficulty is real, and justifies the need for approximation algorithms and cryptographic hardness assumptions.
- **Current status**: open. Evidence for $P \neq NP$:
  - No polynomial-time algorithm found for any NP-Complete problem despite decades of effort.
  - Such an algorithm would collapse the Polynomial Hierarchy.
  - Cryptographic assumptions rely on $P \neq NP$.
  - Still, no proof exists either way.

## Polynomial Hierarchy (PH)

- Generalizes NP and co-NP via alternating quantifiers over polynomial-time predicates.
- Levels: $\Sigma_0 = \Pi_0 = P$; $\Sigma_1 = NP$; $\Pi_1 = \text{co-NP}$; $\Sigma_2 = NP^{NP}$; $\Pi_2 = \text{co-NP}^{NP}$; $\Sigma_{i+1} = NP^{\Sigma_i}$; $\Pi_{i+1} = \text{co-NP}^{\Sigma_i}$; $PH = \bigcup_i \Sigma_i$.
- **Complete problems**: QBF with bounded quantifier alternations. E.g. $\Sigma_2$-complete asks if $\exists x \, \forall y\, \varphi(x,y)$ holds; $\Pi_2$-complete asks if $\forall y \, \exists x\, \varphi(x,y)$ holds.
- If $PH$ collapses to a finite level ($\Sigma_i = \Sigma_{i+1}$), the hierarchy is finite.
- If $P = NP$, $PH$ collapses to $P$. If $NP = \text{co-NP}$, $PH$ collapses to $NP$.
- Widely believed: $PH$ is infinite (no collapse). $PH \subseteq PSPACE$.

## Randomized Complexity Classes

- **BPP** - two-sided error $\leq 1/3$ in polynomial time; correct with probability $\geq 2/3$; error amplifiable by repetition. Randomized analogue of P.
- **RP** - one-sided error: NO answers always correct; YES answers correct with probability $\geq 1/2$.
- **co-RP** - complement of RP: YES answers always correct; NO answers correct with probability $\geq 1/2$.
- **ZPP** - always correct, expected polynomial time; equals $RP \cap \text{co-RP}$; Las Vegas algorithms live here.
- **PP** - success probability $> 1/2$; very broad, contains NP; the gap from $1/2$ can be exponentially small, so amplification is nontrivial.
- **Known relationships**: $P \subseteq ZPP \subseteq RP \subseteq BPP \subseteq PP \subseteq PSPACE$. Believed $BPP = P$. $RP \subseteq NP$. $PP \supseteq NP$.
- **Key randomized algorithms**: Miller–Rabin primality test (BPP), Polynomial Identity Testing (co-RP), randomized QuickSort (ZPP expected), random sampling for approximate counting.

## Quantum Complexity Classes

- **BQP** - quantum analogue of BPP; error $\leq 1/3$ in polynomial time. Contains factoring (Shor's algorithm) and discrete logarithm. $BQP \subseteq PSPACE$. Believed $BQP \not\subseteq P$, but unproven. Not known how BQP relates to NP; believed not to contain NP-Complete problems. Contains BPP. Grover's algorithm gives a quadratic speedup for unstructured search.
- **QMA** - quantum analogue of NP: a quantum poly-time verifier plus a quantum certificate; YES instances accept with high probability on some certificate, NO instances don't on any certificate. Contains NP and BQP. Complete problem: Local Hamiltonian Problem (quantum analogue of SAT).
- **NQP** - quantum analogue of NP with unbounded error; contains NP; less studied than QMA.
- **Known relationships**: $P \subseteq BPP \subseteq BQP \subseteq PSPACE$; $BQP \subseteq QMA$. SAT solvable in $O(2^{n/2})$ via Grover's algorithm on a quantum computer - still exponential.

## Circuit Complexity

- Studies the size and depth of Boolean circuits (DAGs of AND/OR/NOT gates) needed to compute functions; a non-uniform model (a different circuit per input size).
- **Key measures**: circuit size (gate count), circuit depth (longest input-to-output path).
- **Uniformity**: a circuit family is uniform if a TM can generate $C_n$ in polynomial time (or log-space) from $n$; connects circuit complexity to standard complexity classes. Non-uniform circuits can be more powerful.

**Important circuit classes**

- **NC** - polynomial-size, polylogarithmic-depth circuits (uniform version); highly parallelizable problems.
- **AC⁰** - constant-depth, unbounded fan-in AND/OR gates plus NOT; very low complexity.
- **AC⁰[m]** - AC⁰ plus MOD $m$ gates (counting modulo $m$).
- **TC⁰** - constant-depth circuits with threshold (MAJORITY) gates.
- **P/poly** - polynomial-size non-uniform circuits; contains P and BPP.
- **NCⁱ** - depth $O(\log^i n)$, polynomial size; $NC = \bigcup_i NC^i$.
- **ACC⁰** - AC⁰ with MOD gates of arbitrary modulus.

**Known separations and importance**

- $AC^0 \subset AC^0[m]$ for some $m$ (parity is not in $AC^0$). $TC^0 \supseteq AC^0$.
- $P/poly \supseteq P$, but whether $NP \subseteq P/poly$ is unknown (would imply circuit lower bounds).
- Circuit lower bounds are notoriously hard - it was recently shown $NEXP \not\subseteq ACC^0$, but whether $PSPACE \not\subseteq P/poly$ remains open.
- Circuit complexity is central to proving $P \neq NP$: showing $NP \not\subseteq P/poly$ would imply $P \neq NP$ (since $P \subseteq P/poly$) - but general circuit lower bounds are extremely difficult.

## Communication Complexity

- Measures the bits two (or more) parties, each holding part of the input, must exchange to jointly compute a function.
- **Models**: deterministic, randomized (with error), quantum communication.
- **Lower bounds**: gives lower bounds for distributed/parallel computing time and space. Example: the disjointness function requires $\Omega(n)$ bits.
- **Key results**: the rank of the communication matrix (rows = party 1 inputs, columns = party 2 inputs, entries = output) gives lower bounds; randomized communication complexity relates to the matrix's approximate rank; quantum communication can be exponentially smaller than classical for some functions.
- **Applications**: circuit lower bounds, data streaming algorithms, distributed databases, VLSI design.

## Descriptive Complexity

- Characterizes complexity classes via logical formalisms, connecting logic, databases, and complexity theory.

**Logical characterizations**

- **P** - first-order logic with least fixed points (FO + LFP) on ordered structures.
- **NP** - existential second-order logic (ESO).
- **PSPACE** - first-order logic with partial fixed points (FO + PFP), or transitive closure.
- **PH** - second-order logic with bounded alternations.
- **NL** - transitive closure logic (TC) or deterministic transitive closure logic (DTC).

**Key theorems**

- **Fagin's Theorem (1974)** - $NP = \exists SO$; NP problems are exactly those definable by existential second-order formulas.
- **Immerman–Vardi Theorem** - $P = FO + LFP$ on ordered structures.
- **Applications**: database query languages, finite model theory, structural understanding of complexity classes.

## Approximation Algorithms and Inapproximability

- Since most NP-Hard problems can't be solved exactly in polynomial time, near-optimal polynomial-time algorithms are studied instead.
- **Approximation ratio**: for minimization, ratio $\alpha$ means solution cost $\leq \alpha \cdot OPT$; for maximization, cost $\geq \alpha \cdot OPT$.
- **Hardness of approximation**: shown via gap reductions from NP-Complete problems - many problems can't be approximated within certain ratios unless $P = NP$.

**Known results**

- TSP (metric) - best ratio $3/2$ (Christofides); inapproximable within $1+\varepsilon$ for any $\varepsilon$ unless $P=NP$.
- TSP (general) - not approximable within any constant unless $P=NP$.
- Vertex Cover - best ratio $2$; inapproximable below $1.3606$ under the Unique Games Conjecture.
- Set Cover - best ratio $O(\log n)$; inapproximable below $(1-\varepsilon)\ln n$ unless $P=NP$.
- Max Cut - best ratio $0.878$ (Goemans–Williamson); inapproximable above $16/17 \approx 0.941$ unless $P=NP$.
- Knapsack - has an FPTAS (any $\varepsilon$); no inapproximability bound.
- Bin Packing - best ratio $3/2$ (asymptotic); inapproximable below $3/2 - \varepsilon$ unless $P=NP$.
- Maximum Clique - best ratio $O(n/\log^2 n)$; inapproximable within $n^{1-\varepsilon}$ unless $P=NP$.

**PTAS / FPTAS**

- **PTAS** - gives a $(1+\varepsilon)$-approximation in time $n^{f(\varepsilon)}$.
- **FPTAS** - gives a $(1+\varepsilon)$-approximation in time $\text{poly}(n, 1/\varepsilon)$. Knapsack has an FPTAS; TSP does not unless $P=NP$.
- **Unique Games Conjecture** (Subhash Khot) - the Unique Games constraint satisfaction problem is NP-Hard. Implies tight inapproximability results for Vertex Cover, Max Cut, and others. Still open.

## Parameterized Complexity

- Studies problems with extra parameters (often solution size or a structural measure), seeking algorithms exponential only in the parameter and polynomial in input size.
- **Fixed-Parameter Tractability (FPT)** - an algorithm with running time $f(k) \cdot n^{O(1)}$, $k$ the parameter. Example: Vertex Cover is FPT with $O(1.2738^k + kn)$ time.
- **W-hierarchy** - classes $W[1], W[2], \dots, W[P]$ believed not FPT. $W[1]$ is the parameterized analogue of NP. Clique (parameterized by $k$) is $W[1]$-Complete; Dominating Set is $W[2]$-Complete.
- **Downey–Fellows theorem** - if a problem is $W[1]$-Hard, it is not FPT unless $FPT = W[1]$ (analogous to $P \neq NP$).
- **Kernelization** - polynomial-time preprocessing reducing an instance to size bounded by $f(k)$; a problem has a kernel iff it is FPT (under reasonable assumptions). Vertex Cover has an $O(k^2)$-vertex kernel (or $O(k)$ with advanced techniques).
- **Applications**: real-world instances of NP-Hard problems (SAT, Graph Coloring, TSP) often have small parameters, making FPT algorithms practical.

## Useful Resources

**Books**

- Garey, M.R. & Johnson, D.S. - _Computers and Intractability: A Guide to the Theory of NP-Completeness_
- Papadimitriou, C.H. - _Computational Complexity_
- Sipser, M. - _Introduction to the Theory of Computation_
- Arora, S. & Barak, B. - _Computational Complexity: A Modern Approach_
- Vazirani, V. - _Approximation Algorithms_
- Downey, R.G. & Fellows, M.R. - _Parameterized Complexity_

**Online resources**

- Complexity Zoo - https://complexityzoo.net/Complexity_Zoo
- Wikipedia: List of NP-complete problems, Karp's 21 problems, NP-hardness
- Compendium of NP Optimization Problems (Crescenzi & Kann)
- The Open Problem Garden

**GitHub repositories**

- [graphofnpcompletereductions/graphofnpcompletereductions.github.io](https://github.com/graphofnpcompletereductions/graphofnpcompletereductions.github.io)
- [CodingThrust/problem-reductions](https://github.com/CodingThrust/problem-reductions)
- [lawliet89/np-complete](https://github.com/lawliet89/np-complete)
- [GauravBh1010tt/Solving-NP-Hard-problems](https://github.com/GauravBh1010tt/Solving-NP-Hard-problems)
