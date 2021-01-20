# Binary tree questions

This set of Data Structure Multiple Choice Questions & Answers (MCQs) focuses on "Binary Tree Properties".

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

(Q-5-4) *What is the average case time complexity for finding the height of the binary tree?*

- h = O(loglogn)
- h = O(nlogn)
- h = O(n)
- h = O(log n)

(Q-6-4) *Which of the following is not an advantage of trees?*

- Hierarchical structure
- Faster search
- Router algorithms
- Undo/Redo operations in a notepad

(Q-7-2) *In a full binary tree if number of internal nodes is I, then number of leaves L are?*

- L = 2*I
- L = I + 1
- L = I – 1
- L = 2*I – 1

(Q-8-4) *In a full binary tree if number of internal nodes is I, then number of nodes N are?*

- N = 2*I
- N = I + 1
- N = I – 1
- N = 2*I + 1

(Q-9-4) *In a full binary tree if there are L leaves, then total number of nodes N are?*

- N = 2*L
- N = L + 1
- N = L – 1
- N = 2*L – 1

(Q-10-4) *Which of the following is incorrect with respect to binary trees?*

- Let T be a binary tree. For every k ≥ 0, there are no more than 2k nodes in level k
- Let T be a binary tree with λ levels. Then T has no more than 2λ – 1 nodes
- Let T be a binary tree with N nodes. Then the number of levels is at least ceil(log (N + 1))
- Let T be a binary tree with N nodes. Then the number of levels is at least floor(log (N + 1))

11. Construct a binary tree by using postorder and inorder sequences given below.
Inorder: N, M, P, O, Q
Postorder: N, P, Q, O, M

- a

(Q-12-2) *Construct a binary search tree by using postorder sequence given below.*

Postorder: 2, 4, 3, 7, 9, 8, 5.

- 
  ~~~[tree](itemShape=circle,itemSize=30,height=250)
  1:(5)
  2:(1)|3:(8)
  4:(2)|5:(4)|6:(7)|7:(9)
  ~~~
  
- 
  ~~~[tree](itemShape=circle,itemSize=30,height=250)
  1:(5)
  2:(3)|3:(8)
  4:(2)|5:(4)|6:(7)|7:(9)
  ~~~
  
- 
  ~~~[tree](itemShape=circle,itemSize=30,height=250)
  1:(5)
  2:(3)|3:(9)
  4:(2)|5:(4)|6:(7)|7:(8)
  ~~~
  
- 
  ~~~[tree](itemShape=circle,itemSize=30,height=250)
  1:(5)
  2:(3)|3:(7)
  4:(2)|5:(4)|6:(8)|7:(9)
  ~~~

(Q-13-1) *Construct a binary tree using inorder and level order traversal given below.*

Inorder Traversal: 3, 4, 2, 1, 5, 8, 9

Level Order Traversal: 1, 4, 5, 9, 8, 2, 3

- 
  ~~~[tree](itemShape=circle,itemSize=30,height=250)
  1:(1)
  2:(4)|3:(5)
  4:(9)|5:(8)|6:(2)|7:(3)
  ~~~
  
- 
  ~~~[tree](itemShape=circle,itemSize=30,height=250)
  1:(1)
  2:(5)|3:(4)
  4:(9)|5:(8)|6:(2)|7:(3)
  ~~~
  
- 
  ~~~[tree](itemShape=circle,itemSize=30,height=250)
  1:(1)
  2:(4)|3:(5)
  4:(8)|5:(9)|6:(2)|7:(3)
  ~~~
  
- 
  ~~~[tree](itemShape=circle,itemSize=30,height=250)
  1:(1)
  2:(4)|3:(5)
  4:(8)|5:(9)|6:(3)|7:(2)
  ~~~
  

