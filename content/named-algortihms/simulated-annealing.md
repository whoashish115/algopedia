---
title: Simulated Annealing
---

**Purpose:** Find a near-optimal solution to a combinatorial or continuous optimization problem by probabilistically exploring the solution space, occasionally accepting worse moves to escape local optima, with no fixed time complexity guarantee (typically O(iterations) for a chosen iteration budget).

### Algorithm

1. Start with an initial candidate solution and a high initial "temperature" `T`.
2. At each step, generate a random neighboring solution (a small perturbation of the current one).
3. Compute how much worse or better the neighbor is: `Δ = cost(neighbor) - cost(current)`.
4. If the neighbor is at least as good (`Δ ≤ 0`), accept it unconditionally.
5. If the neighbor is worse, accept it anyway with probability `e^(-Δ / T)` — the Metropolis criterion — allowing occasional uphill moves that can escape local optima.
6. Gradually lower `T` according to a cooling schedule (e.g., multiply by a constant factor slightly less than 1 each iteration), making worse moves progressively less likely to be accepted.
7. Repeat until the temperature is effectively zero or a stopping condition is met, and return the best solution seen over the whole run.

### Code

```cpp
template <typename Solution>
Solution simulatedAnnealing(Solution initial, function<double(const Solution&)> cost,
                             function<Solution(const Solution&)> neighbor,
                             double T0, double coolingRate, int iterations) {
    Solution current = initial, best = initial;
    double T = T0;
    double currentCost = cost(current), bestCost = currentCost;

    mt19937 rng(random_device{}());
    uniform_real_distribution<double> uni(0.0, 1.0);

    for (int i = 0; i < iterations && T > 1e-8; i++) {
        Solution next = neighbor(current);
        double nextCost = cost(next);
        double delta = nextCost - currentCost;

        if (delta <= 0 || uni(rng) < exp(-delta / T)) {
            current = next;
            currentCost = nextCost;
            if (currentCost < bestCost) { best = current; bestCost = currentCost; }
        }
        T *= coolingRate;
    }
    return best;
}
```

### Paradigm

**Randomized.** Both the choice of neighbor and the acceptance of worse solutions are governed by randomness; the algorithm gives no deterministic guarantee, only a probabilistic tendency toward good solutions that strengthens as the temperature cools.

### Complexity

- Time: O(iterations), each iteration O(1) plus the cost of generating and evaluating a neighbor
- Space: O(1) beyond storing the current and best solutions

### Proof of Correctness

Simulated annealing is a heuristic, not an exact algorithm, so there's no guarantee of finding the optimum in finite time — correctness here instead means **asymptotic convergence in probability** under the right conditions.

**Sketch:** At a fixed temperature `T`, the Metropolis acceptance rule makes the chain of visited solutions a Markov chain whose stationary distribution is proportional to `e^(-cost(s)/T)` (a Boltzmann distribution) — solutions are visited with probability decreasing exponentially in their cost. As `T → 0`, this distribution concentrates entirely on the global optimum (or optima), since any suboptimal solution's relative probability mass `e^(-Δ/T)` vanishes as `T → 0` for any `Δ > 0`.

**Hajek's theorem (1988)** formalizes this: if the cooling schedule decreases slowly enough — specifically `T(k) ≥ c / log(k)` for a constant `c` depending on the depth of the deepest local-optimum "trap" the chain must escape — the chain converges to a global optimum in probability as the number of iterations `k → ∞`. Cooling any faster than this can leave the chain permanently trapped in a local optimum with non-vanishing probability.

In practice, finite runs use much faster (e.g., geometric) cooling, trading the asymptotic guarantee for a good — though not certified optimal — solution within a practical time budget. ∎

### Variants / Use Cases

- **Adaptive cooling schedules** → adjust the cooling rate based on observed acceptance rate rather than a fixed schedule
- **Parallel tempering (replica exchange)** → runs several chains at different temperatures in parallel, periodically swapping states
- **Quantum annealing** → physical analogue using quantum tunneling instead of thermal fluctuation to escape local optima
- **Threshold accepting / Great Deluge algorithm** → deterministic relatives that accept moves based on a fixed threshold rather than a probabilistic criterion
- **Traveling Salesman Problem, VLSI placement, scheduling, hyperparameter tuning** → classic optimization domains where simulated annealing is commonly applied