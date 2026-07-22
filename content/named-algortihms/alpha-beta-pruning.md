---
title: Alpha-Beta Pruning
---

**Purpose:** Compute the exact same result as minimax — the optimal move in a two-player zero-sum game — while skipping branches that cannot possibly affect that result, reducing time from O(b^d) to as low as O(b^(d/2)) with good move ordering.

### Algorithm

1. Run the same recursive tree traversal as minimax, but additionally track two bounds while descending: `alpha` (the best value the maximizer can already guarantee somewhere in the tree) and `beta` (the best value the minimizer can already guarantee).
2. At a maximizing node, after evaluating each child, update `alpha = max(alpha, childValue)`.
3. At a minimizing node, after evaluating each child, update `beta = min(beta, childValue)`.
4. Before evaluating the next child, check if `alpha >= beta`. If so, stop exploring the remaining children of this node — whatever their true value, the opponent already has a better alternative elsewhere and would never let play reach this branch, so it can't change the final decision.
5. Pass the current `alpha` and `beta` down to child calls so the pruning condition tightens as the search goes deeper.
6. Return the same value minimax would have returned, just computed with less work.

### Code

```cpp
int alphaBeta(GameState state, int depth, int alpha, int beta, bool maximizing) {
    if (depth == 0 || state.isTerminal())
        return state.evaluate();

    if (maximizing) {
        int best = INT_MIN;
        for (auto& move : state.getMoves()) {
            best = max(best, alphaBeta(state.applyMove(move), depth - 1, alpha, beta, false));
            alpha = max(alpha, best);
            if (alpha >= beta) break;
        }
        return best;
    } else {
        int best = INT_MAX;
        for (auto& move : state.getMoves()) {
            best = min(best, alphaBeta(state.applyMove(move), depth - 1, alpha, beta, true));
            beta = min(beta, best);
            if (alpha >= beta) break;
        }
        return best;
    }
}
```

### Paradigm

**Branch and Bound.** Alpha and beta act as running bounds on what the final answer could still turn out to be; any branch whose best possible outcome can't beat a bound already secured elsewhere is cut off without evaluation — the defining move of branch and bound, applied here to a minimax game tree.

### Complexity

- Time: O(b^d) worst case (no effective pruning, e.g. worst move ordering); O(b^(d/2)) best case with optimal move ordering, which doubles the effective search depth for the same cost
- Space: O(d)

### Proof of Correctness

**Claim:** `alphaBeta` called with `alpha = -∞, beta = +∞` returns exactly the same value as `minimax` on the same tree.

**Proof by induction on subtree height:** Base case (terminal/depth-0 nodes) is identical to minimax — trivially equal. For the inductive step, the only difference from minimax is the early `break` when `alpha >= beta`. It suffices to show a pruned child could never have been the one that determines the node's value: at a maximizing node, once `alpha >= beta`, the maximizer already has a move worth at least `alpha ≥ beta` — but the minimizing ancestor that passed down `beta` already has an alternative worth `beta` or better for itself, so it would never choose to enter this branch in the first place. Its true value is therefore irrelevant to the final root decision, regardless of what it turns out to be. The symmetric argument holds at minimizing nodes. Since every pruned subtree is provably irrelevant to the outcome, and all unpruned subtrees are evaluated exactly as in minimax (by the inductive hypothesis), the returned value matches minimax's at every node, including the root. ∎

### Variants / Use Cases

- **Negamax with alpha-beta** → unifies max/min cases via sign-flipping, standard in engine implementations
- **Principal Variation Search (PVS) / NegaScout** → assumes the first move is best and uses cheap "null-window" searches to verify, tightening pruning further
- **Move ordering heuristics (killer moves, history heuristic, transposition tables)** → reordering children to try the best moves first, which is what makes O(b^(d/2)) achievable in practice
- **Iterative deepening + alpha-beta** → the standard combination used in chess and other engines under a time budget
- **Quiescence search** → extends the search past the depth limit at "noisy" positions (e.g. mid-capture) to avoid misjudging unstable evaluations

Built on: Minimax Algorithm.