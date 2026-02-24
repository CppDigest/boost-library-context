# #210 - Use sized delete in boost::container::new_allocator if __cpp_sized_deallocation is defined [Closed]

> Username: marksantaniello  
> Created at: 2022-01-31 02:27:37 UTC  
> Updated at: 2022-07-16 18:57:37 UTC  
> Closed at: 2022-07-16 18:55:49 UTC  
> Url: https://github.com/boostorg/container/issues/210  

Boost container's new_allocator::deallocate ignores the size_type argument:  
https://github.com/boostorg/container/blob/develop/include/boost/container/new_allocator.hpp#L165-L166  
  
If __cpp_sized_deallocation is defined, it would be nice to pass the size to operator delete, as done by libstdc++'s equivalent:  
https://github.com/gcc-mirror/gcc/blob/16e2427f50c208dfe07d07f18009969502c25dc8/libstdc%2B%2B-v3/include/ext/new_allocator.h#L132-L134  
  
As we argued back in 2019, this can reduce the cost of deallocation by up to 40%.  
https://gcc.gnu.org/legacy-ml/libstdc++/2019-02/msg00095.html  
  
High-performance malloc implementations (jemalloc, tcmalloc) divide allocations into "size classes", and would otherwise require an address-to-size-class lookup at deallocation time.  By reminding the allocator of the originally-allocated size, we can avoid the cost of this metadata lookup.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-07-16 18:57:37 UTC  
> Url: https://github.com/boostorg/container/issues/210#issuecomment-1186266124  

Many thanks for the report!
