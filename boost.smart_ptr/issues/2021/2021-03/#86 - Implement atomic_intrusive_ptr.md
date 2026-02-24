# #86 - Implement atomic_intrusive_ptr [Closed]

> Username: Lastique  
> Created at: 2021-03-12 11:25:53 UTC  
> Updated at: 2021-03-13 19:18:05 UTC  
> Closed at: 2021-03-13 19:18:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/86  

`atomic_intrusive_ptr` would provide a similar API to `atomic_shared_ptr`, but, obviously, would work with `intrusive_ptr`.  
  
Comes from https://stackoverflow.com/questions/66594589/atomically-sharewithout-a-mutex-boostintrusive-ptr/66598908, and I think I had a similar need in my own code and worked around with a raw atomic.  
  
It seems, it should be fairly straightforward to implement it on top of `std::atomic` or Boost.Atomic. I can prepare a PR if there is interest.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-03-13 19:18:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/86#issuecomment-798770763  

As was discovered in the referenced SO question, there is a problem in an `atomic`-based `load()` implementation, which is the load of the pointer value and incrementing the reference counter are not atomic, and another thread may reset the pointer and destroy the object in between these events. So it looks like an `atomic`-based `atomic_intrusive_ptr` is not possible. I'm not sure a lock-based implementation is worthwhile, so I'll close this ticket. Sorry for the noise.
