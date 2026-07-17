---
title: Probability
---

## Basics

$$
P(\text{event}) = \frac{\text{favorable outcomes}}{\text{total outcomes}}, \qquad 0 \le P \le 1.
$$

| Rule | Formula | Condition |
|---|---|---|
| Complement | $P(\bar A) = 1 - P(A)$ | always |
| Independent events | $P(A \cap B) = P(A)\cdot P(B)$ | $A, B$ independent |
| Mutually exclusive | $P(A \cup B) = P(A) + P(B)$ | $A, B$ disjoint |
| General addition | $P(A \cup B) = P(A) + P(B) - P(A \cap B)$ | always |
| Conditional | $P(A \mid B) = \dfrac{P(A \cap B)}{P(B)}$ | $P(B) > 0$ |

## Bayes' Theorem

$$
P(A \mid B) = \frac{P(B \mid A)\cdot P(A)}{P(B)}.
$$

> [!abstract]- Mathematical Proof
> By the definition of conditional probability, $P(A \cap B) = P(A \mid B)\cdot P(B)$ and also $P(A \cap B) = P(B \mid A)\cdot P(A)$, since intersection is symmetric. Setting these two expressions equal and dividing through by $P(B)$ isolates $P(A \mid B)$, giving Bayes' Theorem.

Used to "flip" a conditional probability when the reverse direction is easier to measure or reason about — e.g. given the probability of a symptom given a disease, find the probability of a disease given a symptom.

### Law of Total Probability

If $B_1, B_2, \dots, B_k$ partition the sample space (mutually exclusive, collectively exhaustive),

$$
P(A) = \sum_{i=1}^{k} P(A \mid B_i)\cdot P(B_i).
$$

This is typically paired with Bayes' Theorem: expand the denominator $P(B)$ in Bayes' formula by conditioning on every way $B$ could have occurred.

## Expected Value

For a random variable $X$ taking value $x_i$ with probability $p_i$:

$$
E[X] = \sum_i x_i \cdot p_i.
$$

### Linearity of Expectation

$$
E[X + Y] = E[X] + E[Y].
$$

Holds for **any** $X, Y$ — independent or not. This is the single most useful tool in probabilistic counting: split a complicated sum into simple, independently-computable pieces.

### Indicator-Variable Trick

To compute the expectation of a complicated count, write it as a sum of $0/1$ indicator variables, one per event being counted:

$$
E[\text{count}] = \sum_i P(\text{event}_i \text{ happens}).
$$

This avoids reasoning about dependence between events.

## Variance

$$
\text{Var}(X) = E[X^2] - \big(E[X]\big)^2.
$$

For **independent** $X, Y$:

$$
\text{Var}(X + Y) = \text{Var}(X) + \text{Var}(Y).
$$

Unlike expectation, variance is **not** linear in general — the independence condition is required, since in the general case $\text{Var}(X+Y) = \text{Var}(X) + \text{Var}(Y) + 2\,\text{Cov}(X, Y)$.

## Common Distributions

| Distribution | $E[X]$ | $\text{Var}(X)$ | Notes |
|---|---|---|---|
| Bernoulli($p$) | $p$ | $p(1-p)$ | single trial, success probability $p$ |
| Binomial($n, p$) | $np$ | $np(1-p)$ | sum of $n$ independent Bernoulli($p$) trials |
| Geometric($p$) | $1/p$ | $(1-p)/p^2$ | number of trials until the first success |

## Probability / Expectation DP

State captures "where we are" in a process; the DP value is either a **probability** of reaching that state, or an **expected value** computed backward from terminal states.

**Probability form (forward):**

$$
dp[\text{state}] = P(\text{reaching state}), \qquad dp[\text{next}] \mathrel{+}= dp[\text{state}]\cdot P(\text{state}\to\text{next}).
$$

Process states in an order that finalizes $dp[\text{state}]$ before it updates any $\text{next}$ (e.g. decreasing problem size).

**Expectation form (backward from terminal states):**

$$
E[\text{state}] =
\begin{cases}
\text{base case} & \text{terminal} \\[4pt]
1 + \displaystyle\sum_{\text{next}} P(\text{state}\to\text{next})\cdot E[\text{next}] & \text{otherwise}
\end{cases}
$$

Solved by processing states in dependency order, or — when transitions form cycles — by setting up and solving a linear system (e.g. via Gaussian elimination).

## Random Walks & Markov Chains

A Markov chain models a sequence of states where the probability of moving to the next state depends **only on the current state**, not the history:

$$
P(X_{n+1}=j \mid X_n=i, X_{n-1}, \dots, X_0) = P(X_{n+1}=j \mid X_n=i) = P_{ij}.
$$

Collecting the $P_{ij}$ into a transition matrix $P$, the distribution after $n$ steps from an initial distribution $\pi_0$ is

$$
\pi_n = \pi_0 \cdot P^n,
$$

which can be computed in $O(k^3 \log n)$ with **Matrix Exponentiation** when $n$ is large ($k$ = number of states). A classic special case is the **1D random walk**: from position $i$, move to $i+1$ with probability $p$ and to $i-1$ with probability $1-p$; expected hitting times and absorption probabilities are typically found by setting up the expectation-DP recurrence above and solving the resulting linear system.

## Worked Example: Coupon Collector

Expected number of dice rolls needed to see all $n$ distinct faces at least once:

$$
E[\text{rolls}] = n \cdot H_n = n\left(1 + \frac12 + \frac13 + \dots + \frac1n\right).
$$

```cpp
double couponCollectorExpectation(int n) {
    double harmonic = 0;
    for (int i = 1; i <= n; i++)
        harmonic += 1.0 / i;
    return n * harmonic;
}
```

**Algorithm:**
* Split the process into $n$ phases, where phase $i$ begins right after collecting the $(i-1)$-th distinct face.
* In phase $i$, each roll succeeds (reveals a new face) with probability $\frac{n-i+1}{n}$, so by the Geometric distribution above the expected length of phase $i$ is $\frac{n}{n-i+1}$.
* By linearity of expectation, sum the $n$ phase lengths to get $n \cdot H_n$.

**Time Complexity:** $\mathcal{O}(n)$
**Space Complexity:** $\mathcal{O}(1)$
