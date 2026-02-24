# #39 Fix parameter type of uninitialized_copy_alloc_n_source() [Closed]

> Username: togermer  
> Created at: 2016-10-18 12:04:09 UTC  
> Updated at: 2016-10-19 12:20:59 UTC  
> Closed at: 2016-10-18 15:01:26 UTC  
> Url: https://github.com/boostorg/container/pull/39  

This function constructs n objects using Allocator, so the number of objects should be given as the (unsigned) allocator_traits<Allocator>::size_type. There are two callers, both of which already use size_type. However, the function signature did not reflect this yet. This fix makes the function signature compatible with the callers.

---
