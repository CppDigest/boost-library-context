# #657 - constexpr in buffer derived [Closed]

> Username: vinniefalco  
> Created at: 2017-07-16 01:55:06 UTC  
> Updated at: 2017-07-29 07:27:26 UTC  
> Closed at: 2017-07-29 07:27:26 UTC  
> Url: https://github.com/boostorg/beast/issues/657  

`flat_static_buffer` and `static_buffer` can have `constexpr` overloads of `max_size`  
  
`static_string::max_size` could be `constexpr` as well
