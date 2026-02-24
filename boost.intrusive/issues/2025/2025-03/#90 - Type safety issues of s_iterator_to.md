# #90 - Type safety issues of s_iterator_to [Closed]

> Username: CharlieQiu2017  
> Created at: 2025-03-08 16:44:54 UTC  
> Updated at: 2025-04-19 20:21:35 UTC  
> Closed at: 2025-04-19 20:21:35 UTC  
> Url: https://github.com/boostorg/intrusive/issues/90  

Suppose that class `A` derives from `set_base_hook < >`, and class `B` derives from `A`.  
Now one can create an instance `x` of `B` and insert it into a container `y` (say, `rbtree < B >`).  
Then one can cast `x` into `A &` and use `rbtree < A > :: s_iterator_to` and `rbtree < A > :: container_from_iterator` to obtain a reference to `y` with type `rbtree < A >`, which I think should be undefined behavior, because one is accessing `y` through a different type.  
  
Example:  
```c++  
#include <boost/intrusive/rbtree.hpp>  
  
using boost::intrusive::set_base_hook;  
using boost::intrusive::rbtree;  
  
/* Base class */  
struct uint64_rbnode : public set_base_hook < > {  
  uint64_t data;  
};  
  
constexpr bool operator== (const uint64_rbnode &node1, const uint64_rbnode &node2) {  
  return node1.data == node2.data;  
}  
  
constexpr bool operator> (const uint64_rbnode &node1, const uint64_rbnode &node2) {  
  return node1.data > node2.data;  
}  
  
constexpr bool operator>= (const uint64_rbnode &node1, const uint64_rbnode &node2) {  
  return node1.data >= node2.data;  
}  
  
constexpr bool operator< (const uint64_rbnode &node1, const uint64_rbnode &node2) {  
  return node1.data < node2.data;  
}  
  
constexpr bool operator<= (const uint64_rbnode &node1, const uint64_rbnode &node2) {  
  return node1.data <= node2.data;  
}  
  
/* Derived class */  
struct uint64_rbnode_derived : uint64_rbnode { uint64_t another_data; };  
  
int main () {  
  rbtree < uint64_rbnode_derived > tree; /* Container of derived type */  
  uint64_rbnode_derived node;  
  node.data = 0;  
  tree.push_back (node);  
  auto iter = rbtree < uint64_rbnode > :: s_iterator_to (node); /* Iterator of container of base type */  
  /* Reference to container of derived type, but with type of container of base type */  
  auto & tree_ref = rbtree < uint64_rbnode > :: container_from_iterator (iter);  
  return 0;  
}  
```  
  
The current documentation of `s_iterator_to (value)` just says "value must be a reference to a value inserted in a list."  
I think it should be clarified that `value` must have exactly the same type as the template parameter `T` passed to the container.
