# #351 Fix memory leak [Merged]

> Username: jwakely  
> Created at: 2018-10-10 16:17:20 UTC  
> Updated at: 2021-10-02 21:19:30 UTC  
> Merged at: 2018-10-11 00:29:52 UTC  
> Closed at: 2018-10-11 00:29:53 UTC  
> Url: https://github.com/boostorg/build/pull/351  

If vsnprintf returns -1 then the buffer should be freed before returning.

---
