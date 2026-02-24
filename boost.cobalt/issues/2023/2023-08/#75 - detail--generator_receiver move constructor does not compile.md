# #75 - detail::generator_receiver move constructor does not compile [Closed]

> Username: anarthal  
> Created at: 2023-08-15 12:28:13 UTC  
> Updated at: 2023-08-31 03:37:35 UTC  
> Closed at: 2023-08-31 03:37:35 UTC  
> Url: https://github.com/boostorg/cobalt/issues/75  

`awaited_from(lhs.awaited_from)` is trying to copy-construct a unique_ptr
