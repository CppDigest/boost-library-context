# #328 - `boost::container::deque` stores a redundant copy of the allocator, increasing size [Closed]

> Username: glebov-andrey  
> Created at: 2026-01-14 13:08:13 UTC  
> Updated at: 2026-01-21 12:43:34 UTC  
> Closed at: 2026-01-21 12:43:34 UTC  
> Url: https://github.com/boostorg/container/issues/328  

Hi,  
  
With the `1.90.0` release, `deque` stores two copies of effectively the same allocator, just for two different `value_type`s - `T *` and `T`:  
https://github.com/boostorg/container/blob/f818ee9bfd66c3c5c355bb7e3cc3394be7f8618c/include/boost/container/deque.hpp#L716-L718  
  
Because of this, the size of the object increases, especially if the allocator is stateful, e.g. `std::pmr::polymorphic_allocator`.  
  
Even with an empty allocator, it still increases the size for some ABIs, e.g. the Microsoft x86_64 ABI (MSVC and Clang). This is presumably due to different empty-base-optimization rules in different ABIs.  
  
It should be possible to always store a single allocator, e.g. `allocator_type`, and copy-construct `ptr_alloc_t` from it only when needed. For any reasonable allocator (i.e. small and trivially copyable) this should be a zero-overhead operation for optimized builds.  
  
`sizeof` results for reference:  
Windows, x86_64, MSVC or Clang:  
```  
sizeof(boost::container::deque<int>)                                       = 40  
sizeof(boost::container::deque<int, std::allocator<int>>)                  = 40  
sizeof(boost::container::deque<int, std::pmr::polymorphic_allocator<int>>) = 48  
```  
Linux, x86_64, GCC or Clang:  
```  
sizeof(boost::container::deque<int>)                                       = 32  
sizeof(boost::container::deque<int, std::allocator<int>>)                  = 32  
sizeof(boost::container::deque<int, std::pmr::polymorphic_allocator<int>>) = 48  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-21 12:43:21 UTC  
> Url: https://github.com/boostorg/container/issues/328#issuecomment-3777944589  

Thanks. I think the original implementation also stored the second allocator (and I think libc++ also stores does this). In any case, I agree that this optimization is desiable. In principal, we could have pathological allocators where the copy could be expensive, but since the PMR usage is undoubtely more popular, we should optimize the common case.
