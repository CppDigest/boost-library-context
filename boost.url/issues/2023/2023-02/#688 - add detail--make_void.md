# #688 - add detail::make_void [Closed]

> Username: vinniefalco  
> Created at: 2023-02-20 23:25:12 UTC  
> Updated at: 2023-03-01 21:44:52 UTC  
> Closed at: 2023-03-01 21:44:52 UTC  
> Url: https://github.com/boostorg/url/issues/688  

Just add `void_t` to `<boost/url/detail/config.hpp>` as is done here, its a 2 liner:  
  
https://github.com/CPPAlliance/buffers/blob/develop/include/boost/buffers/detail/config.hpp#L69  
  
See if we can remove the dependency on Type Traits after that.
