# Shortest Unique Prefix for Every Word

## Problem Statement

Given an array of strings `arr[]`, find the **shortest prefix** of each string that uniquely identifies it among all strings in the array.

A prefix is **unique** if it is not a prefix shared by any other string in the array.

> **Note:** No string in the given array is a prefix of another string.

---

## Examples

### Example 1

**Input**

```text
arr[] = {"zebra", "dog", "duck", "dove"}
```

**Output**

```text
z dog du dov
```

**Explanation**

- `z` uniquely identifies **zebra**
- `dog` uniquely identifies **dog**
- `du` uniquely identifies **duck**
- `dov` uniquely identifies **dove**

---

### Example 2

**Input**

```text
arr[] = {"geeksgeeks", "geeksquiz", "geeksforgeeks"}
```

**Output**

```text
geeksg geeksq geeksf
```

**Explanation**

- `geeksg` uniquely identifies **geeksgeeks**
- `geeksq` uniquely identifies **geeksquiz**
- `geeksf` uniquely identifies **geeksforgeeks**

---

## Constraints

- `1 ≤ arr.length ≤ 1000`
- `1 ≤ length of each word ≤ 1000`

---

# Approach

This problem can be solved efficiently using a **Trie (Prefix Tree)**.

Each Trie node stores:

- A dictionary of child nodes.
- A `count` variable representing how many words pass through that node.

### Idea

1. Insert every word into the Trie.
2. While inserting, increment the count of every visited node.
3. To find the shortest unique prefix of a word, traverse the Trie.
4. The first node whose `count == 1` gives the shortest unique prefix.

---

# Python Solution

```python
class TrieNode:
    def __init__(self):
        self.child = {}
        self.count = 0


class Solution:
    def findPrefixes(self, arr):
        root = TrieNode()

        # Build Trie
        for word in arr:
            node = root
            for ch in word:
                if ch not in node.child:
                    node.child[ch] = TrieNode()

                node = node.child[ch]
                node.count += 1

        ans = []

        # Find shortest unique prefix
        for word in arr:
            node = root
            prefix = ""

            for ch in word:
                node = node.child[ch]
                prefix += ch

                if node.count == 1:
                    break

            ans.append(prefix)

        return ans
```

---

# Dry Run

### Input

```text
["zebra", "dog", "duck", "dove"]
```

### Trie Counts

```text
z → 1

d → 3
 ├── o → 3
 │    ├── g → 1
 │    └── v → 1
 │          └── e → 1
 └── u → 1
```

Now search each word.

### Word = zebra

```
z → count = 1
```

Answer:

```
z
```

---

### Word = dog

```
d → 3
do → 3
dog → 1
```

Answer:

```
dog
```

---

### Word = duck

```
d → 3
du → 1
```

Answer:

```
du
```

---

### Word = dove

```
d → 3
do → 3
dov → 1
```

Answer:

```
dov
```

---

# Test Cases

### Test Case 1

```text
Input:
["zebra","dog","duck","dove"]

Output:
["z","dog","du","dov"]
```

---

### Test Case 2

```text
Input:
["geeksgeeks","geeksquiz","geeksforgeeks"]

Output:
["geeksg","geeksq","geeksf"]
```

---

### Test Case 3

```text
Input:
["apple","ape","april"]

Output:
["app","ape","apr"]
```

---

### Test Case 4

```text
Input:
["car","cat","can"]

Output:
["car","cat","can"]
```

---

# Time Complexity

| Operation | Complexity |
|-----------|------------|
| Building Trie | **O(N × L)** |
| Finding Prefixes | **O(N × L)** |
| Total | **O(N × L)** |

Where:

- **N** = Number of words
- **L** = Maximum length of a word

---

# Space Complexity

```text
O(N × L)
```

The Trie stores every character of every word once.

---

# Code Explanation

## Step 1: Create TrieNode

```python
class TrieNode:
    def __init__(self):
        self.child = {}
        self.count = 0
```

Each Trie node stores:

- `child` → Dictionary containing child nodes.
- `count` → Number of words passing through that node.

---

## Step 2: Build the Trie

```python
for word in arr:
    node = root
    for ch in word:
        if ch not in node.child:
            node.child[ch] = TrieNode()

        node = node.child[ch]
        node.count += 1
```

For every character:

- Create a new node if it doesn't exist.
- Move to that node.
- Increase its `count`.

The `count` helps determine how many words share the same prefix.

---

## Step 3: Find the Shortest Unique Prefix

```python
for word in arr:
    node = root
    prefix = ""

    for ch in word:
        node = node.child[ch]
        prefix += ch

        if node.count == 1:
            break
```

For each word:

- Traverse the Trie.
- Build the prefix character by character.
- Stop as soon as a node with `count == 1` is found.
- This prefix is unique because no other word passes through that node.

---

## Why Does This Work?

Suppose we have:

```text
dog
duck
dove
```

The Trie counts become:

```text
d  -> 3
do -> 3
dog -> 1
du -> 1
dov -> 1
```

- `dog` becomes unique only at `"dog"`.
- `duck` becomes unique at `"du"`.
- `dove` becomes unique at `"dov"`.

Thus, the first node with `count = 1` always gives the **shortest unique prefix**.

---

# Key Takeaways

- ✔️ Use a **Trie (Prefix Tree)** to efficiently store all words.
- ✔️ Maintain a `count` at every Trie node.
- ✔️ The first node with `count == 1` gives the shortest unique prefix.
- ✔️ Efficient solution with **O(N × L)** time complexity.

---

