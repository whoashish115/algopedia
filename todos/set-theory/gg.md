
---
title: Base
---

##  Set Theory Operations via `O(1)` Bitwise Logic

Let `A` and `B` be two bitmasks representing subsets:

- **Intersection** (`A ∩ B`): `A & B`
- **Union** (`A ∪ B`): `A | B`
- **Symmetric Difference** (`A Δ B`): `A ^ B`
- **Set Difference** (`A \ B`): `A & ~B`
- **Subset Check** (`A ⊆ B`): `(A & B) == A` (equivalently `(A|B)==B`)
