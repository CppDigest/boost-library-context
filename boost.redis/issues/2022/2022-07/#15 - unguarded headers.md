# #15 - unguarded headers [Closed]

> Username: Eddie-cz  
> Created at: 2022-07-25 10:28:10 UTC  
> Updated at: 2022-07-25 17:11:37 UTC  
> Closed at: 2022-07-25 17:11:37 UTC  
> Url: https://github.com/boostorg/redis/issues/15  

There are still some unguarded headers, like aedis/impl/error.ipp and few others.

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-07-25 16:33:24 UTC  
> Url: https://github.com/boostorg/redis/issues/15#issuecomment-1194325177  

`error.ipp` is an implementation file and not a header. You are probably misusing `#include <aedis/src.hpp>`, it should be include in a single `.cpp` in your application. The easiest way to do this is to include it in the file where you define the `main` function. Alternatively, you can also create a file called `aedis.cpp` with the content  
  
```  
#include <aedis/src.hpp>  
```  
and add it to your build system.

---

## Comment 2

> Username: Eddie-cz  
> Created at: 2022-07-25 17:11:37 UTC  
> Url: https://github.com/boostorg/redis/issues/15#issuecomment-1194368497  

yey, thats is inproper misusing of impl files
