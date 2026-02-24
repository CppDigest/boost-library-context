# #239 Fixed lockable_traits bugs [Closed]

> Username: Kojoley  
> Created at: 2018-10-04 13:52:39 UTC  
> Updated at: 2018-10-04 14:06:20 UTC  
> Closed at: 2018-10-04 14:06:19 UTC  
> Url: https://github.com/boostorg/thread/pull/239  

On MSVC 14.1 with -std=c++17 a `boost::lock(mutex)` does not compile because  
`boost::is_mutex_type<boost::mutex>::value` is false.

---
