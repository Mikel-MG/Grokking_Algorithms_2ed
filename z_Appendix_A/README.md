# Performance of AVL trees

AVL trees (Adelson-Velsky and Landis) are a type of self-balancing tree that we introduced in chapter 8. AVL trees offer $O(\log_{n})$ search performance, but there is an interesting nuance to this.

Consider this perfectly balanced tree, which features 15 nodes and a height of 3,

```
       O
   O       O
 O   O   O   O
O O O O O O O O
```

and this AVL tree, which also features 15 nodes but has a height of 4. Remember that AVL trees do not need to be perfectly balanced, just to satisfy a "balance condition", such that the height difference between any two subtrees is less than or equal to 1!

```
        O
    O       O
  O   O   O   O
 O O O . O . O .
 OO O
```
(The dots represent holes in the tree)

Now, how is it possible that both these trees have a performance of $O(\log_{n})$, if performance in trees is tied to height? The answer is related to the base of the log operation.

The perfectly balanced tree has a performance of $O(\log_{n})$, where the base of the log is 2, just like binary search. This can be intuitively deduced from the fact that each level doubles the nodes plus one. Thus, a perfectly balanced tree of height 1 has 3 nodes, of height 2 has 7 nodes, of height 3 has 15, and so on. In other words, each layer adds a number of nodes equal to a power of 2.

On the other hand, the AVL tree has some gaps on it, so each level adds fewer than double the nodes. This is why their performance is also $O(\log_{n})$, but the log base is phi ($\phi$, also known as the golden ratio, ~1.618).

Thus, AVL trees do not offer exactly as good performance as perfectly balanced trees, but the performance is very close, since both are $O(\log_{n})$.

>Note: The golden ratio, the growth of AVL trees in their barely balanced state, and the Fibonacci sequence are tightly connected. This seems like a fascinating rabbit hole to explore one day.
