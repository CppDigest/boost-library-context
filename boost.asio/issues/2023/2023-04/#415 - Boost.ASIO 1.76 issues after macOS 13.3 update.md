# #415 - Boost.ASIO 1.76 issues after macOS 13.3 update [Open]

> Username: timeinpixels  
> Created at: 2023-04-04 09:31:42 UTC  
> Updated at: 2023-04-04 13:45:06 UTC  
> Url: https://github.com/boostorg/asio/issues/415  

Just noticed that after updating to the latest macOS 13.3 my local network connection using Boost.ASIO and tcp sockets no longer works. Do you have any ideas what might be the cause?  
At the same time with the latest Xcode the code no longer compiles in c++20 mode due to some things being removed (deprecated in c++20)  
  
```  
include/boost/asio/impl/co_spawn.hpp:182:22: error: no template named 'result_of'  
[build]     typedef typename result_of<F()>::type awaitable_type;   
```  
  
I was able to resolve the compilation issues with using the latest boost 1.81 and using BOOST_ASIO_HAS_STD_INVOKE_RESULT.  
But I am not sure it's related.   
Any ideas would be greatly appreciated.
