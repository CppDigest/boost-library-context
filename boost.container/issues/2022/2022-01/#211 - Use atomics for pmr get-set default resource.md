# #211 - Use atomics for pmr get/set default resource [Closed]

> Username: marksantaniello  
> Created at: 2022-01-31 03:47:05 UTC  
> Updated at: 2022-07-25 01:59:25 UTC  
> Closed at: 2022-07-24 23:29:31 UTC  
> Url: https://github.com/boostorg/container/issues/211  

Pmr has the concept of a global "default resource", accessed via get_default_resource/set_default_resource.  In the libstdc++ implementation, this is done with std::atomic:  
https://github.com/gcc-mirror/gcc/blob/master/libstdc%2B%2B-v3/include/experimental/memory_resource#L537-L560  
  
In Boost container we have a global lock:  
https://github.com/boostorg/container/blob/develop/src/global_resource.cpp#L85-L86  
  
The default c'tor for polymorphic_allocator calls get_default_resource, so every default-constructed pmr container will acquire the lock, which is expensive:  
https://github.com/boostorg/container/blob/b7d48f1e086435cc03edb2a96199c1d226d7221c/include/boost/container/pmr/polymorphic_allocator.hpp#L45-L46  
  
It's extremely common /not/ to pass a fancy allocator to a pmr container at construction time.  This is one of the key value propositions of std::pmr -- that it provides "vocabulary types" (such as pmr::vector), which are compile-time compatible and accept fancy allocators on specific instances at runtime.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-07-24 23:29:43 UTC  
> Url: https://github.com/boostorg/container/issues/211#issuecomment-1193416978  

Many thanks for the report!

---

## Comment 2

> Username: marksantaniello  
> Created at: 2022-07-25 01:59:24 UTC  
> Url: https://github.com/boostorg/container/issues/211#issuecomment-1193471955  

Thanks for the fix!
