# #232 Fix using pmr::polymorphic_allocator in pre-main [Merged]

> Username: georgthegreat  
> Created at: 2022-10-01 09:24:58 UTC  
> Updated at: 2022-12-11 17:45:42 UTC  
> Merged at: 2022-12-11 17:45:37 UTC  
> Closed at: 2022-12-11 17:45:37 UTC  
> Url: https://github.com/boostorg/container/pull/232  

A similar problems for the above (i. e. non-atomic) branch was addressed in #148, yet the default C++11 branch causes segfault with the following stacktrace:  
  
```  
(gdb) bt  
#0  0x000000000e1c3d28 in boost::container::pmr::memory_resource::allocate (this=0x0, bytes=92, alignment=4)  
    at boost/container/include/boost/container/pmr/memory_resource.hpp:48  
#1  0x000000000e2e9e59 in boost::container::pmr::polymorphic_allocator<float>::allocate (this=0x7fffffffd148, n=23)  
    at boost/container/include/boost/container/pmr/polymorphic_allocator.hpp:92  
#2  0x000000000e2e9cdd in std::__y1::allocator_traits<boost::container::pmr::polymorphic_allocator<float> >::allocate (__a=..., __n=23)  
    at libcxx/include/__memory/allocator_traits.h:262  
#3  0x000000000e2e9b13 in std::__y1::vector<float, boost::container::pmr::polymorphic_allocator<float> >::__vallocate (this=0x7fffffffd130, __n=23)  
    at libcxx/include/vector:958  
#4  0x000000000e33c591 in std::__y1::vector<float, boost::container::pmr::polymorphic_allocator<float> >::vector (this=0x7fffffffd130, __n=23)  
    at libcxx/include/vector:1106  
```

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2022-12-11 17:45:42 UTC  
> Url: https://github.com/boostorg/container/pull/232#issuecomment-1345614957  

Many thanks for the patch!

---
