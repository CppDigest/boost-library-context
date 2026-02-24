# #53 - Enhance usability for finding k-maximum (minimum) values in set [Open]

> Username: rcollyer  
> Created at: 2025-06-20 21:21:59 UTC  
> Updated at: 2025-06-20 21:21:59 UTC  
> Url: https://github.com/boostorg/heap/issues/53  

My chief use case for a heap is finding the k-maximum (minimum) members of a set, but the update methods (`update`, `increase`, and `decrease`) cannot be easily used for this because they take a `handle_type` which cannot be easily acquired for the top element. Using `heap::ordered_begin()` does not work because there is no direct route from an `ordered_iterator` to a `handle` like in `s_handle_from_iterator`.   
  
I propose   
  
1. overloading `s_handle_from_iterator` to accept `ordered_iterator`,  
2. providing access to the top element as a handle, e.g. `handle_type top_element_handle();`, and  
3. possibly adding update methods that act directly on the top element, e.g. `void update_top(const_reference)`, etc.
