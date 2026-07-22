---
title: Sprague-Grundy Theorem
---

**Purpose:** Determine the winner of any impartial combinatorial game (and sums of such games) under normal play convention, by reducing every position to an equivalent single Nim heap — turning arbitrarily complex games into an O(1) XOR check once each component's value is known.

### Algorithm

1. For any game position, define its **Grundy number** `g(position)` as the **mex** (minimum excludant) of the Grundy numbers of all positions reachable in one move: the smallest non-negative integer not among them.
2. A terminal position (no legal moves) has Grundy number 0 by definition (the mex of an empty set).
3. Compute Grundy numbers bottom-up (or via memoized recursion) starting from terminal positions.
4. For a **sum** of several independent games played together (a move affects exactly one component per turn), the Grundy number of the whole is the **XOR** of the Grundy numbers of each component.
5. The player about to move loses under optimal play if and only if the combined Grundy number is 0 (a "P-position"); otherwise they can force a win (an "N-position").
6. To find a winning move from an N-position, find a component and a move within it that changes that component's Grundy number so the overall XOR becomes 0.

### Code

```cpp
int grundy(int state, vector<vector<int>>& moves, vector<int>& memo) {
    if (memo[state] != -1) return memo[state];
    set<int> reachable;
    for (int next : moves[state])
        reachable.insert(grundy(next, moves, memo));

    int mex = 0;
    while (reachable.count(mex)) mex++;
    return memo[state] = mex;
}

bool isWinningCombinedGame(vector<int>& gameGrundyNumbers) {
    int xorSum = 0;
    for (int g : gameGrundyNumbers) xorSum ^= g;
    return xorSum != 0;
}
```

### Paradigm

**Transform and Conquer.** The theorem's entire power is in reducing any impartial game to a canonical equivalent form — a single Nim heap of size equal to its Grundy number — after which the already-solved theory of Nim (XOR of heap sizes) directly gives the answer. Computing individual Grundy numbers via memoized mex is itself an instance of Dynamic Programming, but the algorithmic idea underlying the theorem is the transformation to Nim.

### Complexity

- Time: O(states × average out-degree) to compute all Grundy numbers via memoization; O(k) to combine `k` components via XOR
- Space: O(states) for memoization

### Proof of Correctness

**Claim:** Every impartial game position under normal play is equivalent, in every disjunctive sum, to a Nim heap of size `g(position)`.

**Proof by strong induction on game states (well-founded, since impartial games terminate):**

- **Base case:** A terminal position has no moves, is a loss for the player to move, and has `g = 0` — exactly matching a Nim heap of size 0, which is likewise an immediate loss for the mover.
- **Inductive step:** Assume the claim holds for every position reachable from `position` (each strictly "smaller" in the game's termination order). By the mex definition, the set of reachable Grundy values must contain every integer from `0` to `g(position) - 1` (otherwise the mex would be smaller), so from `position` one can move to a state of Grundy number `k` for any `k < g(position)` — exactly matching the moves available from a Nim heap of size `g(position)` (which can shrink to any smaller size). No move from `position` produces Grundy number `g(position)` itself, by the mex definition, matching Nim's rule that a heap cannot "move to itself." Hence `position` and a Nim heap of size `g(position)` offer identical move options in terms of resulting Grundy values, so they behave identically inside any disjunctive sum.

**Applying to sums:** Since each component is equivalent to a Nim heap of its own Grundy number, a sum of components is equivalent to a sum of Nim heaps — and by Nim's own theory, a Nim-heap sum is a loss for the player to move exactly when the XOR of heap sizes is 0. Substituting Grundy numbers for heap sizes gives the stated winning condition. ∎

### Variants / Use Cases

- **Nim** → the base case, where a position's Grundy number is simply its heap size
- **Wythoff's Game** → a Nim-like game with an extra move type, solved via a modified Grundy analysis
- **Turning/coin games** → games on rows of coins solved by decomposing into independent Grundy-number subgames
- **Green Hackenbush** → a tree-cutting game solved via the Colon Principle, an extension of Grundy theory
- **Misère play variants** → the same normal-play theorem fails under misère convention (last player to move loses instead), requiring separate, more delicate theory
- **Competitive programming "Game Theory" problems** → computing Grundy numbers over small state spaces is a standard technique for multi-pile / multi-component game problems