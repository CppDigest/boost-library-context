# #66 - differering destructor behavior with compile-time size [Closed]

> Username: PatrickAMoran  
> Created at: 2021-03-15 15:17:28 UTC  
> Updated at: 2021-03-25 07:06:11 UTC  
> Closed at: 2021-03-25 07:05:29 UTC  
> Url: https://github.com/boostorg/lockfree/issues/66  

I noticed something I found surprising - a boost::lockfree:spsc_queue<T> behaves differently on destruction if you give it a compile-time size vs giving it a runtime size.  When you have a runtime-sized queue, then any elements still in the queue are destructed when the queue destructs (which is how I usually expect containers to behave). That is handled [here](https://github.com/boostorg/lockfree/blob/c4ece99fbe3102a95b2d7a0a863b5dce58674914/include/boost/lockfree/spsc_queue.hpp#L576). But `compile_time_sized_ringbuffer` doesn't have a destructor declared, and it's storage is just a `boost::aligned_storage`, so it won't destruct any stored objects either.  
  
My assumption is that this is not intentional, and that we expect the remaining elements of the queue to be destructed when destroying a compile-time-sized spsc_queue?

---

## Comment 1

> Username: timblechmann  
> Created at: 2021-03-25 07:06:11 UTC  
> Url: https://github.com/boostorg/lockfree/issues/66#issuecomment-806416011  

thanks for flagging this. pushed a fix
