# Construct a Full Binary Tree from Preorder and Preorder Mirror Traversals

**Difficulty:** Medium
**Topics:** Tree, Binary Tree, Recursion, Hashing

## Problem Statement

Given two arrays `pre[]` and `preMirror[]` of size `n` containing unique elements, where:

- `pre[]` represents the **preorder traversal** of a **full binary tree**
- `preMirror[]` represents the **preorder traversal of its mirror tree**

Construct the original full binary tree using these two traversals and return its root.

> **Note:** A general binary tree cannot be uniquely constructed from just these two traversals. However, since the tree is guaranteed to be a **full binary tree** (every node has either 0 or 2 children), it can be constructed uniquely and without ambiguity.

### Constraints

```
1 ≤ pre.size() ≤ 10^5
0 ≤ pre[i] ≤ 10^9
1 ≤ preMirror.size() ≤ 10^5
0 ≤ preMirror[i] ≤ 10^9
```

---

## Examples

### Example 1

**Input:**
```
pre[]       = [0, 1, 2]
preMirror[] = [0, 2, 1]
```

**Output:**
```
[0, 1, 2]
```

**Explanation:** The tree looks like:

```
        0
       / \
      1   2
```

### Example 2

**Input:**
```
pre[]       = [1, 2, 4, 5, 3, 6, 7]
preMirror[] = [1, 3, 7, 6, 2, 5, 4]
```

**Output:**
```
[1, 2, 4, 5, 3, 6, 7]
```

**Explanation:** The tree looks like:

```
            1
          /   \
         2     3
        / \   / \
       4   5 6   7
```

---

## Intuition

For a full binary tree:

- `pre[]` visits nodes in the order: **root → left subtree → right subtree**
- `preMirror[]` visits nodes in the order: **root → right subtree → left subtree** (since mirroring swaps left and right at every node)

This means that inside any subtree's range in `preMirror[]`, the **left subtree's mirrored preorder always occupies the tail end** of that range, and its first element is exactly the *next* unused value in `pre[]` (because `pre[]` always processes the left child immediately after the root).

So, by locating that value's position in `preMirror[]`, we can split the current range into the right subtree's portion and the left subtree's portion, and recurse.

---

## Algorithm

1. Build a hashmap `pos` that maps every value in `preMirror[]` to its index, for O(1) lookups.
2. Maintain a pointer `preIndex` that tracks the next node to consume from `pre[]`.
3. Recursive function `build(preStart, preEnd)` over the current range of `preMirror[]`:
   - If the range is invalid or `pre[]` is exhausted, return `None`.
   - Take `pre[preIndex]` as the current `root`, then increment `preIndex`.
   - If `preStart == preEnd`, this is a leaf node — return it directly.
   - Otherwise, look up `mid = pos[pre[preIndex]]` — the index in `preMirror[]` where the left subtree's mirrored preorder begins.
   - Recurse:
     - `root.left  = build(mid, preEnd)`
     - `root.right = build(preStart + 1, mid - 1)`
4. Return the root returned by `build(0, n - 1)`.

---

## Python Code

```python
'''
Structure of Binary Tree Node
class Node:
    def __init__(self, val):
        self.data = val
        self.right = None
        self.left = None
'''

class Solution:
    def constructBinaryTree(self, pre, preMirror):
        n = len(pre)
        pos = {val: idx for idx, val in enumerate(preMirror)}
        self.preIndex = 0

        def build(preStart, preEnd):
            if self.preIndex >= n or preStart > preEnd:
                return None

            root = Node(pre[self.preIndex])
            self.preIndex += 1

            if preStart == preEnd:
                return root

            mid = pos[pre[self.preIndex]]

            root.left = build(mid, preEnd)
            root.right = build(preStart + 1, mid - 1)

            return root

        return build(0, n - 1)
```

---

## Code Explanation

| Part | Purpose |
|---|---|
| `pos = {val: idx for idx, val in enumerate(preMirror)}` | Precomputes value → index mapping for `preMirror[]` so we can find `mid` in O(1) instead of scanning the array each time. |
| `self.preIndex` | Shared pointer across recursive calls tracking how far we've consumed `pre[]`. Stored as an instance attribute since Python doesn't allow simple int mutation inside nested functions without `nonlocal`/attribute tricks. |
| `root = Node(pre[self.preIndex]); self.preIndex += 1` | Every recursive call consumes exactly one value from `pre[]` — this is always the root of the current subtree. |
| `if preStart == preEnd: return root` | Base case — a range of size 1 means this node has no children (leaf), since the tree is full. |
| `mid = pos[pre[self.preIndex]]` | The *next* value in `pre[]` is the first node of the left subtree. Its position in `preMirror[]` tells us where the left subtree's block starts (and hence where the right subtree's block ends). |
| `root.left = build(mid, preEnd)` | The left subtree's mirrored preorder occupies `preMirror[mid ... preEnd]`. |
| `root.right = build(preStart + 1, mid - 1)` | The right subtree's mirrored preorder occupies `preMirror[preStart+1 ... mid-1]` (right after the root, before the left subtree's block). |

### Dry Run (Example 2)

```
pre       = [1, 2, 4, 5, 3, 6, 7]
preMirror = [1, 3, 7, 6, 2, 5, 4]
pos       = {1:0, 3:1, 7:2, 6:3, 2:4, 5:5, 4:6}
```

1. `build(0, 6)` → root = `1`, preIndex = 1
   Next `pre` value = `2` → `mid = pos[2] = 4`
   - `left  = build(4, 6)` → covers slice `[2, 5, 4]`
   - `right = build(1, 3)` → covers slice `[3, 7, 6]`

2. `build(4, 6)` → root = `2`, preIndex = 2
   Next `pre` value = `4` → `mid = pos[4] = 6`
   - `left  = build(6, 6)` → root = `4` (leaf, preIndex = 3)
   - `right = build(5, 5)` → root = `5` (leaf, preIndex = 4)

3. `build(1, 3)` → root = `3`, preIndex = 5
   Next `pre` value = `6` → `mid = pos[6] = 3`
   - `left  = build(3, 3)` → root = `6` (leaf, preIndex = 6)
   - `right = build(2, 2)` → root = `7` (leaf, preIndex = 7)

**Final tree:**

```
            1
          /   \
         2     3
        / \   / \
       4   5 6   7
```

Preorder of the reconstructed tree: `[1, 2, 4, 5, 3, 6, 7]` ✅ matches `pre[]`.

---

## Complexity Analysis

| Metric | Complexity | Reason |
|---|---|---|
| **Time** | `O(n)` | Each node is visited exactly once; `mid` lookup is O(1) via hashmap. |
| **Space (hashmap)** | `O(n)` | Storing index positions for all `n` elements of `preMirror[]`. |
| **Space (recursion stack)** | `O(h)` | Where `h` is the height of the tree — O(log n) for a balanced tree, O(n) worst case for a skewed full binary tree. |

---

## How to Run

```bash
python3 solution.py
```

Example driver code to test:

```python
def preorder(root):
    if not root:
        return []
    return [root.data] + preorder(root.left) + preorder(root.right)

if __name__ == "__main__":
    pre = [1, 2, 4, 5, 3, 6, 7]
    preMirror = [1, 3, 7, 6, 2, 5, 4]

    sol = Solution()
    root = sol.constructBinaryTree(pre, preMirror)

    print(preorder(root))  # Expected: [1, 2, 4, 5, 3, 6, 7]
```

---

## License

This project is open source and available for educational use.
