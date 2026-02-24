# #74 - std::is_trivially_copyable asserts false in GCC 4.8 [Closed]

> Username: alandefreitas  
> Created at: 2025-06-18 03:05:05 UTC  
> Updated at: 2025-06-18 16:36:34 UTC  
> Closed at: 2025-06-18 16:35:20 UTC  
> Url: https://github.com/boostorg/atomic/issues/74  

This new assertion from [a commit last week](https://github.com/boostorg/atomic/commit/3b4c10be1fba7131bd6222b5008f3cfd7e6454bb) now fails to assert in GCC 4.8 in CI for Boost.URL:  
  
https://github.com/boostorg/atomic/blob/0a147518327e82e16b2d1bfce143993fa63640f6/include/boost/atomic/detail/bitwise_cast.hpp#L86  
  
```  
  In file included from /__w/url/boost-root/libs/atomic/include/boost/atomic/detail/integral_conversions.hpp:19:0,  
                   from /__w/url/boost-root/libs/atomic/include/boost/atomic/detail/extra_ops_generic.hpp:21,  
                   from /__w/url/boost-root/libs/atomic/include/boost/atomic/detail/extra_operations.hpp:17,  
                   from /__w/url/boost-root/libs/atomic/src/lock_pool.cpp:40:  
  /__w/url/boost-root/libs/atomic/include/boost/atomic/detail/bitwise_cast.hpp: In function 'To boost::atomics::detail::bitwise_cast_memcpy(const From&)':  
  /__w/url/boost-root/libs/atomic/include/boost/atomic/detail/bitwise_cast.hpp:86:19: error: 'is_trivially_copyable' is not a member of 'std'  
       static_assert(std::is_trivially_copyable< unqualified_to_t >::value, "bitwise_cast target type must be trivially copyable");  
                     ^  
  /__w/url/boost-root/libs/atomic/include/boost/atomic/detail/bitwise_cast.hpp:86:64: error: expected primary-expression before '>' token  
       static_assert(std::is_trivially_copyable< unqualified_to_t >::value, "bitwise_cast target type must be trivially copyable");  
                                                                  ^  
  /__w/url/boost-root/libs/atomic/include/boost/atomic/detail/bitwise_cast.hpp:86:65: error: '::value' has not been declared  
       static_assert(std::is_trivially_copyable< unqualified_to_t >::value, "bitwise_cast target type must be trivially copyable");  
                                                                   ^  
  /__w/url/boost-root/libs/atomic/include/boost/atomic/detail/bitwise_cast.hpp:87:19: error: 'is_trivially_copyable' is not a member of 'std'  
       static_assert(std::is_trivially_copyable< From >::value, "bitwise_cast source type must be trivially copyable");  
                     ^  
  /__w/url/boost-root/libs/atomic/include/boost/atomic/detail/bitwise_cast.hpp:87:52: error: expected primary-expression before '>' token  
       static_assert(std::is_trivially_copyable< From >::value, "bitwise_cast source type must be trivially copyable");  
                                                      ^  
  /__w/url/boost-root/libs/atomic/include/boost/atomic/detail/bitwise_cast.hpp:87:53: error: '::value' has not been declared  
       static_assert(std::is_trivially_copyable< From >::value, "bitwise_cast source type must be trivially copyable");                                                  ^  
```  
  
Source:  
  
https://github.com/boostorg/url/actions/runs/15722459227/job/44306004045?pr=914#step:10:6929

---

## Comment 1

> Username: Lastique  
> Created at: 2025-06-18 08:53:18 UTC  
> Updated at: 2025-06-18 08:57:23 UTC  
> Url: https://github.com/boostorg/atomic/issues/74#issuecomment-2983291811  

Yes, a more complete C++11 support is now required by Boost.Atomic. gcc 5 is the minimum tested version.

---

## Comment 2

> Username: Lastique  
> Created at: 2025-06-18 16:36:34 UTC  
> Url: https://github.com/boostorg/atomic/issues/74#issuecomment-2984953173  

I have added workarounds for compatibility with gcc 4.8 and 4.9 for now, but expect those compilers to be dropped sooner rather than later.
