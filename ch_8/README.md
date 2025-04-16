# A balancing act

A balanced binary search tree (BST) is a type of binary tree (check chapter 7) which combines the best of both sorted arrays and linked lists, i.e., a data structure with the search speed of a sorted array but with a faster insertion speed.

|          | Search   | Insert   |
|----------|----------|----------|
| Sorted array   | $O(\log n)$   | $O(n)$   |
| Linked list    | $O(n)$        | $O(1)$   |
| BST            | $O(\log n)$   | Faster than $O(n)$   |



Here is an example of a BST

```
    10
   /  \
  5    20
 / \     \
2   7    25
```
* Like in any binary tree, each node has up to two children, called *left* and *right* children.
* The BST-specific property is that the value of the left child is **always smaller* than its parent node, and the value of the right child is **always greater**

Thus, for node 10, its left child has a smaller value (5) and its right child has a bigger value (20). Not only that, all the numbers in the left child subtree are smaller than the node! (and conversely, all the numbers in the right child subtree are greater). This special property can be exploited for very fast searches.

For example, let us check whether 7 is in the tree.
* We start at the root node (10)
* 7 is smaller than 10, so we check the left subtree
* We are now at the 5 node
* 7 is greater than 5, so we check the right subtree
* There it is our 7!

If we wanted to check whether 8 is in the tree, we would retrace our steps, but in the end we would see that 8 is nowhere to be found.

The whole reason we are dealing with trees is to see whether they are faster than arrays and linked list. Let us now consider the performance of this tree, which depends on its height.

# Shorter trees are faster

Let us look at two trees. They both have seven nodes, but the performance is very different.

```
     O
    / \     1
   O   O
  / \ / \   2
 O  O O  O

 Height: 2
```

```
O
 \                 1
  O
   \               2
    O
     \             3
      O
       \           4
        O
         \         5
          O
           \       6
            O

  Height: 6
```


The height of the best-case tree is 2. This means that from the root node you can get to any node in, at most, two steps. The height of the worst-case tree is 6, which means that at most, it will take 6 steps. The worst-case tree is taller, and it has worse performance.

More generally, in the worst-case tree, the nodes are all in a line, and the height is $O(n)$, so searches will take $O(n)$ time. This worst-case tree can be thought of as a linked list, since one node just points to another. The best-case tree has height $O(\log n)$, and searching this tree will take $O(\log n)$ time.

If we can guarantee that the height of our tree will be $O(\log n)$, then searching the tree will always be $O(\log n)$. But how can we guarantee this? Here is an example where we build a tree that ends up being a worst-case tree. We start from 10, and then we add 20, 30, 40...

```
10
  \
   20
     \
      30
        \
         40
           \
            50
              \
              60
```

We keep having to add these nodes to the right since they are all bigger than the other nodes. But this results in a worst-case tree! The shortest a BST can be is $O(\log n)$, and we can shorten them by balancing. Let us look at a balanced BST.

# AVL trees: A type of balanced tree

AVL trees are a type of self-balancing BST, named after inventors Adelson-Velsky and Landis. "Self-balancing" means that the heights of the two child subtrees of any node differ by at most one, effectively maintaining a height of $O(\log n)$. Whenever the tree is out of balance --that is, its height is not $O(\log n)$-- it will correct itself. For the last example, the tree may balance itself to look like this.

```
   20
  /  \
10   40
     / \
    30 50
```
An AVL tree will give us the desired $O(\log n)$ height we want by balancing itself through rotations.

## Rotations

Suppose you have a tree with three nodes. Any one of them could be the root node.

```
Tree A:           Tree B:           Tree C:
    A                B                 C
     \              / \               /
      B            A   C             B
       \                            /
        C                          A
```

These trees are related by *rotation*. In particular, from tree A to tree B, we have *rotated to the left*. Rotations are a popular to balance trees, such as in AVL trees. We started with an unbalanced tree with A as the root node and ended up with a balanced tree with B as the root node.

## How does the AVL tree know when it is time to rotate?

For the tree to know when it is time to balance itself (as well as which node should be rotated), it needs to store some additional information. Each node must store one of two pieces of information: its height or something called a *balance factor*, which can be -1, 0, or 1, and denotes whether the node is balanced, or whether is left- or right- biased. A difference of 1 (from 0) is acceptable, but any further deviations means that the tree should be rebalanced.

```
Tree A:           Tree B:           Tree C:
Balance factors:

    2                0                 -2

    A                B                 C
     \              / \               /
      B            A   C             B
       \                            /
        C                          A
```

Each node needs to store either the height or the balance factor, since it is easy to compute one if we know the other. For the next example, we will show both of them for clarity.

