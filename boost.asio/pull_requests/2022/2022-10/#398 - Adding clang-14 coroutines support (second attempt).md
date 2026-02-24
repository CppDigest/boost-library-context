# #398 Adding clang-14 coroutines support (second attempt) [Open]

> Username: ramblehead  
> Created at: 2022-10-15 22:28:25 UTC  
> Updated at: 2022-12-21 13:57:35 UTC  
> Url: https://github.com/boostorg/asio/pull/398  

In clang-14 ```<experimental/coroutine>``` is replaced by ```<coroutine>```, thus need to add an additional clang version test macro to set ```BOOST_ASIO_HAS_CO_AWAIT``` correctly.

---

## Comment 1

> Username: raldone01  
> Created_at: 2022-10-16 14:56:09 UTC  
> Url: https://github.com/boostorg/asio/pull/398#issuecomment-1279985813  

You might want to move the pr to https://github.com/chriskohlhoff/asio/pulls.  
It may be more monitored more actively.

---

## Comment 2

> Username: k15tfu  
> Created_at: 2022-12-21 13:57:35 UTC  
> Url: https://github.com/boostorg/asio/pull/398#issuecomment-1361343242  

It looks similar to https://github.com/chriskohlhoff/asio/issues/1138, and https://github.com/chriskohlhoff/asio/pull/1145.

---
