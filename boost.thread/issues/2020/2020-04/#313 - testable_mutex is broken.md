# #313 - testable_mutex is broken [Open]

> Username: Lastique  
> Created at: 2020-04-04 20:02:49 UTC  
> Updated at: 2020-04-05 14:10:41 UTC  
> Url: https://github.com/boostorg/thread/issues/313  

Currently, `testable_mutex` fails to compile because of this static assert:  
  
```  
../../../boost/thread/testable_mutex.hpp:39:24:   required from here  
../../../boost/atomic/detail/atomic_template.hpp:1097:83: error: static assertion failed: boost::atomic<T> requires T to be a trivially copyable type  
```  
  
The problem is that `testable_mutex` uses `atomic<thread::id>` internally, and `thread::id` is indeed not trivially copyable. The trivially copyable requirement has always been there, but it was not enforced by Boost.Atomic until not long ago. Before, the code would compile, but user-defined copy constructor would not be called, which means `thread::id` could cause leak of `thread_data` if it used a smart pointer inside.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-05 14:10:41 UTC  
> Url: https://github.com/boostorg/thread/issues/313#issuecomment-609422578  

Since https://github.com/boostorg/thread/pull/310 was merged, the problem is somewhat reduced as `thread::id` is trivially copyable, unless it uses `thread_data_ptr` internally (when `BOOST_THREAD_PROVIDES_BASIC_THREAD_ID` is not defined). Also, if `thread::id` stores `pthread_t` internally, it may be too large for lock-free `atomic` implementation.
