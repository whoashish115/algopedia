---
title: General
---

Constructive algorithms are a fundamental part of competitive programming and problem solving. Unlike optimization or decision problems, they require you to **explicitly construct** a valid solution that satisfies all given constraints. The challenge is rarely implementation - it is discovering the hidden structure, invariant, pattern, or transformation that makes the construction possible.

Mastering constructive algorithms also improves general problem-solving ability. Many graph, DP, greedy, number theory, and geometry problems ultimately reduce to identifying the right observation or reformulating the problem into a form that is easier to construct.

> **Terminology**
>
> * **Constructive Algorithm** - Explicitly builds a valid object satisfying all constraints.
> * **Problem Solving** - The complete process of understanding, analyzing, proving, and implementing a solution.
> * **Algorithm** - A finite sequence of well-defined steps that solves a problem.
> * **Technique** - A reusable strategy (e.g. Greedy, DP, Binary Search, Two Pointers).
> * **Paradigm** - A broad design philosophy containing multiple techniques (e.g. Divide & Conquer, Dynamic Programming).
> * **Data Structure** - A way to organize and maintain data efficiently.
> * **Observation** - A mathematical or logical insight that simplifies the solution.
> * **Transformation (Reduction)** - Converting a problem into an equivalent but easier one.
> * **Invariant** - A property that remains unchanged after every operation.
> * **State** - Information describing a partial solution or current configuration.
> * **Decision Problem** - Determines whether a solution exists.
> * **Search Problem** - Finds one valid solution.
> * **Optimization Problem** - Finds the best valid solution according to an objective.

---

## Workflow

1. **Understand the Problem** - identify the input, output, constraints, and hidden requirements.
2. **Constraint Analysis** - infer the expected complexity and possible approaches from the limits.
3. **Start Small** - solve for `n = 1`, `2`, `3`, or other minimal cases before generalizing.
4. **Pattern Observation** - compute small examples and look for recurring behavior or formulas.
5. **Mathematical Observation** - derive useful properties, identities, monotonicity, parity, or relationships.
6. **Invariant** - identify a property that never changes after operations.
7. **Extreme Cases** - test smallest/largest inputs, all equal values, sorted/reversed order, parity, duplicates, `0`, `1`, and boundary constraints.
8. **Visualization** - draw arrays, trees, graphs, intervals, or manually simulate operations.
9. **Graph Modeling** - check whether the problem can be represented as a graph.
10. **Problem Remodeling / Transformation** - rewrite the problem into an equivalent but simpler form.
11. **Change the Point of View** - think in reverse, complement, duality, symmetry, or another representation.
12. **Reverse Engineering** - derive previous states or infer the required construction from the final state.
13. **Brute Force** - solve the simplest version first to understand the structure.
14. **Greedy** - determine whether a locally optimal choice leads to a globally optimal solution.
15. **Divide & Conquer** - split the problem into smaller independent subproblems.
16. **Guess & Prove** - formulate a hypothesis from observations, then prove or disprove it rigorously.


## Tips

* Solve the same problem using multiple approaches.
* Compare **Time Complexity**, **Space Complexity**, implementation difficulty, and scalability.
* If an approach fails, identify **exactly why** (counterexample, violated invariant, incorrect assumption, excessive complexity, or missed edge case).
* Prove correctness before optimizing.
* Search for monotonicity, parity, symmetry, periodicity, ordering, or hidden invariants.
* Determine whether preprocessing simplifies repeated queries.
* Consider reversing operations or solving the complementary problem.
* Reduce the problem to a known algorithm, data structure, theorem, or mathematical property.
* Validate the solution on carefully chosen edge cases before implementation.
