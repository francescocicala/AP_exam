# Description                                                                                         {#mainpage}

For this project we are implementing a template binary search tree (BST).

Inside the documentation you will find:

* Design choices and rationale
* Assigned specifications
* Bibliography



---
# Specifications                        

## What you have to submit

You have to upload only and all the source files you wrote, with a Makefile and a readme.md file where you describe how to compile, run your code and a short report on what you have done and understood.

your code should have no memory leaks. You can check running

    `$ valgrind ./a.out ...`

where the ... means possible additional command line arguments, if any.

your code must be compiled with the flags `-Wall -Wextra` and no warnings must appear

## Binary search tree

In this exam you are required to implement a template binary search tree (BST). A BST, is a hierarchical (ordered) data structure where each node can have at most two children, namely, left and right child. Each node stores a pair of a key and the associated value. The binary tree is ordered according to the keys. Given a node N, all the nodes having keys smaller than the key of the node N can be found going left. While all the nodes with a key greater than the key of the node N can be reached going right.

<img src="../binary.png">

Practically speaking, given the binary tree in the picture, if you need to insert a new node with key=5, you start from the root node 8, you go left since 5<8, you reach node 3, then you go right, you land in 6, you go left reaching node 4. Node 4 has no right child, so the new node 5 will be the right child of node 4. If a key is already present in the tree, you can choose if replace the value with the newest one, or leave the things as they are.

From the implementation point of view, your node has two pointers left and right pointing to the left and right child respectively. The pointers points to nullptr if they have no children.

It is useful to add an additional pointer (head, root, whatever you like) pointing to the first node, called root node.
Tree traversal

The tree must be traversed in order, i.e., if I "print" the tree in the picture, I expect to see on the screen the sequence

`1 3 4 6 7 8 10 13 14`

node 1 is the first node, and node 14 is the last one.
Assignments

You have to solve the following tasks in C++11 (C++14 and 17 are welcomed as well).

- [ ] implement a template binary search tree
- [ ] it must be templated on the type of the key and the type of the value associated with it.
- [ ] optional you can add a third template on the operation used to compare two different keys.
- [ ] implement proper iterators for your tree (i.e., iterator and const_iterator)
- [ ] the tree must have at least the following public member function
- [ ] insert, used to insert a new pair key-value.
- [ ] clear(), clear the content of the tree.
- [ ] begin(), return an iterator to the first node (which likely will not be the root node)
- [ ] end(), return a proper iterator
- [ ] cbegin(), return a const_iterator to the first node
- [ ] cend(), return a proper const_iterator
- [ ] balance(), balance the tree.
- [ ] find, find a given key and return an iterator to that node. If the key is not found returns end();
- [ ] optional implement the value_type& operator[](`const key_type& k`) function int the const and non-const versions). This functions, should return a reference to the value associated to the key k. If the key is not present, a new node with key k is allocated having the value value_type{}.
- [ ] implement copy and move semantics for the tree.
- [ ] override the operator put to << in order to print (in order) key: value of all the nodes in the tree.

* Test the performance of the lookups (using the function find) before and after the tree is re-balanced. Use proper numbers (and types) of nodes and look-ups. Does lookup behaves as O(log N)? How your tree compares with std::map? make plots

- [x] optional document the code with Doxygen

- [ ] write a short report

- [ ] test everything

## Hints

* you can use `std::pair<const key_type,value_type>` found in the header utility
* use recursive functions
* big hint start coding and using the iterators ASAP.


---


# Bibliography

<a href="../bibliography/Implementation_of_BT_via_Smart_Pointers.pdf" target="_blank"><b>Donchev et al., "Implementation of Binary Search Trees via Smart Pointers", IJACSA, 2015,6,3</b></a>

<a href="../bibliography/C++11_smart_ptrs.pdf" target ="_blank"><b>David Kieras, University of Michigan, "Use of C++11's Smart Pointers", 2016</b></a> 

---

# Design Choices

Binary tree nodes are defined by three attributes: the data they contain, and two pointers to left and right child nodes.

The project will define a `Node` Class, with these attributes, and the required methods.

A `BLT` class will be needed too, pointing to the first Node (root node), and including for all the properties that a BLT has, but a Node doesn't.

Pointers can often be problematic elements: in particular, we have to take care about potential problems during node deletion: if our code is not totally safe, we will leave behind orphan nodes, rising memory leaks and dangling references.

Luckily with C++11, smart pointers are available, included in the `<memory>` header.

Smart pointers are special RAII modeled Classes that behave like raw pointers, but also *manage* objects created with `new` , allowing us not to worry about when or wheter we delete them, while providing a similar interface ( \*, -> ). Smart pointers contains a built-in raw pointers, and are defined as template class, whose type is the one of the object they point to. Over this, smart pointers add the concept of "owning" an object. We took into consideration two different types of smart pointers, unique and shared. Moreover, they guarantee that there will be no multiple deletions of the same pointer. Lastly, by not allowing null-pointer dereference, they will keep us safe from accidental dereferencing of missing child nodes.

The `unique_ptr<>` template holds a pointer to an object and deletes this object when the unique_ptr<> object is deleted.

The `shared_ptr<>` template instead can be owned by more than one owner, and will keep track internally of their number, deleting the object and freeing the memory when the number of owners will be zero. When using shared pointers we have to be sure that no circular references is created.

Given the possibility of using move semantics, the decision fell on unique pointers, in a first instance. If copy semantics will be needed we should be able to quickly switch to shared pointers.

In the requirements, both the key and the value of each node must be templated: we will be using `template <class K>` for the keys and `template <class T>` for the values.

  