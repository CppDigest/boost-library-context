# #39 Fix gcc/clang shadow warnings. [Merged]

> Username: jhunold  
> Created at: 2015-01-26 11:42:56 UTC  
> Updated at: 2015-01-26 20:19:03 UTC  
> Merged at: 2015-01-26 18:48:53 UTC  
> Closed at: 2015-01-26 18:48:53 UTC  
> Url: https://github.com/boostorg/thread/pull/39  

The variable names are re-used in the all_futures_lock() functions.  
  
```  
.../boost/boost/thread/futures/wait_for_any.hpp:60:58: warning: declaration shadows a field of 'waiter_for_any_in_seq<Future>' [-Wshadow]  
        all_futures_lock(std::vector<registered_waiter>& waiters) :  
```  
  
and  
  
```  
.../boost/boost/thread/future.hpp:880:66: warning: declaration shadows a field of 'boost::detail::future_waiter' [-Wshadow]  
                all_futures_lock(std::vector<registered_waiter>& futures):  
```

---
