# #1393 - free functions should not be static [Closed]

> Username: vinniefalco  
> Created at: 2018-12-28 01:30:46 UTC  
> Updated at: 2019-01-15 20:31:27 UTC  
> Closed at: 2019-01-15 20:31:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1393  

https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/impl/read.ipp#L35  
  
Maybe also change the `constexpr` constant to not be static
