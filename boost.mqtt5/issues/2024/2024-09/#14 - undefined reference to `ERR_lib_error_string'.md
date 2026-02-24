# #14 - undefined reference to `ERR_lib_error_string' [Closed]

> Username: slovak194  
> Created at: 2024-09-24 08:51:31 UTC  
> Updated at: 2024-09-26 05:52:39 UTC  
> Closed at: 2024-09-26 05:52:39 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/14  

Hi!  
  
I was using v.1.0.1 and d52090f for some time with no issues. However, when I tried to run against master - the following linker error appeared:  
  
```  
in function `boost::asio::error::detail::ssl_category::message[abi:cxx11](int) const':  
/usr/include/boost/asio/ssl/impl/error.ipp:39: undefined reference to `ERR_reason_error_string'  
/usr/bin/ld: /usr/include/boost/asio/ssl/impl/error.ipp:42: undefined reference to `ERR_lib_error_string'  
```  
  
Boost:  
-- Found Boost: /usr/lib/x86_64-linux-gnu/cmake/Boost-1.83.0/BoostConfig.cmake (found suitable version "1.83.0", minimum required is "1.82")    
-- Found Boost: /usr/lib/x86_64-linux-gnu/cmake/Boost-1.83.0/BoostConfig.cmake (found version "1.83.0") found components: system   
  
Best regards,  
Alex

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-09-24 13:27:17 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/14#issuecomment-2371276151  

Hi, Alex!  
  
Apologies for the inconvenience.  
  
It turns out that we unintentionally made the OpenSSL library a required dependency with commit 7bc1ccf072edd1c87e6c343383c27606d83e9be3,  
which caused linker errors when the library was not installed.  
  
We have pushed a fix to master, making OpenSSL an optional dependency again.  
This should resolve the linker error you encountered.
