---
title: 4. Game Theory 
--- 

**Combinatorial game theory** studies two‑player games with:
- **Perfect information** (no hidden cards, no dice, no chance),
- **No randomness**,
- **Alternating moves**,
- **Finite termination** (no infinite play),
- **Impartial** (both players have the same legal moves from any position).

The standard convention is **normal play**: the player who cannot move **loses**.  
The alternative is **misère play**: the player who cannot move **wins** (i.e., the last move loses).

## Terminology

- **Terminal state**: no legal moves. In normal play, it is a **losing** state for the player to move.
- **Winning state**: there exists at least one move to a losing state.
- **Losing state**: every legal move leads to a winning state.
- **Nim‑sum**: bitwise XOR of heap sizes.
- **Grundy number** (nim‑value): a non‑negative integer assigned to each position, defined via **mex** (minimum excludant).


## Game Theory Principles

| Principle | Explanation |
|-----------|-------------|
| **Normal Play Convention** | The standard rule in combinatorial game theory. The player who has **no legal move** on their turn **loses**. Most impartial games use this convention. |
| **Misère Play Convention** | Opposite of normal play. The player who has **no legal move wins**, meaning the player making the **last move loses**. Many normal-play strategies no longer work directly. |
| **Perfect Information** | Every player knows the complete state of the game at every moment. There are no hidden cards, hidden information, or secret moves. Chess and Nim satisfy this property. |
| **Deterministic Games** | No randomness exists. There are no dice, shuffled cards, or probability. Every move completely determines the next state. |
| **Alternating Turns** | Players take turns making exactly one move until the game ends. |
| **Finite Game** | Every sequence of legal moves eventually reaches a terminal state. Infinite loops are not allowed. |
| **Impartial Game** | Both players always have exactly the same legal moves from every position. Nim is the classic example. |
| **Partisan Game** | The legal moves depend on whose turn it is. Chess and Checkers are partisan games. Sprague–Grundy does **not** apply directly. |
| **Terminal Position** | A state with no legal moves remaining. Under normal play it is a losing position; under misère play it is winning. |
| **Winning Position** | A position from which there exists **at least one move** leading to a losing position for the opponent. |
| **Losing Position** | A position where **every possible move** gives the opponent a winning position. |
| **Recursive Winning Rule** | A state is winning **iff** at least one of its children is losing. This is the recursive definition used in DP. |
| **Recursive Losing Rule** | A state is losing **iff** every legal move leads to a winning state. |
| **Backward Induction** | Start by marking terminal positions, then work backwards through the game graph to classify every position as winning or losing. |
| **Game Tree Principle** | Every position is a node, and every legal move is an edge. Solving the game means analyzing this tree (or DAG). |
| **State Graph Principle** | Instead of a tree, identical states can be merged into one node, producing a graph that is easier to solve with DP. |
| **Sprague–Grundy Theorem** | Every finite impartial game under normal play is equivalent to a single Nim heap whose size equals its Grundy number. Therefore every impartial game can be solved using XOR. |
| **Grundy Number (Nim-value)** | Every position is assigned a non-negative integer representing an equivalent Nim heap. Grundy numbers completely characterize impartial games. |
| **mex Principle** | The Grundy number of a position is the **minimum excluded non-negative integer** among the Grundy numbers of all reachable positions. $$g(P)=mex(\{g(Q)\})$$ |
| **mex Empty Rule** | If a position has no legal moves, the reachable set is empty, so **mex(∅)=0**. Thus every terminal position has Grundy number 0. |
| **mex Consecutive Rule** | If the reachable Grundy numbers are `{0,1,...,k−1}`, then the mex is exactly `k`. |
| **Grundy Zero Rule** | A position is losing **iff** its Grundy number equals 0. |
| **Grundy Nonzero Rule** | A position is winning **iff** its Grundy number is greater than 0. |
| **Disjoint Sum Principle** | If a game consists of independent components where one move affects only one component, the total Grundy number equals the XOR of the components. |
| **Game Equivalence** | Two impartial games are equivalent if they have the same Grundy number. Replacing one with the other never changes the winner. |
| **Nim Heap Equivalence** | Every impartial game can be replaced by one Nim heap whose size equals its Grundy number. |
| **Component Reduction** | Instead of analyzing the original game, compute the Grundy number of each independent component and replace each by a Nim heap. |
| **Composite Game Principle** | Solve each independent game separately, XOR their Grundy numbers, then determine the winner from the final XOR. |
| **Nim-Sum Principle** | The XOR of all heap sizes determines the winner in ordinary Nim. |
| **Zero XOR Rule** | If the XOR of all heaps is zero, the current player loses assuming perfect play. |
| **Winning Move in Nim** | Compute `S = XOR of all heaps`. Choose a heap `h` where `(h ^ S) < h`, then reduce it to `h ^ S`, making the new XOR equal to zero. |
| **Bouton's Theorem** | Gives the complete solution to ordinary Nim: XOR = 0 is losing; otherwise winning. |
| **Misère Nim Principle** | Play exactly like ordinary Nim unless **every heap has size 1**. In that special case the parity of the number of heaps determines the winner. |
| **Zeckendorf Theorem** | Every positive integer can be written uniquely as the sum of non-consecutive Fibonacci numbers. This theorem solves Fibonacci Nim. |
| **Golden Ratio Principle** | Wythoff's losing positions are generated using the golden ratio $$\phi=\frac{1+\sqrt5}{2}$$. |
| **Beatty Sequence Principle** | Wythoff's losing positions form two complementary Beatty sequences: $$\lfloor k\phi\rfloor,\lfloor k\phi^2\rfloor$$. |
| **Moore's Nim Principle** | For Nimₖ, examine each binary bit independently. A position is losing iff every bit count is divisible by `k+1`. |
| **Staircase Nim Principle** | Only piles on **odd-numbered stairs** contribute to the XOR. Even-indexed stairs never affect the winner. |
| **Turning Turtles Principle** | Treat every head at position `i` as a Nim heap of size `i`. XOR all head positions to determine the winner. |
| **Green Hackenbush Stalk Principle** | A simple vertical stalk containing `n` edges has Grundy number `n`; it is equivalent to a Nim heap of size `n`. |
| **Colon Principle** | In Green Hackenbush, replace each branch attached to a stalk by its Grundy number, greatly simplifying the game. |
| **Fusion Principle** | Certain cycles may be contracted into simpler equivalent structures without changing the game's value. |
| **Independence Principle** | If one move cannot affect another component, each component can be analyzed independently. |
| **XOR Identity** | `x ⊕ 0 = x`. A component with Grundy number 0 contributes nothing to the total XOR. |
| **XOR Cancellation** | `x ⊕ x = 0`. Equal Grundy numbers cancel each other. |
| **Associativity of XOR** | `(a⊕b)⊕c = a⊕(b⊕c)`. Components may be XORed in any grouping. |
| **Commutativity of XOR** | `a⊕b = b⊕a`. The order of components does not matter. |
| **Optimal Play Principle** | Both players always choose moves that maximize their own chance of winning. |
| **Determinacy Principle** | Every finite impartial game has exactly one correct outcome under perfect play: winning or losing. Draws do not occur. |
| **Winning Move Principle** | Whenever possible, move to a state whose Grundy number makes the overall XOR equal to zero. |
| **Losing Move Principle** | From a losing position, every legal move necessarily gives the opponent a winning position. |
| **Mirror Strategy** | In symmetric games, copy the opponent's move to restore symmetry. This often proves the second player wins. |
| **Pairing Strategy** | Pair objects before the game starts. Whenever the opponent chooses one object, choose its partner, preserving an invariant. |
| **Copycat Strategy** | Maintain a winning invariant by imitating or mirroring the opponent's move in another component. |
| **Strategy-Stealing Principle** | Assume the second player has a winning strategy. The first player makes an arbitrary move, then "steals" that strategy. If this is always possible, the second player's strategy cannot exist. Often proves only that the first player wins, not how. |
| **Invariant Principle** | Find a quantity that never changes regardless of the moves played. If the desired end state violates the invariant, it is impossible to reach. |
| **Monovariant Principle** | Find a quantity that always increases or decreases. Since it cannot change forever, the game must terminate or satisfy a bound. |
| **Parity Principle** | Many games depend only on whether some quantity is even or odd. Tracking parity often completely determines the winner. |
| **Coloring Principle** | Color a board or graph to reveal invariants or parity properties that are otherwise difficult to see. |
| **Potential Function Principle** | Define a numerical measure that strictly decreases after every move. This proves termination and often bounds the number of moves. |
| **Dynamic Programming Principle** | Compute winning states or Grundy numbers recursively while storing previously computed answers. |
| **Memoization Principle** | Cache solved states so each game position is evaluated only once. |
| **DAG Principle** | If the game graph has no cycles, Grundy numbers can be computed efficiently using DFS or topological order. |
| **Topological Order Principle** | For DAGs, process states from leaves toward the root so every child is already solved before its parent. |
| **Search Principle** | DFS is the most common way to recursively compute Grundy numbers on finite state graphs. |
| **Reachability Principle** | Only positions reachable from the initial state need to be analyzed. |
| **Reduction Principle** | Replace complicated structures with simpler equivalent ones while preserving Grundy values. |
| **Dominated Move Principle** | If one move is never better than another, it can often be ignored during analysis. |
| **Reversible Move Principle** | Some moves that can immediately be undone contribute nothing strategically and may be simplified away. |
| **Cycle Handling Principle** | Standard Sprague–Grundy theory assumes games always terminate. Games containing cycles require additional methods beyond ordinary Grundy numbers. |
| **State Compression Principle** | Encode positions compactly (often using bitmasks) so DP over game states becomes feasible. |
| **Bitmask DP Principle** | Small combinatorial games can often be solved by representing every state as a bitmask and computing Grundy numbers over subsets. |
| **Complexity Principle** | For an acyclic game graph with `V` states and `E` transitions, Grundy values can typically be computed in `O(V+E)` plus mex computation. |
| **Binary Representation Principle** | Several Nim variants become simple when each binary bit is analyzed independently. |
| **Binary Column Principle** | In Moore's Nim, each bit position behaves independently, so binary column sums determine the answer. |
| **Heap Independence Principle** | In ordinary Nim, every move changes exactly one heap, allowing XOR to completely characterize the game. |
| **Conservation of XOR** | After every opponent move in Nim, the winning player restores the total XOR to zero, maintaining the winning invariant until victory. |


