
# Algorithms

## Sorting

- **Quick sort:**
- **Heap sort:** Convert to a heap, then keep popping values out of the heap.
- **Merge sort:** Keep splitting the input in half, look in left and right and decide where each goes.
- **Insertion sort:** consider the next item, keep swapping it left until the next left value is less than it.
- **Bubble sort:** repeatedly swap consecutive values.
- Hybrid
- **Selection sort:** find the smallest in what remains, and swap it with the next one.

## Data Structures

- Stacks: last in, first out.  Tends to use a linked list if the size is unbound.
- Queues: first in, first out.  Tends to use a linked list if the size is unbound.  A slice works if the size is bound.
- Heap: Uses a slice to model a tree structure.  Performs in O(lg(n)) to find the first item, or to add another item.
- Binary search: with an ordered array, finds an item in O(lg(n))
- Binary trees: holds value, left node, and right node.
    - keeping it balanced is the vast majority of the complexity.  If you must code one from scratch, 
      it is easier to just shuffle your input instead of guaranteeing it's balanced.
- Graphs
- Breadth first search: requires using a queue
- Hashing
- Distributed Sets
- String searching
- Trie
- Splay Tree

## Algorithms

- distributed sets: all elements get their own set.  They get joined together by electing one "leader" of the set, which is itself 
  at the start.  Each node keeps track of its set leader.  If the set leader is retrieved and has changed, the node is updated.
- dynamic programming: Tends to handle problems with exponentially growing complexity, when the problem can be decomposed to 
  a set of similar sub-problems.  Tends to use some kind of cache to memorize the solutions of sub-problems.
- greedy algorithms: At every step, the algorithm takes a choice that is most optimal.  It doesn't need to consider
  past decisions or future ramifications.
- Boyer Moore: quickly gets the element that takes over 50% of the values.
