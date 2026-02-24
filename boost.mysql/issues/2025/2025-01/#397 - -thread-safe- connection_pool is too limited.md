# #397 - "thread-safe" connection_pool is too limited [Closed]

> Username: feverzsj  
> Created at: 2025-01-11 17:42:49 UTC  
> Updated at: 2025-01-11 19:50:47 UTC  
> Closed at: 2025-01-11 19:50:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/397  

According to the document for most methods of connection_pool:  
  
> ... Mutates the pool state. If the pool was built with thread-safety enabled, it can be called concurrently with other functions that don't modify the state handle.  
  
These methods can't be called concurrently, no matter same method or different method or per-operation cancellation. It's not so thread-safe in common sense. Warping everything in strand is also less efficient and hard to use. I'd prefer using mutex for internal states and allowing concurrently calling these methods by default.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-01-11 18:13:36 UTC  
> Url: https://github.com/boostorg/mysql/issues/397#issuecomment-2585362639  

The only methods that mutate the state handle are move construction/assignment and destruction. Note that mutating the state handle is different than mutating the pool's state.  
  
In this context, the pool is implemented as a `std::shared_ptr` to a state type. Mutating the state handle means that the `std::shared_ptr` is mutated, which happens with move operations and destruction. Mutating the state means that the underlying state object is mutated, but the `std::shared_ptr` is not modified.  
  
TL;DR: you can concurrently call almost everything, except for pool move construction/assignment and destruction.  
  
What is your use case?
