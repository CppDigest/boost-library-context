# #1273 - HTTP specializations for non-blocking [Closed]

> Username: vinniefalco  
> Created at: 2018-10-19 21:49:39 UTC  
> Updated at: 2019-02-16 13:56:16 UTC  
> Closed at: 2019-02-16 13:56:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1273  

It is likely possible to provide overloads for asynchronous HTTP read and write algorithms which accept `basic_socket` streams to work in non-blocking mode.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-31 18:17:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1273#issuecomment-434794107  

The implementation can check the socket and see if it is in non-blocking mode, and switch to a non-blocking algorithm.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-16 13:56:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1273#issuecomment-464349127  

This is done, and it turned out to be slightly worse performance-wise, on Windows at least. Code for it is here:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/detail/impl/read.hpp#L253
