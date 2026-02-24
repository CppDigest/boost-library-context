# #368 - non-virtual-dtor in future_error_category [Open]

> Username: Sil3ntStorm  
> Created at: 2022-04-06 19:25:52 UTC  
> Updated at: 2022-11-15 11:32:52 UTC  
> Url: https://github.com/boostorg/thread/issues/368  

libs/thread/src/future.cpp:19:12: warning: 'boost::thread_detail::future_error_category' has virtual functions but non-virtual destructor [-Wnon-virtual-dtor]  
    class  future_error_category :  
           ^  
1 warning generated.

---

## Comment 1

> Username: cpsauer  
> Created at: 2022-11-15 11:32:37 UTC  
> Updated at: 2022-11-15 11:32:52 UTC  
> Url: https://github.com/boostorg/thread/issues/368#issuecomment-1315182011  

+1  
It's not obvious without more context that there isn't potential for a real issue here.
