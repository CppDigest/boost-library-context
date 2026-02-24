# #397 - sync_queue missing include when setting BOOST_THREAD_DONT_PROVIDE_INTERRUPTIONS [Open]

> Username: dvirtz  
> Created at: 2023-10-26 10:57:10 UTC  
> Updated at: 2023-10-26 10:57:10 UTC  
> Url: https://github.com/boostorg/thread/issues/397  

```  
In file included from /opt/compiler-explorer/libs/boost_1_83_0/boost/thread/sync_queue.hpp:14:  
In file included from /opt/compiler-explorer/libs/boost_1_83_0/boost/thread/concurrent_queues/sync_queue.hpp:15:  
/opt/compiler-explorer/libs/boost_1_83_0/boost/thread/concurrent_queues/detail/sync_queue_base.hpp:74:23: error: no template named 'lock_guard'; did you mean 'std::lock_guard'?  
   74 |     inline bool empty(lock_guard<mutex>& ) const BOOST_NOEXCEPT  
      |                       ^  
```  
https://godbolt.org/z/185hWToY9