## Sprague–Grundy Theorem

The fundamental theorem of impartial games:

- Every position has a Grundy number:
  
$$
g(P) = \operatorname{mex}\bigl(\{ g(Q) \mid P \to Q \text{ is a legal move} \}\bigr)
$$

- `mex(S)` = the smallest non‑negative integer not in `S`.  
  Example: `mex({0,1,3}) = 2`, `mex(∅) = 0`.

- A position is **losing** iff `g(P) == 0`.

- **Sum of independent subgames**: If a game consists of several components, and a move is made in exactly one component, the Grundy number of the combined game is the **XOR** of the Grundy numbers of all components.

## Nim

**Game**: `k` heaps; a move removes any positive number of stones from exactly one heap. Last move wins.

**Solution**:

- Compute `S = XOR of all heap sizes`.
- If `S == 0` → losing position.
- If `S != 0` → winning. A winning move exists: find a heap `h` such that `(h ^ S) < h`; reduce it to `h ^ S`. The new XOR becomes zero.

```cpp
int xorSum = 0;
for (int x : heaps) xorSum ^= x;

if (xorSum == 0) {
    // losing
} else {
    for (int &x : heaps) {
        if ((x ^ xorSum) < x) {
            x = x ^ xorSum;
            break;
        }
    }
}
```

