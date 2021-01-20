# Binary Tree

This set of Data Structure Multiple Choice Questions & Answers (MCQs) focuses on Binary Tree Properties.

(Q-1-2) *The number of edges from the root to the node is called __________ of the tree.*

- Height
- Depth
- Length
- Width

(Q-2-1) *The number of edges from the node to the deepest leaf is called _________ of the tree.*

- Height
- Depth
- Length
- Width

(Q-3-1) *What is a full binary tree?*

- Each node has exactly zero or two children
- Each node has exactly two children
- All the leaves are at the same level
- Each node has exactly one or two children

(Q-4-3) *What is a complete binary tree?*

- Each node has exactly zero or two children
- A binary tree, which is completely filled, with the possible exception of the bottom level, which is filled from right to left
- A binary tree, which is completely filled, with the possible exception of the bottom level, which is filled from left to right
- A tree In which all nodes have degree 2

Explanation: A binary tree, which is completely filled, with the possible exception of the bottom level, which is filled from left to right is called complete binary tree. A Tree in which each node has exactly zero or two children is called full binary tree. A Tree in which the degree of each node is 2 except leaf nodes is called perfect binary tree.

(Q-5-4) *What is the average case time complexity for finding the height of the binary tree?*

- h = O(loglogn)
- h = O(nlogn)
- h = O(n)
- h = O(log n)

Explanation: The nodes are either a part of left sub tree or the right sub tree, so we don’t have to traverse all the nodes, this means the complexity is lesser than n, in the average case, assuming the nodes are spread evenly, the time complexity becomes O(logn).

(Q-6-4) *Which of the following is not an advantage of trees?*

- Hierarchical structure
- Faster search
- Router algorithms
- Undo/Redo operations in a notepad

Explanation: Undo/Redo operations in a notepad is an application of stack. Hierarchical structure, Faster search, Router algorithms are advantages of trees.

(Q-7-2) *In a full binary tree if number of internal nodes is I, then number of leaves L are?*

- L = 2*I
- L = I + 1
- L = I – 1
- L = 2*I – 1

Explanation: Number of Leaf nodes in full binary tree is equal to 1 + Number of Internal Nodes i.e L = I + 1

(Q-8-4) *In a full binary tree if number of internal nodes is I, then number of nodes N are?*

- N = 2*I
- N = I + 1
- N = I – 1
- N = 2*I + 1

Explanation: Relation between number of internal nodes(I) and nodes(N) is N = 2*I+1.

(Q-9-4) *In a full binary tree if there are L leaves, then total number of nodes N are?*

- N = 2*L
- N = L + 1
- N = L – 1
- N = 2*L – 1

Explanation: The relation between number of nodes(N) and leaves(L) is N=2*L-1.

(Q-10-4) *Which of the following is incorrect with respect to binary trees?*

- Let T be a binary tree. For every k ≥ 0, there are no more than 2k nodes in level k
- Let T be a binary tree with λ levels. Then T has no more than 2λ – 1 nodes
- Let T be a binary tree with N nodes. Then the number of levels is at least ceil(log (N + 1))
- Let T be a binary tree with N nodes. Then the number of levels is at least floor(log (N + 1))

(Q-12-2) *Construct a binary search tree by using postorder sequence given below.*

Postorder: 2, 4, 3, 7, 9, 8, 5.

- 
  ~~~[tree](itemShape=circle,itemSize=30,height=200,width=300)
  1:(5)
  2:(1)|3:(8)
  4:(2)|5:(4)|6:(7)|7:(9)
  ~~~

- 
  ~~~[tree](itemShape=circle,itemSize=30,height=200,width=300)
  1:(5)
  2:(3)|3:(8)
  4:(2)|5:(4)|6:(7)|7:(9)
  ~~~

- 
  ~~~[tree](itemShape=circle,itemSize=30,height=200,width=300)
  1:(5)
  2:(3)|3:(9)
  4:(2)|5:(4)|6:(7)|7:(8)
  ~~~

- 
  ~~~[tree](itemShape=circle,itemSize=30,height=200,width=300)
  1:(5)
  2:(3)|3:(7)
  4:(2)|5:(4)|6:(8)|7:(9)
  ~~~

(Q-13-1) *Construct a binary tree using inorder and level order traversal given below.*

Inorder Traversal: 3, 4, 2, 1, 5, 8, 9

Level Order Traversal: 1, 4, 5, 9, 8, 2, 3

- 
  ~~~[tree](itemShape=circle,itemSize=30,height=200,width=300)
  1:(1)
  2:(4)|3:(5)
  4:(9)|5:(8)|6:(2)|7:(3)
  ~~~

- 
  ~~~[tree](itemShape=circle,itemSize=30,height=200,width=300)
  1:(1)
  2:(5)|3:(4)
  4:(9)|5:(8)|6:(2)|7:(3)
  ~~~

- 
  ~~~[tree](itemShape=circle,itemSize=30,height=200,width=300)
  1:(1)
  2:(4)|3:(5)
  4:(8)|5:(9)|6:(2)|7:(3)
  ~~~

- 
  ~~~[tree](itemShape=circle,itemSize=30,height=200,width=300)
  1:(1)
  2:(4)|3:(5)
  4:(8)|5:(9)|6:(3)|7:(2)
  ~~~
