# #147 - Use BOOST_THROW_EXCEPTION [Closed]

> Username: anarthal  
> Created at: 2023-05-19 10:54:08 UTC  
> Updated at: 2023-05-24 10:19:15 UTC  
> Closed at: 2023-05-24 10:19:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/147  

We're already using it for I/O functions, but some other functions are still using plain `throw`
