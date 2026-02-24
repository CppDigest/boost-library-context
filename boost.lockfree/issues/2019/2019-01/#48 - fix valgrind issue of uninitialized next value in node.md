# #48 - fix valgrind issue of uninitialized next value in node [Closed]

> Username: yuvalif  
> Created at: 2019-01-20 15:00:26 UTC  
> Updated at: 2019-05-15 10:59:42 UTC  
> Closed at: 2019-05-15 10:59:41 UTC  
> Url: https://github.com/boostorg/lockfree/issues/48  

The following two errors appear in all queue related tests when running under valgrind (valgrind-3.14.0).  
valgrind output when running the queue example binary:  
```  
==29902== Conditional jump or move depends on uninitialised value(s)  
==29902==    at 0x415273: std::atomic<boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node> >::compare_exchange_weak(boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node>&, boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node>, std::memory_order, std::memory_order) (atomic:288)  
==29902==    by 0x414292: std::atomic<boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node> >::compare_exchange_weak(boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node>&, boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node>, std::memory_order) (atomic:308)  
==29902==    by 0x412C88: bool boost::lockfree::queue<int>::do_push<false>(int const&) (queue.hpp:318)  
==29902==    by 0x41135A: boost::lockfree::queue<int>::push(int const&) (queue.hpp:280)  
==29902==    by 0x40C711: producer() (queue.cpp:27)  
==29902==    by 0x416302: boost::detail::thread_data<void (*)()>::run() (thread.hpp:117)  
==29902==    by 0x4E59475: thread_proxy (thread.cpp:177)  
==29902==    by 0x61E9593: start_thread (in /usr/lib64/libpthread-2.27.so)  
==29902==    by 0x64FAF4E: clone (in /usr/lib64/libc-2.27.so)  
==29902==   
==29902== Conditional jump or move depends on uninitialised value(s)  
==29902==    at 0x412C8B: bool boost::lockfree::queue<int>::do_push<false>(int const&) (queue.hpp:318)  
==29902==    by 0x41135A: boost::lockfree::queue<int>::push(int const&) (queue.hpp:280)  
==29902==    by 0x40C711: producer() (queue.cpp:27)  
==29902==    by 0x416302: boost::detail::thread_data<void (*)()>::run() (thread.hpp:117)  
==29902==    by 0x4E59475: thread_proxy (thread.cpp:177)  
==29902==    by 0x61E9593: start_thread (in /usr/lib64/libpthread-2.27.so)  
==29902==    by 0x64FAF4E: clone (in /usr/lib64/libc-2.27.so)  
```  
  
As I understand, this is not a real issue, since it does not matter which tag we use at the beginning, as long as we increment it, however, it does create noise when running with valgrind, and does not seem to have any cost in perf.  
Also, the fix is simple, as it could be done by uncommenting existing code in the ctor of the node class in queue.hpp