## Misère Nim

Same as Nim, but last move **loses**.

**Solution**:

- If **all heaps have size 1**:
  - Winning iff the number of heaps of size 1 is **odd**.
- Otherwise, use standard Nim strategy (non‑zero XOR → winning).

```cpp
bool misereNimWin(vector<int>& heaps) {
    int xorSum = 0;
    bool allOnes = true;
    for (int x : heaps) {
        xorSum ^= x;
        if (x > 1) allOnes = false;
    }
    if (allOnes) {
        int ones = count(heaps.begin(), heaps.end(), 1);
        return (ones % 2 == 1);
    }
    return xorSum != 0;
}
```

## Wythoff's Game

**Game**: two heaps. A move removes any positive number from one heap, **or** the same positive number from both. Last move wins.

**Solution**: Losing positions are `(⌊kφ⌋, ⌊kφ²⌋)` for `k ≥ 0`, where `φ = (1+√5)/2`.  
To test if `(a,b)` is losing (assume `a ≤ b`):

```cpp
bool isWythoffLosing(int a, int b) {
    if (a > b) swap(a, b);
    int d = b - a;
    int k = (int)(d * (sqrt(5.0) + 1.0) / 2.0);
    return (a == k && b == k + d);
}
```

## Staircase Nim

**Game**: `n` stairs, each with `a_i` coins. A move moves any positive number of coins from stair `i` to stair `i-1` (stair 0 = ground, coins removed). Last move wins.

