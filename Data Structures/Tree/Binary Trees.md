# Binary Search Tree
If the number of real nodes in the tree is the same as the number of “deleted” nodes, then the depth of the tree is only expected to go up by a small constant (why?), so there is a very small time penalty associated with lazy deletion. Also, if a deleted item is reinserted, the overhead of allocating a new cell is avoided #mark 
$$D(N)=D(i)+D(N-i-1)+N-1$$
then the average value of both $D(i)$ and $D(N-i-1)$ is $(1/N)\sum_{j=0}^{N-1}{D(j)}$
$$D(N) = \frac{2}{N} \bigg[\sum_{j=0}^{N-1}{D(j)\bigg]+N-1}$$
obtaining an average value of $D(N)=O(N.\log{N})$. thus the expected depth of any node is $O(\log{N})$
#### deletion operation
let the node to be remove is `x`
we have 3 cases :
- if `x` hasn't any child
	- delete x
- if `x` has one child
	- perform `removeAboveExternal` for x by linking `x's` child with `x's` parent
- if `x` has two children
	- 

### AVL Tree
let `w` the newly inserted node or the node to be removed
let `z` be the first unbalanced node we encounter in going up from the `w` toward the root
the violation might occur in 4 cases :
1. A modification into the left subtree of the left child of `z` `left-left`
2. A modification into the right subtree of the left child of `z` `left-right`
3. A modification into the left subtree of the right child of `z` `right-left`
4. A modification into the right subtree of the right child of `z` `right-right`

in the **single rotation** `b = y`
![[Pasted image 20241227175835.png]]
![[Pasted image 20241227175809.png]]
in the **double rotation** `b = x`
![[Pasted image 20241227175906.png]]
![[Pasted image 20241227175933.png]]
Since one rotation always suffices, a carefully coded nonrecursive version generally turns out to be faster than the recursive version, but on modern compilers the difference is not as significant as in the past. However, nonrecursive versions are quite difficult to code correctly, whereas a casual recursive implementation is easily readable

---
### Splay Tree
> Note : in all cases : maintain the inorder relationships of the nodes in T

there are 3 cases :
- if `X` hasn't a grandparent (`X's` parent is the root)
	- **`Zig`** : perform single rotation `x's child - x - root`
- if `X` has parent `P` and grandparent `G`
	- **`Zig-Zag`** : where `X` is a right child and `P` is a left child or vice-versa
		- perform double rotation `X - P - G`
	- **`Zig-Zig`** : where X and P are both left children or right
		- replace `G` by `X` making `P` a child of `X` and `G` a child of `P`

![[Pasted image 20241228174016.png]]
### B-Tree
A B-Tree of order M is an M-ary tree with the following properties :
_- Rules 3 & 5 must be relaxed for the first L insertions -_
1. the data items are stored at leaves
2. the non-leaf nodes store up to $M-1$ keys to guide the searching : key $i$ represents the smallest key in subtree $i+1$
3. the root is either a leaf or has between $2$ and $M$ children
4. all non-leaf nodes (except the root) have between $\lceil M/2 \rceil$ and $M$
5. all leaves are at the same depth and have between $\lceil L/2 \rceil$ and $L$ data items
###### example
>[!note]
>Suppose the following :
>- we have 10,000,000 record to fit in the tree
>-  one block holds 8,192 bytes
>-  One Key uses 32 bytes
>-  One record = 256 bytes

1. we would be able to fit $\frac{8,192}{256}=32$ records in a block
2. In a B-tree of order M, we would have M-1 keys. for a total of $32M - 32$ bytes $+\;M$ branches
	- Since each branch is essentially a number of another disk block (suppose 1 branch = 4 bytes)
	- total memory for a non-lead nodes is $36M-32$ bytes; so $M$ will be 228
3. from (1) choose $L$ = 32
In the worst case we could have $\frac{10,000,000}{16}=625,000$ so the leaves will be at level 4

---
### Red-Black Tree

>[!note]
> it has a conceptual advantage that only a constant number of trinode restructurings are ever needed to restore the balance in a red-black tree after an update.

<span class="green">Root Property</span> : The root is black
<span class="green">External Property</span> : Every external node is black
<span class="green">Internal Property</span> : The children of a red node are black
<span class="green">Depth Property</span> : All the external nodes have the same black depth, defined as the number of black ancestors minus one. (Recall that a node is an ancestor of itself)

Height of Red-Black Tree is $O(\log n)​$ :
$$\log(n+1)\le h \le 2\log(n+1)$$
## Correspondence between (2, 4) & Red-Black
### Construct (2, 4) from Red-Black
- By merging every red node into its parent storing the entry from v at its parent
### Construct Red-Black from (2, 4)
- Coloring each node black
- for each internal node **`v`**
    - if **`v`** is a 2-node keep as it is
    - if **`v`** is a 3-node
        1. create a new red node **`w`**
        2. give **`v`**’s first two children to **`w`**
        3. make **`w`** and **`v`**’s third child be the children of **`v`**
    - if **`v`** is a 4-node
        1. create two new red nodes **`w`** and **`z`**
        2. give **`v`**’s first two children to **`w`**
        3. give **`v`**’s last two children to **`z`**
        4. make **`w`** and **`z`** be the two children of **`v`**
## Update Operations
### Insertion Operation
1. as the search terminates unsuccessfully it will be at an external node, replace this node with a new internal node **`z`**
2. if **`z`** is the root color **`z`** black else color **`z`** red. (also color **`z`**’s children black)
3. remedy double red (if **`z`** is not the root and its parent **`v`** is red - _violate Internal Property_ - ) _- 2 cases -_
    - The sibling **`w`** of **`v`** is Black _- malformed replacement for a corresponding 4-node of the (2,4) -_
        - perform trinode restructure at **`z`**
    - The sibling **`w`** of **`v`** is Red _-overflow in the corresponding (2,4)-_ ^c96f37
        - perform recoloring _(eliminate or propagate to u) no. of recolorings caused by insertion is no more than half of height_
            - color **`v`** and **`w`** black and their parent **`u`** red (unless u is the root in which case it is colored black)
### Removal Operation

>[!info]
>- Let v the the node we will remove (internal node which has an external node)
>- Let w an external child node of v
>- Let r be the sibling of w
>- Let x be the parent of v
---
1. consider Removal Operation in The Binary Search Tree to get node v
2. remove nodes v and w (making r a child of x)
3. if v was red or r is red, we color r black and we are done
4. if, instead r is black and v was black then r double black _- underflow -remedy double black problem at r `- 3 cases -`
	- The Sibling y of r is **Black** and Has a **Red** Child z  _- at most one is performed -_ ^d6dd92
		- perform trinode restructuring at z *- corresponds to transfer operation -eliminate-
			1. takes the node z, y, and x, labels them temporarily left to right as a, b, and c
			2. replaces x with the node labeled b making it the parent of the other two
			3. color a, c and r black, give b the former color of x
	- The Sibling y of r is Black and Both Children of y are Black ^2da802
		- perform recoloring *- corresponds to fusion operation -eliminate or propagate to x -*
			1. color r black, color y red
			2. if x is red, color it black otherwise, color x double black
	- The Sibling y of r is Red
		- perform adjustment operation _- at most one is performed -_ _- corresponds choosing a different representation of a 3-node in the (2,4) -_
			1.  If y is the right child of x let z be the right child of y otherwise let z be the left child of y
			2. execute the trinode restructuring operation at z (which makes y the parent of x)
			3. color y black and x red
			4. apply either [Case 1](#^d6dd92) or [Case 2](#^2da802) at r _- Note that if Case 2 applies, the double-black problem cannot reappear -_

----
