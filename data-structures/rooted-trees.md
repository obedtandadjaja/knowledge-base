# Rooted Trees
A rooted tree has one initial node which branches out to other nodes, forming a tree. If a tree root is nil then the tree is empty.

## Terminology
`Leaf`: nodes without any children

`Depth`: count of how many nodes the root goes through to get to the leaf

`Balanced tree`: a tree where its left and right subtrees only differ in depth by 1

## Binary Tree
Tree node only has two children.

Binary search tree follows a policy where the left node must be smaller than the parent node's and the right node's value.

![picture](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/1_fvAa2lIvPcl3pEF0EwjT_g.png?raw=true)

## Left-child Right-sibling Tree
Each node has 3 pointers: parent, left, right.

Left pointer will point to the child, right pointer will point to its sibling, and parent pointer will point to its parent.

This scheme is usually used for trees with unbounded branching. This strategy allows any n-node to have the same space (not one or two nodes have overwhelmingly larger size).

![picture](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/Tree_LeftChild_RightSibling_Represenation.png?raw=true)