Consider the following tree



```
     20
    / \
  10   30
  /
 5
```

| Node  | Height  | Balance<br>factor  |
|---|---|---|
| 20  |  2 |  -1 |
| 10  |  1  |  -1 |
| 30  |  0 |   0|
| 5   |  0 |  0 |

After adding an additional node, we will update the values for height and balance factor, starting from the bottom leaves

```
      20
     / \
   10   30
   /
  5
 /
2
```

| Node  | Height  | Balance<br>factor  |
|---|---|---|
| 20  |  ? |  ? |
| 10  |  2  |  <span style="color:red">-2</span> |
| 30  |  0 |   0|
| 5   |  1 |  1 |
| 2   |  0 |  0 |

Ah, balance factor is -2, time to rebalance! Note how we will rotate the **problematic** node, 10. 

```
      20
     / \
    5  30
   / \
  2  10
```

| Node  | Height  | Balance<br>factor  |
|---|---|---|
| 20  |  2 |  -1 |
| 5  |  1  |  0 |
| 30  |  0 |   0|
| 2   |  0 |  0 |
| 10   |  0 |  0 |

If updated after every new insertion, AVL trees require, at most, one rebalancing. AVL trees are a good option if we need a balanced BST.

Let us summarize the content so far:
* Binary trees are a type of tree
* In binary trees, each node has, at most, two children
* BST (Binary Search Trees) are a type of binary tree where all the values in the left subtree are smaller than the node, and all the values in the right are great than the node
* BST can give great performance if we can guarantee their height will be $O(\log n)$
* AVL trees (Adelson-Velsky and Landis) are self-balancing BSTs, guaranteeing their height will be $O(\log n)$
* AVL trees balance themselves through rotations.

We have shown that AVL trees offer $O(\log n)$ search performance. What about insertions? Insertions are just a matter of searching for a place to insert the node and adding a pointer, just like a linked list, so they are $O(\log n)$ as well. Here is a summary of the performance of the data structures we have seen thus far:

|          | Search   | Insert   |
|----------|----------|----------|
| Sorted array   | $O(\log n)$   | $O(n)$ |
| Linked list    | $O(n)$        | $O(1)$ |
| BST            | $O(\log n)$   | $O(\log n)$ |

# Splay trees

While AVL trees are a good basic balanced BST that guarantees $O(\log n)$ time for a number of operations, Splay trees allow for faster searches for recently searched items.

Splay trees achieve this by making the newly-searched node into the new root. More generally, nodes that have been looked up recently get clustered to the top and become faster to look up.

The tradeoff is that Splay trees are not guaranteed to be balanced, and that *some* searches might take longer than $O(\log n)$ time. Some searches might even take as long as linear time (worst case)! Additionally, while performing the search, we may need to rotate the node up to the root if it is not already the root, which incurs additional overhead.

The tradeoff can be advantageous since if we do $n$ searches, the total running time will be $O(n \log n)$ *guaranteed*, this is, $O(\log n)$ per search.

# B-trees

B-trees are a generalized form of binary tree. Here is a B-tree.

```
            _____(9)_____
           /             \
     ( 3|6 )              (12|15)
    /   |   \            /   |   \
(1|2) (4|5) (7|8) (10|11) (13|14) (16|17)
```

Its most notable features are:
* Some of the nodes have more than two children (unlike regular binary trees)
* Most nodes have more than one key

## What is the advantage of B-trees?

B-trees have a very interesting optimization because it is a physical optimization. Since computers are physical objects, when we look for a node in a tree, a physical object has to move to retrieve that data; this overhead is called *seek time*. Seek time can be a big factor in how fast or slow an algorithm is.

The fundamental idea with B-trees is that *once we have done a search, we might as well read adjacent data into memory*. B-trees have larger nodes, where each node can have many more keys and children than a binary tree. Thus, we spend more time reading each node, *but we seek less because we read more data in one go*. This is what makes B-trees faster. B-trees are a popular data structure for databases, which is no surprise as databases spend a lot of time retrieving data from a disk.

The ordering of the B-tree is also interesting. Starting at the lower-left, we can snake across the whole tree in increments of 1. Doing so will reveal a property typical of BST, where for each key, the keys to the left are smaler, and the keys to the right are larger. For example, for key 3, the keys to the left are 1 and 2, and the keys to the right are 4 and 5.

Also, notice that the number of children is one greater than the number of keys. So the root node has one key and two children. Each of those children has two keys and three children. And so on.

In summary, B-trees are a form of generalized BST, where each node can have multiple keys and multiple child nodes. This is so because B-trees try to minimize seek time by reading more data in one go.
