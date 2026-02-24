# #456 - Possible resource leak in `awaitable::operator=` [Open]

> Username: ddvamp  
> Created at: 2025-09-03 05:17:57 UTC  
> Updated at: 2025-09-03 05:33:54 UTC  
> Url: https://github.com/boostorg/asio/issues/456  

https://github.com/boostorg/asio/blob/44722ea0246e001e5fbac6a8b3b8ee153882c83e/include/boost/asio/awaitable.hpp#L81-L86  
  
In the implementation of `awaitable::operator=`, if there is an `awaitable_frame`, it is not destroyed before a new one is assigned. Therefore, resource leak is possible. [Mini example on godbolt](https://godbolt.org/z/qv5Tbxxrb)
