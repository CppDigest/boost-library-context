# #14 Node checkers [Merged]

> Username: mateidavid  
> Created at: 2014-08-14 20:33:15 UTC  
> Updated at: 2014-08-29 21:51:29 UTC  
> Merged at: 2014-08-29 21:51:28 UTC  
> Closed at: 2014-08-29 21:51:29 UTC  
> Url: https://github.com/boostorg/intrusive/pull/14  

My modifications consist of adding a `void check() const` public method to all containers: (s)lists, hashtables, and trees. The method traverses the containers and asserts various things about their integrity. With `NDEBUG`, the asserts would be off, but the code would still do the traversing. Here are the details:  
  
For (s)lists, the method is inside `(s)list_impl`. It checks that the pointers look as they should: for `circular`, they have to go back to the header; for `linear`, they should end in null. It also checks the size if `constant_time_size`, and the last node if `cache_last` for slists.  
  
For hashtables, the method is empty. I only put it in so that I can add a call to `check()` in the tests suite.  
  
For trees, I have a more elaborate scheme which allows checkers to be combined. A Checker type must have:  
- A type `Checker::return_type`. When checking a given node `p`, an object of this type is propagated up the tree, and can be used to pass up information to facilitate further checking.  
- A public method `void Checker::operator()(const const_node_ptr&, const return_type&, const return_type&,  
  return_type&)`. When this is called at a node `p`, the 2nd&3rd arguments are the return values from the left&right subtree of `p`, and the 4th is what the check at `p` should return.  
  
The main public `check()` method is inside `bstree_impl`. The code there takes an optional additional checker of type `ExtraChecker`. The code constructs a tree checker of type `get_node_checker<AlgoType, ValueTraits, NodePtrComp, ExtraChecker>::type(nodeptr_comp, extra_checker)`. Here, `nodeptr_comp` is a node pointer comparator (using the tree's comparator). Different algorithm types provide different specializations of `get_node_checker`. The method then invokes the checker implementation inside `bstree_algorithms.hpp`.  
  
The implementation in `bstree_algorithms.hpp` traverses the tree in post-order: left(p), right(p), then p. When checking the left and right subtrees of a node `p`, two values of type `Checker::return_type` are obtained from below. To check the node `p`, the code invokes `checker(p, return_val_left, return_val_right, return_val)`.  
  
I implemented the following checkers:  
  
`bstree_node_checker`:  
  `return_type`:  
    `const_node_ptr` to min&max key in subtree  
    `size_t` node count  
  effect:  
    checks tree pointers (e.g. `parent(left(p))==p`)  
    checks key ordering  
    checks left&right pointers of header  
    Note: if `constant_time_size`, the `bstree_impl::check()` method also checks the size of the tree.  
  
`rbtree_node_checker: public bstree_node_checker`  
  `return_type: public bstree_node_checker::return_type`  
  effect:  
    checks: if `p` is red, neither of its children is red  
  
`avltree_node_checker: public bstree_node_checker`  
  `return_type: public bstree_node_checker::return_type`, plus  
    `int` height  
  effect:  
    check height difference equals the balance  
  
All of the above take an extra `Checker` as template argument, and use it as base. So they do their checks, then invoke the base check. `rbtree_node_checker` and `avltree_node_checker` simply pass this on to their `bstree_node_checker` base.  
  
For `splaytree` & `sgtree`, the `bstree_node_checker` is used (i.e., no other checks).  
  
For `treap`, I also implemented priority checks. The small complication is that the `bstree_impl` base of a `treap_impl` does not know about priorities. So the priority checks are implemented as `treap_node_extra_checker` that (unlike rb&avl) doesn't have a `bstree_node_checker base`. Instead, `treap_impl` contains its own `check()` method. This one invokes `tree_base::checker()` by passing it an extra checker of type `treap_node_extra_checker`.  
  
As I said, each `check()` method accepts an optional `ExtraChecker` which the user can give to do more checks. If not given, this defaults to `detail::empty_node_checker`. Here is the inheritance summary for the actual node checkers that reach the `bstree_algorithms::check` core method, for the 6 type of trees:  
  
For `bstree`, `splaytree`, `sgtree`:  
  `bstree_node_checker : ExtraChecker`  
  
For `rbtree`:  
  `rbtree_node_checker : bstree_node_checker : ExtraChecker`  
  
For `avltree`:  
  `avltree_node_checker : bstree_node_checker : ExtraChecker`  
  
For `treap`:  
  `bstree_node_checker : treap_node_extra_checker : ExtraChecker`

---
