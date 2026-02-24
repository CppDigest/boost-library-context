# #482 - No default value for request/response::version [Closed]

> Username: niklas88  
> Created at: 2017-06-13 07:28:48 UTC  
> Updated at: 2017-06-14 00:38:21 UTC  
> Closed at: 2017-06-14 00:38:21 UTC  
> Url: https://github.com/boostorg/beast/issues/482  

Since version doesn't have a default value it is initialized to, forgetting to set it will result in conditional jumps depending on an uninitialized variable (valgrind detects this). I propose to set it to HTTP/1.1 by default.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-13 12:09:11 UTC  
> Url: https://github.com/boostorg/beast/issues/482#issuecomment-308095661  

will fix
