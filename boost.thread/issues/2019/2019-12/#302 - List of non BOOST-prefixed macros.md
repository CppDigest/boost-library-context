# #302 - List of non BOOST-prefixed macros [Open]

> Username: ned14  
> Created at: 2019-12-16 13:57:02 UTC  
> Updated at: 2021-01-26 10:09:48 UTC  
> Url: https://github.com/boostorg/thread/issues/302  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost/thread/win32/thread_heap_alloc.hpp:#ifndef THREAD_HEAP_ALLOC_HPP  
./boost/thread/win32/thread_heap_alloc.hpp:#define THREAD_HEAP_ALLOC_HPP  
./boost/thread/pthread/thread_heap_alloc.hpp:#ifndef THREAD_HEAP_ALLOC_PTHREAD_HPP  
./boost/thread/pthread/thread_heap_alloc.hpp:#define THREAD_HEAP_ALLOC_PTHREAD_HPP  
```

---

## Comment 1

> Username: suvidsahay  
> Created at: 2021-01-26 10:09:47 UTC  
> Url: https://github.com/boostorg/thread/issues/302#issuecomment-767440511  

Well I see no one working on this issue and this would be a simple good first issue as well. Shall I take this?
