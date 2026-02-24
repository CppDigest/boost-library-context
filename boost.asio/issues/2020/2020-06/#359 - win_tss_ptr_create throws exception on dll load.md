# #359 - win_tss_ptr_create throws exception on dll load [Closed]

> Username: juergen3ds  
> Created at: 2020-06-17 13:21:39 UTC  
> Updated at: 2020-12-30 01:14:31 UTC  
> Closed at: 2020-12-30 01:14:30 UTC  
> Url: https://github.com/boostorg/asio/issues/359  

On dynamic dll loading the initialization of static  variables in win_global_impl calls win_tss_ptr_create which calls TlsAlloc. TlsAlloc returns TLS_OUT_OF_INDEXES and then an error is thrown and dll load fails.  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/win_global.hpp  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/impl/win_tss_ptr.ipp  
  
TlsAlloc fails, because the maximum number of indexes per process is 1,088.  
https://docs.microsoft.com/en-us/windows/win32/procthread/thread-local-storage  
  
Is it required that TlsAlloc is called already on initialization of a dll? Is there an option to disable this?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:14:29 UTC  
> Url: https://github.com/boostorg/asio/issues/359#issuecomment-752293679  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#764](https://github.com/chriskohlhoff/asio/issues/764).