**Solution**: Equivalent to Nim on **odd‑numbered stairs** only. Compute XOR of `a_i` for odd `i`. If zero → losing.

```cpp
int xorSum = 0;
for (int i = 1; i <= n; i += 2) xorSum ^= a[i];
// losing if xorSum == 0
```

## Fibonacci Nim

**Game**: one heap. First move cannot take all stones. Each subsequent move may take at most twice the previous move. Last move wins.

**Solution**: Use the **Zeckendorf representation** – every positive integer is a unique sum of non‑consecutive Fibonacci numbers.  
- If the heap size is a Fibonacci number, the position is losing.
- Otherwise, the winning move is to take the smallest Fibonacci number in the Zeckendorf representation.

```cpp
vector<int> fib = {1,2,3,5,8,13,21,...}; // up to n
int n = heapSize;
int take = 0;
for (int i = fib.size()-1; i >= 0; --i) {
    if (fib[i] <= n) {
        if (take == 0) take = fib[i];
        n -= fib[i];
    }
}
// take is the amount to remove on the first move
```

## Moore's Nim (Nimₖ)

**Game**: `k` heaps. Each move removes any positive number from **at most `k` heaps** (at least one). Last move wins.

**Solution**: Write each heap size in binary. For each bit position, compute the sum of that bit across all heaps. The position is losing **iff** every bit‑sum is divisible by `k+1`.

```cpp
bool mooreNimLosing(vector<int>& heaps, int k) {
    int maxBit = 0;
    for (int x : heaps) maxBit = max(maxBit, 32 - __builtin_clz(x));
    for (int b = 0; b < maxBit; ++b) {
        int sum = 0;
        for (int x : heaps) sum += (x >> b) & 1;
        if (sum % (k+1) != 0) return false;
    }
    return true;
}
```

## Turning Turtles

**Game**: a row of coins (heads/tails). A move selects a coin showing heads, turns it to tails, and optionally flips one coin to its left. Last move wins.

**Solution**: Treat each head at position `i` (0‑based from left) as an independent Nim heap of size `i`. The Grundy number of the position is the XOR of all `i` for heads.

```cpp
int turningTurtlesGrundy(vector<int>& heads) {
    int xorSum = 0;
    for (int pos : heads) xorSum ^= pos;
    return xorSum;
}
```

## Green Hackenbush

**Game**: a graph with green edges (both players can cut any green edge). When an edge is cut, all edges no longer connected to the ground are removed. Last move wins.

**Solution**:  
- A single stalk (path) has Grundy equal to its length.
- For a tree, apply the **colon principle**: replace each branch with its Grundy; the Grundy of the stalk is `mex` of the set of Grundy values reachable by cutting one edge (which is typically the nim‑sum of remaining branches).
- For general graphs, use the **fusion principle** to reduce cycles (not detailed here; rarely needed).

## Strategy for Any Impartial Game

1. **Decompose** the game into independent components if possible.
2. Compute Grundy numbers for each component (often via DP/memoization for small states).
3. XOR all Grundy numbers – zero means losing, non‑zero means winning.
4. To find a winning move, locate a component whose Grundy `g` can be changed to `g' = xorSum ^ g` (with `g' < g` and a move exists to that Grundy).

**MEX computation** can be done with a boolean array of size up to the maximum Grundy value seen.
