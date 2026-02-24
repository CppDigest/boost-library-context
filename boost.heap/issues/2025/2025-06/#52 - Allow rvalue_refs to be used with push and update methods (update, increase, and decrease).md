# #52 - Allow rvalue_refs to be used with push and update methods (update, increase, and decrease) [Open]

> Username: rcollyer  
> Created at: 2025-06-20 21:06:38 UTC  
> Updated at: 2025-06-20 21:06:38 UTC  
> Url: https://github.com/boostorg/heap/issues/52  

Currently, only `emplace` has move semantics with regards to adding or updating the values in any of the heaps. This is limiting when the `value_type` is move only, especially as there is no workaround for the update methods. (`emplace` will invoke the move constructor, so it can be used as work around for `push`.) I propose adding overloads for the adding or updating methods to allow for values with move semantics to be used, e.g.  
  
`handle_type push(value_type&&);`  
`void update(handle_type, value_type&&);`  
`void increase(handle_type, value_type&&);`  
`void decrease(handle_type, value_type&&);`  
`void update_lazy(handle_type, value_type&&); // fibonacci_heap only`  
  
A similar issue exists in #23 for dealing with the comparison function.
