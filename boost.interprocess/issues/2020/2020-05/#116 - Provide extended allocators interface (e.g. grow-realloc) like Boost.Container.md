# #116 - Provide extended allocators interface (e.g. grow/realloc) like Boost.Container [Open]

> Username: vinipsmaker  
> Created at: 2020-05-01 05:22:32 UTC  
> Updated at: 2020-05-01 05:22:32 UTC  
> Url: https://github.com/boostorg/interprocess/issues/116  

[Boost.Container allocators support extra interfaces than the standard allocator requirements](https://www.boost.org/doc/libs/1_73_0/doc/html/container/extended_allocators.html).  
  
Some of these functions are useful even to C libraries that used to enjoy `realloc()` like features. So this extra interface is useful even if we're not planning to use it with standard containers.  
  
It'd be nice if Boost.Interprocess could provide a few of these features for its [allocators](https://www.boost.org/doc/libs/1_73_0/doc/html/interprocess/allocators_containers.html) as [`managed_shared_memory`](https://www.boost.org/doc/libs/1_73_0/doc/html/interprocess/managed_memory_segments.html#interprocess.managed_memory_segments.managed_memory_segment_features.allocate_deallocate) has plenty of features not found in Boost.Container allocators and we cannot use Boost.Container extended allocators to manage preallocated memory regions.
