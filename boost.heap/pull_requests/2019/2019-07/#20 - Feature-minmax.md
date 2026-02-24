# #20 Feature/minmax [Open]

> Username: gscano  
> Created at: 2019-07-24 13:48:25 UTC  
> Updated at: 2021-08-27 16:35:57 UTC  
> Url: https://github.com/boostorg/heap/pull/20  

This is an implementation of a min-max heap as described in the paper 'Min-Max Heaps and Generalized Priority Queue' by Atkinson et al..  
Because I extended this data structure to the arity aspect, I based my work on the D-ary heap for the general structure of the class.  
All regular tests as well as stability and mutability tests are integrated.  
  
I just want to point your attention towards the modifications I made, for the sake of iterators (ordered and reverse ordered), to `mutable_heap.hpp` and `ordered_adaptor_iterator.hpp`.  
Because the function `get_child_nodes` had to return more than one range of indexes (as min and max levels are intertwined within the tree) I had to design an `extended_ordered_adaptor_iterator` inheriting from `ordered_adaptor_iterator` and thus created a global `ordered_adaptor_iterator_facade` to avoid code duplication between those two. The same applies to `priority_queue_mutable_wrapper` inner structure `ordered_iterator`.

---
