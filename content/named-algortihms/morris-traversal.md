---
title: Morris Traversal
---

**Purpose:** Traverse a binary tree in-order (or pre-order) without recursion or an explicit stack, using O(1) extra space, in O(N) time.

### Algorithm
1. Start at the root as the current node.
2. If the current node has no left child, visit it and move to its right child.
3. If it has a left child, find its **in-order predecessor** (the rightmost node in its left subtree) by walking right from the left child until reaching a node with no right child (or one that already points back to the current node).
4. If the predecessor's right pointer is `null`, set it to point to the current node (a temporary "thread"), then move to the left child — this thread lets the algorithm return to the current node later without a stack.
5. If the predecessor's right pointer already points to the current node, this means the left subtree has been fully processed: remove the thread (restore `null`), visit the current node, and move to its right child.
6. Repeat until the current node becomes `null`.

### Code
```cpp
vector<int> morrisInorder(TreeNode* root) {
    vector<int> result;
    TreeNode* cur = root;

    while (cur) {
        if (!cur->left) {
            result.push_back(cur->val);
            cur = cur->right;
        } else {
            TreeNode* pred = cur->left;
            while (pred->right && pred->right != cur) pred = pred->right;

            if (!pred->right) {
                pred->right = cur;
                cur = cur->left;
            } else {
                pred->right = nullptr;
                result.push_back(cur->val);
                cur = cur->right;
            }
        }
    }
    return result;
}
```

### Paradigm
**Transform and Conquer.** The algorithm temporarily transforms the tree structure itself (by threading predecessor-to-current links) to simulate the effect of a call stack, eliminating the need for auxiliary recursion or an explicit stack, then reverses the transform once each thread has served its purpose.

### Complexity
- Time: O(N) — each edge is traversed at most twice (once to create the thread, once to remove it)
- Space: O(1) extra (beyond output), since no recursion stack or explicit stack is used

### Proof of Correctness
**Why finding the in-order predecessor correctly identifies where to "return":** In a standard in-order traversal, after fully processing a node's left subtree, control returns to that node before proceeding to its right subtree. The in-order predecessor (rightmost node in the left subtree) is exactly the last node visited before returning to the current node in a normal in-order traversal — so threading its right pointer to the current node exactly replicates the "return address" a call stack would otherwise store.

**Why thread creation and removal correctly toggles between the two visits to the same node:** The first time a node with a left child is reached, its predecessor's right pointer is `null`, so the algorithm creates the thread and descends left — this correctly defers visiting the current node until its entire left subtree is processed. The second time the same node is reached (now arriving via the thread from the predecessor, after the left subtree is fully processed), the predecessor's right pointer already equals the current node, signaling that it's now safe to visit the current node and remove the thread (restoring the tree to its original structure) before moving right.

**Why the tree is correctly restored:** Every thread created is explicitly removed exactly once, when it's used to detect the return — so after the full traversal, no thread pointers remain, and the tree structure is bit-for-bit identical to before the traversal began.

**Why total work is O(N):** Each edge in the tree is traversed at most twice: once while walking down to find a predecessor (or during normal descent), and at most once more while walking through an existing thread to detect a previously created one — this bounds total pointer movements to O(N), even though it may look superlinear from the repeated predecessor-finding walks. ∎

### Variants / Use Cases
- **Morris Pre-order Traversal** → same threading idea, but the node is visited before descending left rather than after
- **Morris traversal for space-constrained embedded systems** → useful where recursion/stack space is severely limited
- **Detecting/validating BST properties in O(1) space** → in-order Morris traversal lets you check sorted order without extra memory
- **Threaded binary trees (persistent variant)** → some tree structures permanently keep such threads for faster traversal, rather than creating/removing them temporarily
- **Flattening a binary tree to a linked list in O(1) space** → related pointer-rewiring technique
