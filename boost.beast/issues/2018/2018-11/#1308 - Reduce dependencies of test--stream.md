# #1308 - Reduce dependencies of test::stream [Closed]

> Username: djarek  
> Created at: 2018-11-21 00:05:10 UTC  
> Updated at: 2018-11-27 16:35:03 UTC  
> Closed at: 2018-11-27 16:35:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1308  

`test::stream` has a dependency on a small part of websockets and also transitively pulls in `tcp.hpp` from ASIO. I believe this can be resolved with forward declarations.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-21 01:15:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1308#issuecomment-440491814  

teardown.hpp is pretty harmless except for the tcp.hpp part

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-11-21 01:21:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1308#issuecomment-440492911  

I think we're allowed to forward declare stuff from other boost libs, after all they are frozen at the same version so the source is predictable.
