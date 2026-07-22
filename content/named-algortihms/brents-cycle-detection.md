---
title: Brent's Cycle Detection
---

**Purpose:** Detect a cycle in a linked list (or sequence) and find its length and starting point, in O(n) time and O(1) space — with fewer pointer evaluations in practice than Floyd's algorithm.
### Algorithm
1. Initialize `power = 1` and `lam = 1` (this will become the cycle length).
2. Set `tortoise = head` and `hare = head->next`.
3. While `tortoise != hare`:
   - If `power == lam`, teleport the tortoise to the hare's position and double `power`, then reset `lam = 0`.
   - Move `hare` forward by one step and increment `lam`.
4. Once they meet, `lam` holds the exact cycle length.
5. To find the cycle start: reset both pointers to the head. Advance one pointer `lam` steps ahead first.
6. Then move both pointers one step at a time together — the point where they meet is the start of the cycle.

### Code
```cpp
pair<int, ListNode*> brentCycle(ListNode* head) {
    if (!head || !head->next) return {0, nullptr};

    int power = 1, lam = 1;
    ListNode *tortoise = head, *hare = head->next;

    while (tortoise != hare) {
        if (power == lam) {
            tortoise = hare;
            power *= 2;
            lam = 0;
        }
        hare = hare->next;
        lam++;
        if (!hare) return {0, nullptr}; // no cycle
    }

    // find cycle start
    tortoise = hare = head;
    for (int i = 0; i < lam; i++) hare = hare->next;
    while (tortoise != hare) {
        tortoise = tortoise->next;
        hare = hare->next;
    }
    return {lam, tortoise};
}
```

### Paradigm
**Decrease and Conquer (exponential search / doubling).** It uses power-of-two checkpoints to bound the search for the cycle length, exponentially expanding the search window until the hare laps the fixed tortoise — a doubling strategy distinct from Floyd's pure two-pointer convergence.

### Complexity
- Time: O(n)
- Space: O(1)

### Proof of Correctness
**Part 1 — Why the main loop finds the cycle length:** The tortoise "teleports" to the hare's position at power-of-two checkpoints (`1, 2, 4, 8, ...`), and between teleports, the hare walks ahead while `lam` counts the steps taken since the last teleport. If a cycle of length `C` exists, once the hare enters the cycle, it will eventually complete a full loop and land back on some earlier position. Because the teleport checkpoints double every round, at some checkpoint the distance the hare walks (`lam`) will span at least one full cycle length `C` while the tortoise sits fixed at a point inside or before the cycle. At that point, the hare — moving one step at a time — must land exactly on the tortoise, since it traverses every position in the cycle exactly once per lap. When this happens, `lam` equals exactly `C`, the cycle length (since the count started fresh from the last teleport, which was aligned to the point the hare eventually laps back to).

**Part 2 — Why the second phase finds the cycle start:** Let `L` = distance from head to cycle start, and `C = lam` = cycle length. Advancing one pointer `C` steps ahead first means that when both pointers then move together one step at a time, the lead pointer is always exactly `C` steps (one full cycle) ahead of the trailing pointer. Once the trailing pointer reaches the cycle start (after `L` steps), the lead pointer — having also moved `L` steps from position `C` — is at `C + L`, which is `L` steps into the cycle from the start, i.e., the same node modulo `C`. Since both pointers are now moving in lockstep inside the cycle at the same relative offset, they meet exactly at the cycle's start node. ∎

### Variants / Use Cases
- **Floyd's Cycle Detection** → simpler slow/fast (1x/2x speed) alternative; Brent's typically does fewer node accesses
- **Pollard's Rho for integer factorization** → uses Brent's cycle detection to efficiently find cycles in the pseudorandom sequence for factoring
- **Detecting cycles in iterated functions (f(f(...f(x))))** → general technique beyond linked lists, useful for hash chains and PRNG period detection
- **Finding cycle length only (without start)** → just the first phase of Brent's algorithm suffices
- **Duplicate number detection in arrays** → same index-chasing trick as Floyd's, can use Brent's for fewer comparisons
