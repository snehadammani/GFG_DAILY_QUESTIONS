# Longest Consecutive Path in Binary Tree

## Problem Statement

Given the root of a Binary Tree, find the length of the longest path consisting of connected nodes such that each next node (child) has a value exactly **1 greater** than its parent.

- The path must move strictly from **parent to child** (top to bottom) — never sideways or upward.
- Values along the path must be strictly increasing by exactly 1 at each step.
- If no such path of at least 2 connected nodes exists, return **-1**.

**Constraints:**
- 1 ≤ number of nodes ≤ 10⁵
- 1 ≤ node value ≤ 10⁵

---

## Algorithm / Approach

The key observation: since the path can only go **downward** (parent → child, never branching back up through a node), this is fundamentally different from problems like "tree diameter." At any node, you can extend the chain into **at most one** child — whichever one has value `node.data + 1`. You cannot combine a left-side chain and a right-side chain through the same node, because that would require going up from one child and back down the other, which breaks the "parent to child only" rule.

So the approach is a **post-order DFS** where each node reports back the length of the longest consecutive chain that **starts at itself and goes downward**:

1. **Base case:** If the node is `None`, its chain length is `0`.
2. **Recurse** on the left and right children first to get `left_len` and `right_len` (their downward chain lengths).
3. At the current node, start with `cur_len = 1` (the node by itself).
4. **Check left child:** if `left.data == node.data + 1`, the chain can extend through the left child, so `cur_len = max(cur_len, 1 + left_len)`.
5. **Check right child:** similarly, if `right.data == node.data + 1`, `cur_len = max(cur_len, 1 + right_len)`.
6. **Update the global answer** `max_len = max(max_len, cur_len)` — because the longest chain overall might start at any node in the tree, not necessarily the root.
7. **Return** `cur_len` up to the parent, so the parent knows how far it can extend through this node.
8. At the end, if `max_len` is still `1` (meaning no two connected nodes ever formed a valid consecutive pair), return `-1`; otherwise return `max_len`.

---

## Example

**Input:** `root = [1, 2, 3]`
Tree:
```
        1
       / \
      2   3
```
- Node `2` (leaf): `cur_len = 1`
- Node `3` (leaf): `cur_len = 1`
- Node `1`: left child is `2 = 1 + 1` ✅ → `cur_len = max(1, 1 + 1) = 2`. Right child is `3 ≠ 1 + 1` ❌.
- `max_len = 2`

**Output:** `2` (the path `1 → 2`)

**Input:** `root = [10, 20, 30, 40, N, 60, 90]`
```
              10
             /  \
           20    30
          /      /  \
        40      60   90
```
No parent-child difference equals exactly 1 anywhere (10→20 is +10, 20→40 is +20, 30→60 is +30, 30→90 is +60).
- Every node's `cur_len` stays at `1`, so `max_len = 1`.

**Output:** `-1`

---

## Solution (Python)

```python
'''
Structure of Binary Tree Node
class Node:
    def __init__(self, val):
        self.right = None
        self.data = val
        self.left = None
'''
class Solution:
    def longestConsecutive(self, root):
        self.max_len = 1

        def dfs(node):
            if node is None:
                return 0

            left_len = dfs(node.left)
            right_len = dfs(node.right)

            cur_len = 1
            if node.left and node.left.data == node.data + 1:
                cur_len = max(cur_len, 1 + left_len)
            if node.right and node.right.data == node.data + 1:
                cur_len = max(cur_len, 1 + right_len)

            self.max_len = max(self.max_len, cur_len)
            return cur_len

        dfs(root)
        return -1 if self.max_len == 1 else self.max_len
```

---

## Code Explanation

- **`self.max_len = 1`**: A global tracker (across the whole recursion) initialized to `1`, representing "just a single node" as the trivial minimum chain.

- **`dfs(node)`**: A helper function that does two things at once:
  - Returns the length of the longest downward consecutive chain **starting exactly at `node`**.
  - As a side effect, updates `self.max_len` if it finds a longer chain anywhere in the subtree.

- **`if node is None: return 0`**: An empty subtree contributes a chain length of `0` to its parent (so the parent's `1 + 0 = 1` correctly represents "just the parent, no valid extension").

- **`left_len = dfs(node.left)`** and **`right_len = dfs(node.right)`**: Post-order traversal — we need the children's results *before* we can decide what the current node's chain length is.

- **`cur_len = 1`**: By default, assume the node forms a chain of length 1 (itself, no valid extension found yet).

- **`if node.left and node.left.data == node.data + 1`**: Checks whether the left child continues the consecutive sequence. If yes, the chain through this node could be `1 (this node) + left_len (chain from left child downward)`.

- **`if node.right and ...`**: Same check for the right side. Note this is a separate `if`, not `elif` — but only one of them can actually end up contributing to `cur_len`'s final value at a time since `cur_len` just takes the max at each step (both are evaluated independently, and whichever is larger wins).

- **`self.max_len = max(self.max_len, cur_len)`**: This is crucial — the best chain in the entire tree might not run through the root at all. It could start deep inside the tree. So we check at *every* node, not just at the end.

- **`return cur_len`**: Pass this node's chain length up to its parent so the parent can decide whether to extend through it.

- **Final return**: `-1` if no node ever found a valid 2-node extension (`max_len` never grew past `1`), otherwise `max_len`.

---

## Time and Space Complexity

- **Time Complexity:** `O(n)` — each node is visited exactly once during the DFS, and the work done at each node (comparisons, max operations) is `O(1)`.
- **Space Complexity:** `O(h)`, where `h` is the height of the tree — this accounts for the recursion call stack.
  - Worst case (skewed/degenerate tree): `O(n)`
  - Best case (balanced tree): `O(log n)`
