# #71 - alignment change in 1.77 beta breaks VS 2017 builds [Open]

> Username: thebrandre  
> Created at: 2021-07-15 08:27:26 UTC  
> Updated at: 2021-07-16 10:12:33 UTC  
> Url: https://github.com/boostorg/lockfree/issues/71  

The underlying issue with the VS2017 compiler is shown [here on godbolt](https://godbolt.org/z/Mq6TrcEGf).  
  
The error was triggered in our code base (which works fine with boost-1.76) where we have boost::lockfree::queue in classes managed by shared_ptr. It is probably due to [this commit](https://github.com/boostorg/lockfree/commit/9144954d247024db8b21da0bca3fa7f20edd4fcc).  
  
Note that `boost::lockfree::queue<int, boost::lockfree::capacity<N>>`, [which is already pretty large](https://godbolt.org/z/dbzKoYdeb), increases by 60 bytes.  
  
Of course, there is an easy fix by defining `_ENABLE_EXTENDED_ALIGNED_STORAGE`, but there is a reason they did not enable this by default.  
  
This issue is obviously not a deal breaker but it will make upgrading boost from 1.76 to 1.77 troublesome for many users.

---

## Comment 1

> Username: timblechmann  
> Created at: 2021-07-15 08:54:17 UTC  
> Url: https://github.com/boostorg/lockfree/issues/71#issuecomment-880520689  

> but there is a reason they did not enable this by default.  
  
afaict they did not enable this by default, as it's a subtle change to the ABI.   
  
----  
  
i'd rather not change the code, though: it addressed an ubsan error

---

## Comment 2

> Username: thebrandre  
> Created at: 2021-07-16 09:55:46 UTC  
> Url: https://github.com/boostorg/lockfree/issues/71#issuecomment-881326311  

However ...  
* it is a breaking change for VS2017 users because you enforce setting `_ENABLE_EXTENDED_ALIGNED_STORAGE` globally  
* it has a performance impact for all users due to the stack size increase  
  
I am not sure if this is worth it.  
  
Did you make sure that both changes are needed for ubsan compatibility? Only  
```cpp  
struct BOOST_ALIGNMENT(BOOST_LOCKFREE_CACHELINE_BYTES) compiletime_sized_freelist_storage  
```  
causes problems.  
  
Replacing `std::allocator<T>` by `boost::alignment::aligned_allocator<T, BOOST_LOCKFREE_CACHELINE_BYTES>` works fine ... (which is a shame because this one could be resolved more easily).  
  
Can you maybe add a hint to the release notes? Because this issue is rather hard to track. The error message of VS2017 will point out only the struct/class that contains the boost::lockfree::queue and is created via `std::make_shared`.

---

## Comment 3

> Username: timblechmann  
> Created at: 2021-07-16 10:12:33 UTC  
> Url: https://github.com/boostorg/lockfree/issues/71#issuecomment-881336903  

> it has a performance impact for all users due to the stack size increase  
  
boost.lockfree structs use deliberate padding to prevent false sharing.  
  
> Did you make sure that both changes are needed for ubsan compatibility?  
  
iirc one change was needed for compile-time sizeing the other for run-time sizing.
