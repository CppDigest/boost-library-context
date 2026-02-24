# #132 - vector::swap fails to swap the allocators [Closed]

> Username: bmerry  
> Created at: 2014-06-06 13:28:08 UTC  
> Updated at: 2014-06-06 15:31:23 UTC  
> Closed at: 2014-06-06 15:31:23 UTC  
> Url: https://github.com/boostorg/compute/issues/132  

I am trying to swap two vectors with different contexts, using `boost::compute::vector::swap`. Specifically, I am trying to defer initialization of a vector stored in a class, so I am letting it be default-constructed, then trying to swap it with a new vector created in a non-default context. I am later getting the assertion below, which I'm guessing is because the swap function does not swap the allocator instances, and hence deallocation is in the wrong context.  
  
```  
include/boost/compute/container/allocator.hpp:66: void boost::compute::allocator<T>::deallocate(boost::compute::allocator<T>::pointer, boost::compute::allocator<T>::size_type) [with T = int; boost::compute::allocator<T>::pointer = boost::compute::device_ptr<int>; boost::compute::allocator<T>::size_type = long unsigned int]: Assertion `p.get_buffer().get_context() == m_context' failed.  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-06-06 15:31:20 UTC  
> Url: https://github.com/boostorg/compute/issues/132#issuecomment-45350028  

Thanks for reporting this!  
  
Fixed in 2ca2c16839942a5c4e36ae230472dbfa3cf5307a (currently in `develop`, should be merged to `master` this week).
