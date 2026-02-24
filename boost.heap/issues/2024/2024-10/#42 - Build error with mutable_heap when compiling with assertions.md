# #42 - Build error with mutable_heap when compiling with assertions. [Closed]

> Username: phillip-keldenich  
> Created at: 2024-10-10 19:45:41 UTC  
> Updated at: 2024-10-11 09:45:23 UTC  
> Closed at: 2024-10-11 09:45:23 UTC  
> Url: https://github.com/boostorg/heap/issues/42  

The assertions in `boost/heap/detail/mutable_heap.hpp` in in `increase` and in `decrease` cause compilation errors when assertions are enabled and value_compare is not default-constructible, even though everything builds fine without assertions.  
  
The lines in question are:  
  
`BOOST_ASSERT(!value_compare()(v, handle.iterator->first));`  
  
and   
  
`BOOST_ASSERT(!value_compare()(handle.iterator->first, v));`  
  
and can simply be changed to `value_comp()` instead of `value_compare()` (calling a member function, which may have been intended and is just a typo...) to make the container work with non-default-constructible comparison callables.  
I may have missed a requirement that the comparison operator must be default constructible in the documentation, but that would make the container a lot less useful for certain applications where the comparison operator has to access information, e.g., when the queue stores indices into some other data structure.

---

## Comment 1

> Username: phillip-keldenich  
> Created at: 2024-10-11 09:45:23 UTC  
> Url: https://github.com/boostorg/heap/issues/42#issuecomment-2407042766  

Thanks for the quick response - this should be fixed now.
